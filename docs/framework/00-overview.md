# The 4-stage migration model

A whole-home migration looks overwhelming until you give it a shape. This is the shape: four stages, in order, each with a clear goal, a single question it answers, and an exit criterion that tells you when you're allowed to move on. Don't advance until the stage you're in is genuinely done. The order is the point.

## Stage 1 — Discovery & Inventory

**Goal:** Know every device, signal path, and dependency in the house before you touch anything.

**The question it answers:** *What do I actually have, and how is it really wired?*

You cannot migrate what you don't understand. The old system has been quietly doing things you've forgotten about or never knew — a source that routes two different ways depending on the room, a device that only works because something else powers it on first. Surface all of it now, on paper, while the old system is still running and can be observed.

**Exit criterion:** You have a written inventory of every device, a map of every signal path, and a list of the dependencies between them. Nothing is "I think it works like..." — it's confirmed.

→ [Discovery & Inventory](01-discovery-and-inventory.md)

## Stage 2 — Prove the hard part first

**Goal:** Identify the single riskiest integration and validate it in isolation, before you plan anything else.

**The question it answers:** *Is the thing I'm most worried about even possible — and how?*

Every migration has one integration nobody has a clean path for. It's the one that, if it can't be made to work, changes the whole plan. Find it and prove it before you've sunk effort into the easy parts. This stage sets your real timeline, because the hard part is the only part whose duration you can't guess.

**Exit criterion:** The riskiest integration works in isolation, reproducibly, and you understand *why* it works. "It worked once" is not proven.

→ [Prove the hard part first](02-prove-the-hard-part-first.md)

## Stage 3 — Parallel operation

**Goal:** Run the old system and the new one simultaneously, migrating one zone at a time, while the house keeps working the entire time.

**The question it answers:** *Can I move this zone over without anyone noticing?*

This is where most of the calendar time goes, and that's correct. You move a single zone to the new system, live with it, fix what's rough, and only then move the next. The old system stays up the whole time as your safety net. Nothing is irreversible yet.

**Exit criterion:** Every zone runs on the new system, has been lived with, and you'd be comfortable if the old system vanished tomorrow.

→ [Parallel operation](03-parallel-operation.md)

## Stage 4 — Cutover & decommission

**Goal:** Retire the old system — but only after the new one owns every zone and the household hasn't noticed the difference.

**The question it answers:** *Is it actually safe to pull the old system out?*

Decommissioning is the easy part if the first three stages were done honestly. Cut over, remove the old hardware, keep the few things worth keeping, and confirm nothing regressed. If you're nervous here, you skipped something earlier.

**Exit criterion:** The old system is removed, nothing broke, and the only people who know a migration happened are the ones who did it.

→ [Cutover & decommission](04-cutover-and-decommission.md)

## Why this order

Each stage exists to protect the next.

**Discovery first** so you de-risk early, on paper, where mistakes are free. **Prove the hard part second** so the one thing that could sink the project is settled before you've invested in everything around it. **Parallel operation third** so you never break the house — the old system is always there to fall back to. **Cutover last** so you only remove your safety net once trust has been earned, zone by zone, over time.

De-risk early. Never break the house. Earn trust incrementally. Do it in this order and the scary project becomes a series of small, reversible, boring steps — which is exactly what you want.
