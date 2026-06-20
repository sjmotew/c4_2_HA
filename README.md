# C4 → HA Migration Field Guide

A practitioner's field guide to migrating a whole-home Control4 AV system to Home Assistant — without downtime and without a rip-out weekend.

Written for anyone weighing the move or already mid-migration: the decisions, the framework, and the gotchas that only show up once you're holding the soldering iron.

## Why this exists

Most people start a migration doc on day one, get three zones in, and never come back to finish it. This one is different: it was written *after* a completed, multi-phase migration of a roughly 10-zone whole-home audio/video system — then distilled into reusable decisions and principles. The goal is not to document one house. It's to hand the next person the map I wish I'd had.

## Who this is for

There are two ways into this guide, depending on where you are:

- **Deciding whether to leave Control4?** Start with the decision layer → [`docs/decision/`](docs/decision/). It walks the honest tradeoffs before you spend a dollar or pull a single zone.
- **Already migrating?** Go straight to the framework and the lessons → [`docs/framework/`](docs/framework/) and [`docs/gotchas/`](docs/gotchas/). The framework gives you the sequence; the gotchas keep you from re-learning things the hard way.

## Map of the repo

- [`docs/decision/`](docs/decision/) — Should you leave Control4 at all? Tradeoffs, what you're signing up for, and a readiness self-check.
- [`docs/framework/`](docs/framework/) — The repeatable 4-stage migration model plus the cross-cutting principles that hold across every house.
- [`docs/gotchas/`](docs/gotchas/) — A growing catalog of hard-won, generalized lessons: the failures, the rules they taught me, and how to apply them.
- [`docs/journey/`](docs/journey/) — The actual arc of one migration, compressed — proof the framework came from doing, not theorizing.

New here? The one-screen router lives at [`docs/00-start-here.md`](docs/00-start-here.md).

## How this is written

Framework-first, journey-as-evidence. The principles lead; the story exists to show they were earned. Everything is **principle-level** — no copy-paste configuration, no exact device-to-input maps, no "do this and you're done." Your house is wired differently from mine, your gear is different, and your family's tolerance for a dark living room is different. The point is to teach you how to think about the migration so you can make the right call for your setup.

## Contribute your experience

This guide is meant to grow. Your migration bit you in a way mine never did — that lesson belongs here.

- **Share a gotcha or suggest a recommendation** by opening an [issue](../../issues/new/choose).
- **Ask an open-ended "how did you handle X?"** in [Discussions](../../discussions).

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for the contribution conventions and the anonymization bar every entry must clear.

---

By `amellomd` · CC-BY-4.0
