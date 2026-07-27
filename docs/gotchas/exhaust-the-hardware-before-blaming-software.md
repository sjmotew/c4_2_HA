# Exhaust the hardware before blaming software

**TL;DR:** An integration that flaps intermittently for weeks is often failing hardware — eliminate the physical layer before you build software workarounds around it.

## The situation

An integration starts dropping to unavailable. Not constantly — enough to be annoying, not enough to be reproducible. You have recently added a second controller that talks to the same device, so there is an obvious suspect, and contention is a real phenomenon that produces symptoms exactly like these. The hypothesis fits, it is cheap to believe, and it points at something you can change.

So you change it. And it gets a little better, or it seems to, which is the worst possible outcome.

## What bit me

An AVR's integration kept dropping to unavailable, taking room and volume control with it. The working theory for weeks was software contention: two controllers competing for a device with a limited session budget. That theory was plausible enough that a watchdog was built around it — an automation that detected the drop and reloaded the integration automatically. It worked, in the sense that the system recovered on its own.

An exhaustive elimination pass eventually isolated the fault to a failing onboard network interface. Different switch port, different cable, different power — the flapping followed the device. It was hardware, and it had been hardware the whole time.

The device was replaced and the symptom vanished. The watchdog then sat in the configuration as dead code, silently reloading an integration that no longer needed it, with a name and a date and no explanation of what it was for. Finding it and being confident enough to delete it took its own small investigation, months later.

## The general rule

**Intermittent-and-worsening is a hardware signature.** Software contention is usually deterministic: it fails under a condition you can describe — during a specific operation, when two things run at once, after a particular sequence. If you cannot state the condition under which the fault occurs, you have not diagnosed it. You have found a hypothesis you like.

The second half of this is about the cost of being wrong. A workaround built on a wrong diagnosis does not fail loudly — it succeeds, partially, and outlives the bug it was written for. It then becomes something you have to find, understand, and be brave enough to delete, at a point when nobody remembers why it exists.

## How to apply it

- **Write down the condition under which the fault occurs before you build anything.** If the sentence comes out as "sometimes," stop and keep diagnosing.
- **Test the physical layer directly.** Different switch port, different cable, different power source, different position in the rack. Change one at a time and watch whether the fault follows the device.
- **Prefer elimination over accumulation.** Removing suspects proves things; adding mitigations only masks them.
- **Date every workaround and record what it compensates for**, in the workaround itself. A watchdog with a comment naming the bug it covers can be retired. One without a comment cannot.
- **Schedule the retirement.** When the real cause is fixed, deleting the workaround is part of that fix, not a follow-up someone will get to.

## How to verify

- For every automatic-recovery mechanism in your configuration, you can name the fault it exists for and say whether that fault is still present.
- The fault reproduces or disappears predictably when you change the physical layer — which is what proves the diagnosis either way.
- After a suspected hardware fix, the workaround is removed and the system stays healthy without it. Leaving it in place "just in case" means you did not verify the fix.

Related: [reliability](../building/reliability.md) for where watchdogs legitimately belong, and [one control authority per device](one-control-authority-per-device.md) for the contention hypothesis this incident kept mistaking itself for.
