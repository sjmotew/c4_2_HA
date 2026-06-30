# Layered architecture

Before you write a single script, decide how the pieces are *layered*. The most common way a whole-home build turns into spaghetti is tangling two independent questions together: **what is playing** and **where it is playing**. Keep them separate and almost everything else gets easier. Tangle them and every new feature fights the last one.

## Separate the source layer from the room layer

There are two independent axes in a whole-home system:

- **The source / engine layer** — *what* is playing. Which streaming service, which physical input, the transport state.
- **The room / distribution layer** — *where* it's playing. Which zones are in the group right now.

Build them so they **never call each other directly.** Changing the source shouldn't re-pick your rooms; adding a room shouldn't restart the source. The two layers communicate only through shared state (below), and a thin **dispatcher** wires them together at the edges.

**Why it holds:** this is [one source of truth](../framework/principles.md) applied to the two things a person actually controls. Once the layers are decoupled, presets, dashboards, voice, and reactive automations all compose the same small primitives instead of each re-implementing the whole flow. It's the single highest-leverage decision in the build.

## Two-axis state: one select for source, one boolean per room

Make the state explicit and durable using Home Assistant helpers, not variables hidden inside scripts:

- **One `input_select`** holds the active source (`None`, `Spotify`, `Turntable`, …).
- **One `input_boolean` per room** holds membership (`input_boolean.kitchen_active`, …).

Scripts *write* these helpers; dashboards, the now-playing sensor, and automations *read* them. The "active room set" is simply which booleans are on.

```yaml
# Illustrative — the two axes as helpers, the single source of truth.
input_select:
  house_source:
    options: ["None", "Spotify", "Turntable"]

input_boolean:
  kitchen_active:
  living_room_active:
```

**Why it holds:** because they're real HA entities, the whole system **survives a restart** and is introspectable — you can see the exact state in the UI, and anything can read it. State that lives only inside a running script vanishes the moment HA reloads.

## A dispatcher joins the layers

A single small entry point takes a source, stops whatever's currently active, and routes to the matching engine. Callers — a dashboard tile, a voice command, an automation — only ever learn *one* script and a *name*.

```yaml
# Illustrative — one entry point routes to per-engine scripts by name.
play_source:
  fields:
    source:
      selector: { select: { options: ["Spotify", "Turntable"] } }
  sequence:
    - action: script.stop_active_source        # stop-then-start: clear the old source
    - choose:
        - conditions: "{{ source == 'Spotify' }}"
          sequence: [{ action: script.play_source_spotify }]
        - conditions: "{{ source == 'Turntable' }}"
          sequence: [{ action: script.play_source_turntable }]
```

The room layer has its own tiny primitives — an idempotent `room_add_<room>` and `room_remove_<room>` per zone — that only touch that room's player and its boolean. Presets and reactive automations call those primitives; they never reach into the source layer to do it.

## Observe is not the same as control

Once you have a status display reading engine state, a subtle trap appears: **the engine can be telling the truth while a room is lying.** A now-playing card that reads the engine can correctly say "Service X is playing" while a particular room is actually playing a stale, ungrouped source — the report is right about the *engine* and wrong about the *room*.

The fix belongs in the **distribution layer**, not the reporting layer: when you add a room, both join it to the group *and* stop whatever it was independently playing. Don't try to patch a reconciliation problem by editing the sensor that merely observes it.

**Why it holds:** observing state and controlling state are different jobs. A reporting layer that reads one entity will faithfully report that entity — making it "more accurate" can't fix a room that's genuinely in the wrong state. Reconcile reality in the layer that owns reality.

## Pitfalls

- **Source-selection and room-membership tangled in the same script** — now every source change disturbs the room set and vice versa.
- **State held in script variables instead of helpers** — it doesn't survive a restart and nothing else can read it.
- **Callers reaching across layers** — a preset that pokes the source engine directly, or an engine script that toggles room booleans, reintroduces the coupling you just removed.
- **Fixing a reconciliation bug in the observe layer** — the sensor isn't wrong; the room is.

## See also

- [Principles](../framework/principles.md) — one source of truth, human-readable names.
- [Scripting](scripting.md) — the composable primitives this architecture is built from.
- [Automations](automations.md) — keeping the shared state truthful however playback starts.
- [Music Assistant](music-assistant.md) — the engine that usually sits in the source layer.
