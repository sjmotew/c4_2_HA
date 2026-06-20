# Stage 2 — Prove the hard part first

**Goal:** Identify the single riskiest integration and validate it in isolation, before you plan anything else.

Every migration has one part that nobody has a clean, documented path for. It's the integration where the forums go quiet, where the answers are "I gave up and used something else," where you genuinely don't know if it's possible. That part — not the easy ten zones around it — sets your real timeline. So you settle it first, while you've invested nothing you'd hate to throw away.

This is counterintuitive. The instinct is to start with an easy win for momentum. Resist it. An easy win you build before proving the hard part is an easy win you might have to tear out when the hard part forces a different architecture.

## Spot the riskiest integration

Your [discovery work](01-discovery-and-inventory.md) already pointed at it. The riskiest integration usually has one or more of these smells:

- **No off-the-shelf component exists for it.** You'd be building or bridging something custom.
- **It's proprietary or undocumented** — a closed bus, an unpublished protocol, a device that was only ever meant to talk to its own ecosystem.
- **It spans systems** — it has to make two things that were never designed to cooperate work together.
- **Everyone who tried it describes pain**, or nobody describes it at all.

For a whole-home AV migration, the hard part is often the thing that makes many zones behave as one — distributing a single experience across the house and keeping it coherent — because that's exactly the value the old proprietary system was built to deliver, and the part it guarded most tightly. Yours might be something else. You'll know it because thinking about it is what makes you nervous.

Pick the one that, if it turned out to be impossible, would change your whole plan. That's the one to prove.

## Isolate it and prove it

Build the smallest possible test that exercises *only* the hard part. Not in your live system — off to the side, where you can break things freely and where a failure costs nothing.

- **Strip away everything else.** No dashboards, no automations, no other zones. Just the one integration, doing the one thing, by hand.
- **Make it work once.** This is the spike. It's allowed to be ugly. Hardcode things. The goal is a yes/no answer to "is this even possible," as cheaply as possible.
- **Then make it work on purpose.** Once it works once, figure out *why* it worked — which command, which sequence, which condition. A spike that works for reasons you can't name hasn't proven anything; it's a coincidence you'll fail to reproduce.

## What "proven" actually means

"It worked" and "it's proven" are different claims, and conflating them is how projects get blindsided in month three. Proven means:

- **Reproducible.** It works again, on demand, from a cold start — not just the once when the stars aligned.
- **Understood.** You can explain the mechanism. You know which step does the work and why, so when it breaks later you'll know where to look.
- **Bounded.** You know its limits — what it can't do, where it's fragile, what makes it fall over. Those limits are real constraints you'll design around.

Until all three are true, the hard part is not proven, and any timeline you write is fiction.

## How this sets your timeline

The easy parts are estimable — you can guess a zone and be roughly right. The hard part is the only piece whose duration you genuinely cannot guess until you've done it. So proving it first does two things: it removes the chance that the whole project is impossible (the worst discovery to make late), and it converts your single largest unknown into a known quantity.

Once the hard part is proven, the rest of the migration stops being a leap of faith and becomes a sequence of estimable, boring steps. That's the moment you're allowed to plan the rest — and the moment you move to [parallel operation](03-parallel-operation.md).

## Exit criterion

The riskiest integration works in isolation, reproducibly, from a cold start, and you can explain *why* it works and where its limits are. "It worked once" does not count. When you can make it happen on demand and describe the mechanism, the scariest part of the project is behind you.
