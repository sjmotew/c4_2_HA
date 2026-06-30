# Building it well in Home Assistant

This is the repo's first hands-on, Home Assistant-specific section. The [decision](../decision/) and [framework](../framework/) layers are deliberately platform-agnostic — they're about *whether* and *in what order*. This section is about *how to build it so it doesn't fight you* once you've committed to Home Assistant.

It's written as learnings and general direction, not an exhaustive reference. The goal is to point you the right way and warn you off the holes — not to document every option HA gives you.

## About the snippets

Unlike the rest of the guide, the pages here include small config snippets. They are **illustrative, not copy-paste**: they show the *shape* of a pattern using generic placeholder entities like `media_player.kitchen`. Your entity names, your wiring, and your devices are different. Read them for the idea, not the literal values.

## In this section

- [**Architecture**](architecture.md) — the one decision that keeps the build from becoming spaghetti: separate *what's playing* from *where it plays*, joined by a dispatcher over explicit shared state. Read this first.
- [**Integrations**](integrations.md) — choosing integrations that age well, fronting devices with readable wrapper entities, and when to drop down to the raw device instead.
- [**Music Assistant**](music-assistant.md) — using one streaming engine as the backbone that fans out to every zone, instead of a different mechanism per service.
- [**Scripting**](scripting.md) — composable, idempotent, name-based scripts that stay readable and don't fight your own taps.
- [**Automations**](automations.md) — activity/state automations that enforce a single active source and never loop or double-toggle.
- [**Pitfalls**](pitfalls.md) — cross-cutting Home Assistant platform gotchas that aren't tied to one topic.

Everything here is the [principles](../framework/principles.md) applied in Home Assistant. When something bites you mid-build, the [migration gotchas](../gotchas/) are the field dressing.
