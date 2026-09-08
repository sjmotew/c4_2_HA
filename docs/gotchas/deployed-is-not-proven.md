# Deployed is not proven

**TL;DR:** A reliability guard that has never fired is a hypothesis, not a guard — live-fire it, and at the timing that actually fails, not the timing that's convenient.

## The situation

You've written the reliability layer. Availability pre-checks before acting on a device, a skipped-room set surfaced on the dashboard, a guard that detects a bad state and corrects it. It's deployed, it parses, it's been code-reviewed, and the desk tests pass.

Then everything runs fine for weeks, because devices mostly work. The guard sits there with a trigger count of zero. Nothing is wrong, and you have no evidence at all that any of it does what it says.

## What bit me

Two of them, in the same stretch.

**The guard that had never run.** A detect-and-correct automation for a recurring audio fault: watch for the bad state, correct it, notify either way. Deployed, reviewed, live, correct-looking. Its trigger timestamp was `null` for its entire life. A code review of that same automation later found a real defect in it — the correction call had no error tolerance, so on precisely the device failures the system's own notes documented as common, the guard's "never fails silently" promise would have failed completely silently. Caught by reading it, not by running it. It could have sat there looking healthy indefinitely.

**The live-fire that tested the wrong thing.** The unreachable-device guard did get exercised: cut the amplifier's network port, re-run the command, confirm the other rooms keep playing. It passed.

It passed because the re-run happened about four and a half minutes after the cut. By then the platform had long since given up on the device and marked it unavailable — an honest, unambiguous signal, and the guard acted on it correctly. But the failure the guard exists for happens in the *first seconds*, while the state still reads healthy and stale and the platform hasn't noticed anything. That case had never been tested. The passing test was a different test wearing the right name.

Worse, the run wasn't repeatable from its own record. The note said "port disabled" and nothing else — not which port, not the elapsed time. Re-establishing what had actually been tested took its own session.

## The general rule

**Reliability code is written for a condition that isn't happening while you write it, so it is the one category of code that cannot be validated by shipping it.** Everything else you build gets exercised by ordinary use. A guard is only exercised by the fault, and the fault is rare by construction.

Two consequences.

**Live-fire is not optional.** Cut the port. Unplug the device. Force the bad state. There is no desk-side substitute, because the reason the guard exists is usually that some state reading was misleading — and a desk test asks that same reading. It'll agree with you.

**The timing is part of the test.** Distributed systems have a window between *a thing broke* and *the platform noticed*. Guards are for that window. A test that starts after the window closed is testing the easy case, and it will pass, and it will tell you nothing about the case you're afraid of. Re-fire in the first ten to twenty seconds and you have an entirely different — and much more informative — test.

And the honest bookkeeping that goes with it: an item awaiting a live-fire is **not a defect**. It's built, reviewed, and unproven. That routes to *schedule the test*, not to *re-engineer the mechanism*. Filing it as a bug sends someone back to rewrite working code.

## How to apply it

- **Assume any guard with zero firings is broken until proven otherwise.** Whatever your platform's equivalent of a last-triggered timestamp is, check it, and treat `never` as a red flag rather than as good news.
- **Schedule the live-fire as a calendar event.** These need a quiet house, real playback, and a person. They will not happen as a to-do item; they will happen as a sitting.
- **Cut at the layer the failure happens at.** Disabling the switch port is a truer test than stopping a service, because it reproduces the silence the guard is written against.
- **Record the elapsed seconds, and which port, and what was playing.** A test you can't re-run from its own notes is a story about a test.
- **Write the falsifiable prediction first**, then run it — including what the *other* rooms should do, which is usually the part that actually matters.
- **Read the guard's error handling specifically.** A guard whose own corrective call can fail silently inverts its entire purpose, and this is invisible from the outside.
- **Don't run two tests that drive the same device in one sitting.** They'll confound each other and you'll learn nothing from either.

## How to verify

- Every guard you depend on has a non-null firing timestamp, and somebody heard or saw the result of at least one of them.
- Your live-fire notes contain: what was cut, at what time, when the retry fired, and what was audible in each room.
- The elapsed cut-to-retry time is inside the window where the platform's state is still stale — you know that number, and it's short.
- Someone else could reproduce your last live-fire from your written record alone.

Related: [proving it works](../building/verification.md), [reliability](../building/reliability.md) for the patterns being proved, and [a mitigation is not a diagnosis](a-mitigation-is-not-a-diagnosis.md) for what a passing guard does and doesn't tell you.
