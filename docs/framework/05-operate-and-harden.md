# Stage 5 — Operate & harden

**Goal:** Turn a working system into infrastructure the household can depend on without its builder present.

Everything up to here was a project. This one is a condition. [Cutover](04-cutover-and-decommission.md) removed the old system and, with it, the fallback that made every earlier mistake survivable. From here, the thing you built is the only thing standing between the house and silence. That shift is larger than it sounds, and it is the entire reason this stage exists.

The question it answers: *does this still work when nobody is watching — and when it doesn't, does anyone find out?* Neither half of that question has a finish line, which is the first honest thing to say about the stage.

## Why this stage exists at all

The migration behind this guide spent roughly half its total effort here, after the old system was already in a box. None of it looked like migration work: reliability hardening, a hardware replacement inside a live system, giving a component that had quietly become critical its own footing, and building the interface everyone else in the house actually touches. All of it decided whether the migration held.

Plans routinely treat decommission as the finish line. In practice it is closer to the midpoint. A system that works while you are standing in front of it, on a good day, with a laptop open, is not yet infrastructure — it is a demo that happens to be installed in your house.

## Survive the unattended restart

Power events happen when you are not home. So do reboots you did not schedule, updates that restart a service, and network blips that drop half your devices at once. How the system behaves in those minutes is not an edge case; it is the normal operating condition of anything meant to run for years.

Test it deliberately rather than waiting to be tested:

- **Pull the power and watch it come back.** Cold-start the whole chain with nobody intervening. This is the [regression pass](04-cutover-and-decommission.md) from Stage 4, run periodically instead of once.
- **Treat any required manual step as a defect.** If something needs you and a laptop to become usable after a restart, the household doesn't have a working system — it has one that works when you're home.
- **Write down what has to start before what.** Startup order is the dependency a cold boot exposes and a warm restart hides, and it is never in anyone's documentation.

## Degrade loudly, not silently

A partial failure that reports success is worse than a clean failure, because a clean failure gets fixed. If a command targets five rooms, one is unreachable, and the system says *done*, you have trained yourself to believe a report that isn't true.

Two versions of this bit hard here. One was an intermittent control bridge that dropped off the network for minutes at a time: any command landing in the gap never arrived, so the audio came up, the display stayed dark, and nothing anywhere recorded an error. The other was a wall-mounted control surface holding a days-old session after a configuration reload — taps registered on the screen and went nowhere, no error, no log entry. Both failures looked exactly like a person doing nothing. The rule that came out of them: **assert the outcome, not the command.** Confirm the thing you asked for actually happened, surface it when it didn't, and keep an independent availability signal on anything that can go quiet. The patterns live in [reliability](../building/reliability.md).

## Prove it, don't assume it

A reliability layer is the one thing you build that ordinary use never exercises. Everything else gets tested by the household simply living in the house; a guard is only tested by the fault, and the fault is rare by construction. So a guard that has never fired is not protection — it is a hypothesis with good syntax, and it can sit there looking healthy for years.

Two habits close that gap, and both cost a scheduled evening rather than any new code.

**Live-fire the guards.** Cut the network port, unplug the device, force the bad state, and listen to what the rest of the house does. There is no desk-side substitute, because the reason the guard exists is usually that some state reading was misleading — and a desk test asks that same reading, which will agree with you. Test at the timing that actually fails, too: the first live-fire here passed easily because the retry came minutes after the cut, long after the platform had noticed. The window the guard exists for is the first few seconds, while the state still reads healthy and stale, and that case had never been tested at all.

**Verify by ear, against a prediction written first.** The system's purpose is sound in a room. "The script ran," "the trace is green," and "the entity says playing" are all statements about messages, not about rooms, and all three have been simultaneously true here while a room was silent. Write down what you expect and what would falsify it, *then* run it — otherwise you'll find a plausible story for whatever happens, because a system this complex offers one for every outcome.

There is a third habit that only shows up once you start doing the first two seriously: ask what *else* would produce the result you just got. A test can match its prediction exactly and still be worthless, if a device's own default behavior explains the observation just as well. That is the whole of [proving it works](../building/verification.md), and it is the page to read before designing a physical test.

Expect a share of this work to need a person in the room, and treat those as calendar events rather than open items. They compete with everyone's evening and they do not happen otherwise.

## Names stop being true

Nothing in this stage decays as quietly as naming. A device gets replaced and its name survives the swap because renaming it would mean touching fifty call sites. A room changes function. Two years in, the most-referenced entity in the config can be named after hardware sitting in a box in the garage — with nothing broken, no error anywhere, and every future reader starting from a false statement.

The trigger to watch for is a name that needs a caveat when you say what it physically is. Audit on a schedule rather than on an incident, because there will not be an incident. And rename during a hardware swap, when you are already touching every call site, rather than promising to do it later. [Naming as infrastructure](../building/naming.md) has the convention and the mechanics; the cleanup pass here turned into its own multi-week project precisely because it was deferred.

## Notice when a component became load-bearing

Components change status without telling you. Something added as a convenience gets adopted by every room, and then a routine restart of whatever machine it happens to share takes the entire house's audio down with it. It is the same component it always was. Its blast radius is not.

The signal to watch for is a failure that stops being local. Once one component's downtime is everyone's downtime, it has earned isolation — its own lifecycle, its own resources, and a recovery path you have actually rehearsed rather than assumed. Moving the streaming engine off the primary host and onto its own footing was the single change that stopped unrelated maintenance from being a household event.

## Replacing hardware inside a live system

Devices fail years after you have built around them. Swapping one is not a different discipline from [parallel operation](03-parallel-operation.md) — it is that discipline compressed into an afternoon.

- **Diagnose before you buy.** An AVR here flapped in and out of availability for months and was blamed on software contention the whole time. Only an exhaustive isolation pass proved it was a failing network interface. Replacing the wrong thing is expensive twice.
- **Capture the outgoing device's configuration before you unplug it.** Speaker layout, input assignments, level trims, the small settings nobody writes down. You cannot read them off the replacement, and reconstructing them by ear costs more than the capture would have.
- **Prove the replacement on one zone before you cut the rest over.** Same rule as a first zone migration: make your mistakes where the fewest people are listening.
- **Re-verify every zone the old device touched.** One box usually serves more rooms than you remember, and the ones you forget are the ones that stay broken for a week.

## The family-facing surface is a deliverable

The interface everyone else touches is the product. That it works from your laptop is irrelevant — nobody else in the house has your laptop, your bookmarks, or your patience for a control that almost works.

Building the wall tablet turned out to be a small project rather than an afternoon, and its lessons generalize past the hardware. It has to come back on its own after a power cut, with zero touches: a device lock screen will halt it before the interface ever launches. It has to sit at a stable address, because an address that drifts is indistinguishable from dead hardware and will be diagnosed as dead hardware. And it has to be legible from across the room in the light that room actually has, showing what somebody standing there wants — what's playing, where, and how to make it stop. What that looks like in practice is in [the family interface](../building/family-interface.md).

## There is no exit criterion

Every other stage in this model ends. This one doesn't, and that is deliberate — it's the point of the stage rather than an omission in it.

The stages before this one ask *is it done?* This one asks *is it trustworthy?*, and that answer has to be re-earned every time the power flickers, a vendor ships an update, or a device quietly begins to die. A migration finishes; a system you live in is operated. The measure of success stops being a checklist and becomes a duration — how long the house has gone without anyone having to think about it.
