# Scripting

Scripts are where your "do this whole thing" sequences live — start listening in the kitchen, watch a movie in the living room, shut it all down. Good scripts are **composable, idempotent, name-based, and readable.** Bad ones are long, fragile, and fight you every time a tap repeats.

## Compose small scripts

Build small scripts that **call each other** rather than one monolith per activity. A "listen in the kitchen" script calls a shared "stop everything else" script; a "movie night" script calls "wake the TV" and "set the source." Reuse the pieces.

**Why it holds:** small composable scripts are testable in isolation, readable at a glance, and fixable without fear of breaking five other flows. The monolith is where bugs hide.

## Reference sources by name, never by index

Select sources and zones by **name**, not by raw numeric index. "Kitchen, Spotify" — not "zone 4, input 2."

**Why it holds:** indices are an accident of how hardware enumerates itself, and they drift. Six months on, an index tells you nothing and may not even point at the same thing. Names survive reorganizations and are obvious when wrong. (This is the [principles](../framework/principles.md) rule on human-readable names.)

## Make every sequence idempotent

A script should be **safe to run twice.** The reliable pattern: before selecting a new source, clear whatever's currently active. Then a double-tap, or an automation firing while you also press a button, can't leave the house in a half-on, contradictory state.

```yaml
# Illustrative — a small composable "listen" script, safe to re-run.
script:
  listen_kitchen:
    sequence:
      - action: script.stop_all_sources          # idempotent: clear what's active first
      - action: media_player.select_source
        target:
          entity_id: media_player.kitchen
        data:
          source: "Spotify"
```

This is the single most important habit in scripting a real house. See the migration gotcha on [idempotent commands](../gotchas/idempotent-commands.md) for why it matters so much.

## Model wake chains explicitly

A bare "play" won't wake a sleeping device. If a source needs its display on and the right input selected before it'll respond, **put those steps in the script, in order** — power, then input, then action. Don't assume the device will sort itself out; it won't.

## Wait for the state, not the clock

A fixed delay is a guess about how long a device takes. When the device exposes a state you can watch, **wait for that state with a ceiling** instead — it's faster when the device is quick and bounded when it's slow or offline.

```yaml
# Illustrative — wait for the device to actually be ready, then move on regardless.
- action: media_player.turn_on
  target: { entity_id: media_player.living_room_avr }
- wait_for_trigger:
    - trigger: state
      entity_id: media_player.living_room_avr
      to: "on"
  timeout: "00:00:10"
  continue_on_timeout: true        # proceed even if it never reports ready
```

Reserve a fixed `delay` for the things Home Assistant *can't* see: an HDMI/CEC handshake settling, a network card finishing its boot. Those have no entity to wait on, so a short, measured pause is correct there. Everywhere else, prefer the event.

## Use the minimum reliable delay where a delay is unavoidable

For the genuinely unobservable settles above, use the **shortest pause that's actually reliable**, not a comfortable round number copied from a forum. Every padded second is a second the house feels sluggish.

But tune delays **after the system is stable, not during a refactor.** Freeze them while you're changing structure, then reduce them one device at a time, verifying physically three times and reverting the instant anything misbehaves. And leave the rare high-risk delays (a cold-boot wake) alone — low reward, high blast radius. A disciplined pass like this reclaims real responsiveness without reintroducing the wrong-state bugs that blind tuning causes.

## Parallelize independent devices; sequence the ones that share a resource

Actions on *different* physical devices can run at once — wrap them in `parallel:` so the whole house responds instantly instead of waking one box at a time. But any actions that contend on a **single shared device** — several zones joining through one group leader, say — must run **sequentially, in a fixed order**, or they race that shared resource and you get intermittent, maddening failures.

The rule: parallelize what's truly independent; serialize what shares hardware or has a real ordering dependency (the streamer can only wake *after* the input path exists).

## Block when timing depends on it; fire-and-forget when it doesn't

Calling a sub-script with `action: script.<name>` **blocks** until it finishes — use that when a later step depends on the earlier one completing (release the video activity *before* switching the room to audio). Calling `script.turn_on` is **fire-and-forget** — use that inside a `parallel:` fan-out where you want every zone to start at once and don't need to await each. Mixing these up silently is a common source of ordering bugs.

## Let harmless errors through

Some integration calls legitimately error in a normal situation — re-joining an already-grouped device, for instance, may throw even though the group is fine. Wrap **just that step** in `continue_on_error: true` so the steps that matter still run. Keep it surgical: guard the one fallible-but-harmless call, not the whole sequence, or you'll swallow errors you actually needed to see.

## Set the intent before the fallible call

When a step might fail, write your *intent state* first. Turn the room's boolean on, **then** attempt the join — so if the hardware call fails, your state still reflects what the user asked for (and the audio often still flows via a shared device). Order the durable bookkeeping ahead of the fragile hardware command.

## Pitfalls

- **Hardcoding input/zone indices** — they drift and mean nothing later.
- **Non-idempotent scripts** that stack up or desync when a tap repeats.
- **Missing wake-chain steps** so "play" silently does nothing on a sleeping device.
- **Cargo-cult delays** — long fixed sleeps everywhere "to be safe," making everything feel slow.
- **Fixed delays where a state wait would do** — slower than necessary on fast devices, and no safer on slow ones.
- **Parallelizing actions that share a device** — concurrent commands race the one resource they have in common.

## Run modes at a glance

| Mode | Use it for |
|------|------------|
| `restart` | Anything a person may re-tap, or that supersedes an in-flight run — source/activity scripts. A second tap cleanly replaces the first. |
| `single` | Teardown / "all off" that must never overlap itself. |
| `queued` | An append-style writer (saving a preset) that must serialize so rapid repeats don't race the same slot. |
| `parallel:` | Not a script mode — a sequence *block* for fanning out independent actions at once (see above). |

Profile sequences with Home Assistant's built-in **script trace** (temporarily raise `stored_traces` for per-step timestamps) rather than reaching for external tooling.

## See also

- [Architecture](architecture.md) — the layered design these primitives are built for.
- [Principles](../framework/principles.md) — idempotency, human-readable names.
- [Automations](automations.md) — scripts are the verbs; automations decide when to call them.
- [Gotcha: idempotent commands](../gotchas/idempotent-commands.md).
