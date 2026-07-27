# C4 → HA Migration Field Guide

A practitioner's field guide to migrating a whole-home Control4 AV system to Home Assistant — without downtime and without a rip-out weekend.

## Two ways in

- **Deciding whether to leave Control4?** Start with the decision layer → [`decision/`](decision/). It walks the honest tradeoffs before you spend a dollar or pull a single zone.
- **Already migrating?** Go straight to the framework, the build guide, and the lessons → [`framework/`](framework/), [`building/`](building/), and [`gotchas/`](gotchas/). The framework gives you the sequence; the build guide shows how to do it well in Home Assistant; the gotchas keep you from re-learning things the hard way.

## Map of the guide

- [`decision/`](decision/) — Should you leave Control4 at all? Tradeoffs, what you're signing up for, and a readiness self-check.
- [`framework/`](framework/) — The repeatable 4-stage migration model plus the cross-cutting principles that hold across every house.
- [`building/`](building/) — Building it well in Home Assistant: integrations, Music Assistant, scripting, automations, and platform pitfalls — with small illustrative snippets.
- [`gotchas/`](gotchas/) — A growing catalog of hard-won, generalized lessons: the failures, the rules they taught me, and how to apply them.
- [`journey/`](journey/) — The actual arc of one migration, compressed — proof the framework came from doing, not theorizing.

For why this guide exists, how it's written, and how to contribute, see the [repository README](https://github.com/sjmotew/c4_2_HA).

---

By `amellomd` · CC-BY-4.0
