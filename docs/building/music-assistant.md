# Music Assistant

Whole-home audio is the part where a closed system like Control4 earned its keep: one place to pick music, and it plays everywhere you want, in sync. To match that in Home Assistant, you need a **streaming backbone** — and Music Assistant is the strongest answer. This page is about how to think about it, not a feature tour.

## One engine, not one mechanism per service

The trap is wiring each streaming service its own way — one path for this service, a different path for that one, a third for radio. Each behaves differently, breaks differently, and doubles your surface area.

**Route all streaming through one engine instead.** Tracks, playlists, and stations all go through the same mechanism. When something misbehaves, there's one place to look; when you add a service, it slots into a pattern you already understand.

**Why it holds:** this is **one source of truth** ([principles](../framework/principles.md)) applied to audio. A single engine you understand deeply beats five integrations you each half-remember.

## One play target that fans out

Designate a **single play target** that receives your streaming commands. Behind it, the engine distributes to the individual zone players (and a receiver path for the rooms that need it). You command *one* entity; it handles the fan-out to many.

```yaml
# Illustrative — play a station on the single streaming target; it fans out to zones.
- action: music_assistant.play_media
  target:
    entity_id: media_player.home_stream    # the one play target, not a per-zone player
  data:
    media_type: radio                      # stations need the radio type, not a track type
    media_id: "Jazz"                       # a station name (substring) or a URI
```

The mistake here is pointing commands at an individual zone player when the design wants the single fan-out target. Pick the target deliberately and send everything there.

## Match the media type to the content

Different content kinds need different handling, and getting this wrong is the most common dead end:

- A **station / radio stream** is a different `media_type` than a track or playlist. Ask for a station as if it were a track and you'll get errors or silence.
- A **track or playlist** is identified by a URI or a search string.

**Why it holds:** the engine routes on the type you declare. Declaring the wrong one sends your request down a path that can't satisfy it. When a source "just won't play," the media type is the first thing to check.

## Stopping is its own action

Stopping streaming is **not** the same as selecting a different source. Stop means releasing or pausing the distribution leader — explicitly ending the fan-out. Model it as a distinct action, not a side effect of starting something else, or you'll get zones that keep playing after you thought you'd stopped them.

## Pitfalls

- **Using a track media_type for a station** (or a device's native play call that doesn't support stations at all). Wrong type → dead end.
- **Treating "stop" as "select another source"** — leaving the old stream running.
- **Aiming at a per-zone player** when the architecture wants the single fan-out target.

## See also

- [Principles](../framework/principles.md) — one source of truth.
- [Scripting](scripting.md) — wrap these calls in composable, idempotent scripts.
- [Pitfalls](pitfalls.md).
