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

## Use the minimum reliable delay

Sequences sometimes need a pause for a device to catch up. Use the **shortest delay that's actually reliable**, not a comfortable round number copied from a forum. Every padded second is a second the house feels sluggish. Tune delays down until something breaks, then back off just enough.

## Pitfalls

- **Hardcoding input/zone indices** — they drift and mean nothing later.
- **Non-idempotent scripts** that stack up or desync when a tap repeats.
- **Missing wake-chain steps** so "play" silently does nothing on a sleeping device.
- **Cargo-cult delays** — long fixed sleeps everywhere "to be safe," making everything feel slow.

## See also

- [Principles](../framework/principles.md) — idempotency, human-readable names.
- [Automations](automations.md) — scripts are the verbs; automations decide when to call them.
- [Gotcha: idempotent commands](../gotchas/idempotent-commands.md).
