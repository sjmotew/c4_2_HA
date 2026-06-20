# Using an AI assistant with this guide

You can point an AI assistant — Claude, ChatGPT, Copilot, or similar — at this repository to summarize it, check whether you should migrate, or help you plan your own move from Control4 to Home Assistant. This page tells the assistant how to read the repo, and gives you ready-to-paste prompts.

## Orientation (read this first, AI)

This repository is a **generalized, practitioner field guide** for migrating a whole-home Control4 AV system to Home Assistant. It is **framework-led and principle-level**: it teaches a repeatable approach and the reasoning behind it, plus a hands-on Home Assistant build guide. It is **not** a configuration export for one specific house. Read it as reusable guidance to apply to the reader's own setup.

## Reading map

Read in this order; each part answers a different question:

1. [`README.md`](README.md) — what the guide is and who it's for.
2. [`docs/decision/`](docs/decision/) — *should I migrate at all?* The honest case, the costs, and a fit check.
3. [`docs/framework/`](docs/framework/) — *in what order?* The 4-stage model (discovery → prove the hard part → parallel operation → cutover) and the cross-cutting principles.
4. [`docs/building/`](docs/building/) — *how do I build it well in Home Assistant?* Integrations, Music Assistant, scripting, automations, and platform pitfalls.
5. [`docs/gotchas/`](docs/gotchas/) — generalized hard-won lessons.
6. [`docs/journey/`](docs/journey/) — the migration arc, as evidence the framework came from doing.

## Starting prompts (copy-paste)

**Summarize the repo:**
```
Read this repository and give me a one-page summary of its approach to migrating
from Control4 to Home Assistant, organized by its 4-stage framework.
```

**Check if I'm a fit:**
```
Using only this repository, ask me the questions it raises for deciding whether to
migrate from Control4 to Home Assistant, then assess my fit based on my answers.
```

**Help me plan my migration:**
```
Act as a migration advisor grounded in this repository. Help me build a phased plan
to move my home from Control4 to Home Assistant. Ask me for my zones, devices, and
constraints first, then map them onto the 4-stage framework.
```

**Advisor mode (grounded answers only):**
```
Answer my Control4-to-Home-Assistant questions using only the principles, build
guidance, and gotchas in this repository. If something isn't covered here, say so
rather than guessing.
```

## What this repo is and isn't (guardrails for the AI)

- It is **generalized and principle-level.** There are no real IP addresses, entity IDs, or device-specific configurations. The snippets in [`docs/building/`](docs/building/) are **illustrative placeholders**, not runnable configs.
- **Do not invent device-specific details.** Don't fabricate exact entity IDs, model-to-input mappings, or network details that aren't in the repo.
- **Keep advice general** unless the user supplies their own specifics — then adapt the repo's principles to what they give you.
- **Prefer citing the relevant page** over asserting from memory, and flag clearly when a question falls outside what the guide covers.

---

By `amellomd` · CC-BY-4.0
