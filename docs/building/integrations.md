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

## Same brand isn't same capability

Two devices from one vendor — even sharing a single integration — can expose different capabilities. A call that works on one (`play_media` on a model with a streaming stack) can hard-error on its sibling that lacks it. And numbered multi-zone entities don't tell you which physical room they actually drive. Verify each device's real capabilities by walkthrough, and wrap each in a room-named entity, rather than assuming the integration treats them alike.

## Give one integration ownership of each device

When a streaming engine and a native integration both want the same device, they can starve each other — the device allows only so many control sessions, and a contested one drops to *unavailable*. Pick one owner per device and disable the player on the other layer. This matters enough to be its own lesson: [one control authority per device](../gotchas/one-control-authority-per-device.md).

## Prefer local control; cloud is a reliability tax

A device API that "works when the cloud is up" is not acceptable for daily home automation — outages, expired tokens, rate limits, and devices that randomly appear offline will all eventually ruin an evening. When a device offers a local control path, invest in it even when it's harder than the cloud one. Treat cloud control as a last resort, never as the path for anything reliability-critical.

## When a device fights you, add a side channel

Some devices resist clean control: a vendor "turn on" that's deprecated on one unit but fine on another, a wake that only half-works, a power-off that drops the network card for a minute. The durable answer is often a **second, independent control path**:

- **Harden unreliable wakes** — send wake-on-LAN as a short repeat burst rather than a single packet, and add a keep-alive that re-sends it if the device goes `unavailable for:` a window during waking hours, so its network card never drifts into a standby that the next wake can't escape.
- **Drop to a local side channel** — where network control is flaky, a small dedicated node driving the device over its serial/IR control port can expose *discrete, idempotent* commands ("input HDMI 1," "art mode on") that force a known state from any starting point. A side channel survives a dead network stack.

Treat HDMI-CEC as a *helpful side effect*, never the primary mechanism — it may wake a display as a bonus, but don't build your input selection on it.

## Re-adding an integration can renumber your entities

Some integrations have no reconfigure flow — to re-point them you must delete and re-add. The trap: a stale registry entry left behind causes the re-added device to come back with a suffixed entity_id (`..._2`), silently breaking every script that referenced the original. Before re-adding, **purge the orphaned entities and devices**, then verify the id came back un-suffixed. (Migrating the integration's data directory, where it has one, preserves the underlying player IDs across the move.)

## Decouple long-lived services from the hub

If a streaming engine or similar service runs *inside* your hub, their lifecycles are coupled — a hub restart or engine update takes both down together. Running it as its own standalone service decouples them, survives hub restarts independently, and tends to be the prerequisite for later scaling work.

## Pitfalls

- **Adopting an unmaintained custom integration** because it demoed well. Check its pulse first.
- **Automating against a wrapper that silently fails** for some actions — you'll debug the automation when the problem is the abstraction.
- **Assuming an integration exposes every capability** the device has. It often doesn't; confirm the action you need actually exists before you design around it.
- **Assuming sibling devices behave alike** — same brand, different capability.
- **Building on cloud control** for anything that has to work every day.
- **Re-adding an integration without purging orphans first** — suffixed entity_ids break every reference.

## See also

- [Principles](../framework/principles.md) — human-readable names, one source of truth.
- [Architecture](architecture.md) — where these devices sit in the source and room layers.
- [Stage 1 — Discovery & inventory](../framework/01-discovery-and-inventory.md) — you choose integrations against the inventory you built.
- Gotchas: [one control authority per device](../gotchas/one-control-authority-per-device.md), [device status codes can lie](../gotchas/device-status-codes-can-lie.md).
- [Pitfalls](pitfalls.md) — cross-cutting HA platform gotchas.
