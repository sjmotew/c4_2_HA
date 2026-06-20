# Integrations

**Integrations are how Home Assistant reaches your devices.** Everything else — scripts, automations, dashboards — sits on top of them. Choose and structure them well and the rest is pleasant. Choose badly and you'll spend your evenings nursing a brittle foundation.

## Prefer built-in, then weigh the rest

Reach for **built-in, official integrations first.** They track HA's release cycle, they're tested against breaking changes, and when something does break, half the community breaks with you — so it gets fixed.

When you need a **custom (HACS) integration** — and in a whole-home AV build you will — treat adoption as a decision, not a reflex. Before you depend on one, read the maintenance signals:

- **Recent activity** — commits in the last few months, not the last few years.
- **Issue health** — are bugs acknowledged and closed, or piling up unanswered?
- **Adoption** — a widely-used integration has more eyes and more longevity.

**Why it holds:** a clever integration that nobody maintains is a future outage with your name on it. The day HA ships a breaking change, an abandoned integration takes a zone down and you're the only one who can fix it. Depend on living projects.

## Front devices with wrapper entities

Don't automate directly against raw device entities everywhere. Put a **wrapper (proxy) entity** in front — a human-readable, room-named entity that represents what a person cares about ("the kitchen," "the living room AVR") rather than a specific box.

This is the [principles](../framework/principles.md) in action: **human-readable names** and **one source of truth**. Your automations read clearly, and when you swap a device, you re-point one wrapper instead of editing fifty references.

## But know when to target the raw entity

Wrappers aren't free of leaks. Sometimes a proxy or wrapper entity **misbehaves for a specific action** — it works for play and volume but errors when you ask it to select a source, because the abstraction doesn't cleanly cover that call. When that happens, target the **underlying raw device entity** for just that one action.

```yaml
# Illustrative — when a wrapper entity errors on a specific action,
# target the raw device entity for just that call.
- action: media_player.select_source
  target:
    entity_id: media_player.living_room_avr_raw   # raw device, not the room wrapper
  data:
    source: "Turntable"
```

The skill is knowing both exist and reaching for the right one: wrapper for the clean, common case; raw for the specific call the wrapper can't carry.

## Pitfalls

- **Adopting an unmaintained custom integration** because it demoed well. Check its pulse first.
- **Automating against a wrapper that silently fails** for some actions — you'll debug the automation when the problem is the abstraction.
- **Assuming an integration exposes every capability** the device has. It often doesn't; confirm the action you need actually exists before you design around it.

## See also

- [Principles](../framework/principles.md) — human-readable names, one source of truth.
- [Stage 1 — Discovery & inventory](../framework/01-discovery-and-inventory.md) — you choose integrations against the inventory you built.
- [Pitfalls](pitfalls.md) — cross-cutting HA platform gotchas.
