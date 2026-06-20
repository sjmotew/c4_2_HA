# Stage 4 — Cutover & decommission

**Goal:** Retire the old system — but only after the new one owns every zone and the household hasn't noticed the difference.

If the first three stages were done honestly, this one is almost anticlimactic. You're not migrating anything here; that's finished. You're removing a safety net you no longer need and cleaning up after yourself. The drama, if there is any, means you skipped something earlier — so the first instruction is a gut check.

## Before you remove anything: the readiness gate

Do not pull the old system until all of these are true:

- **Every zone runs on the new system** and has been [lived with](03-parallel-operation.md) through real household use.
- **You have a current backup** of the new system's configuration — a known-good state you can restore to if the cutover itself goes sideways.
- **You could survive the old system disappearing tomorrow** without a scramble.

If any of those gives you pause, you're not at Stage 4 yet. Go back to parallel operation and finish. Removing the safety net is the one genuinely irreversible act in the whole migration; earn it.

## Decommission in the right order

Order matters, because the old system is interwoven with hardware you're *keeping*. Pull it carelessly and you'll take working zones down with it.

1. **Confirm the new system is fully in control** of every zone, one last time, end to end.
2. **Disable the old system's control** before you remove anything physical — stop it from issuing commands, so it can't fight the new system during teardown.
3. **Remove the brain, keep the body.** Retire the old system's controllers and processors — the proprietary central pieces whose job the new system has taken over.
4. **Leave shared field hardware in place** (next section) — don't yank anything other systems still depend on.
5. **Label and tidy wiring as you go.** What you remove, remove cleanly; what you keep, label so the next person (often future you) isn't reverse-engineering it.

Work zone by zone or rack by rack, verifying as you go, rather than pulling everything at once. A staged teardown means any surprise is contained to the thing you just touched.

## What's worth keeping

A migration replaces the *brain*, not necessarily the *body*. Much of the old install is perfectly good hardware that now answers to a new master. Typically worth keeping:

- **Distribution and amplification** — matrices, amplifiers, and the speaker wiring behind them. This is expensive, in-wall, and protocol-agnostic. Keep it.
- **Displays and endpoints** — the screens and players in each zone.
- **Bridges and interfaces** that let the new system reach older devices — infrared emitters, serial adapters, and the like. These earned their place; they're how the new brain talks to the old body.
- **The structured wiring itself** — the cabling in the walls is the most valuable and least replaceable thing in the house. You're almost never removing this.

What goes is the proprietary core: the controllers, processors, and licensing that made it *that* system rather than just a collection of capable hardware.

## Verify nothing regressed

Once the old system is out, do a deliberate regression pass — don't assume that "it worked yesterday with the old system still present" means "it works today with it gone." Removing the old system can surface dependencies you didn't know existed.

- **Walk every zone, every source, every common action.** The same checklist you'd use to prove a single zone, run across the whole house.
- **Test from cold.** Power things down and bring them up fresh. Wake chains and power dependencies that were masked while the old system was idling in the background will show themselves now.
- **Re-check the shared infrastructure** you kept — the matrices and amplifiers — to confirm nothing about their behavior depended on the old controller being present.

Fix anything that surfaces before you call it done. This is your last pass with the project mindset still engaged; use it.

## The real definition of done

The technical checklist matters, but it's not the finish line. The migration is done when **the only people who know it happened are the ones who did it.**

- No one in the household is working around a new quirk.
- No one has asked why something changed.
- The house simply works, the way it always did — except now it's yours, it improves on your schedule, and changing it costs minutes instead of a service call.

That silence is the goal. A whole-home migration succeeds not with a launch announcement but with the absence of one. If the people you live with never had to think about it, you did it right.
