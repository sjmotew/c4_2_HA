# A mitigation is not a diagnosis

**TL;DR:** A fix that reliably restores the right behavior proves the recovery works — not that you know why the fault happens. Label it as a guess, in writing, or it becomes accepted fact.

## The situation

Something in the house misbehaves intermittently. You can't reproduce it on demand, but you have a strong theory, and you can write something that detects the bad state and corrects it. You do. It fires, it corrects, somebody confirms the result. The complaints stop.

At which point the problem is, in every practical sense, gone — and the record of what is actually *known* about it starts drifting away from what is *believed* about it, quietly, in your own notes.

## What bit me

Surround audio in one room would occasionally collapse to the front speakers. It had been reported several times over months and had never once been reproduced under controlled conditions.

An explanation was recorded early: a video-distribution device on that path was negotiating the audio capability down. It sounded right, it was consistent with the symptom, and it got written into the documentation as the cause. Over the following months it was cited, planned around, and treated as settled.

It was wrong on both halves. When the rack was finally opened, no such device appeared to be on that path at all — the ones found served two entirely different rooms. And the replacement theory that got floated in the same session was ruled out within an hour by the project's *own* topology notes, which had been sitting in four separate documents since the first month of the project and stated plainly that the second room was fed from a different output entirely. Nobody had gone back and re-read the primary artifact.

What did work was a guard: detect the specific bad mode, correct it, notify either way. It fired at ten seconds, the ceiling speakers came back, and the owner confirmed by ear. That's real and valuable.

It is also not a diagnosis. Nobody knows what puts the device into that state. The guard proves that *if* it lands there, it recovers. Whether landing there was ever what was happening in the original complaints remains unknown — and the guard was carefully written down as **a mitigation for an unexplained fault**, because the previous confident explanation had already cost months.

There's a smaller, sharper version of the same lesson from the same system. Weak wireless on one device was diagnosed as an antenna and placement limit — recorded in bold as *only moving the device or changing its antenna will fix this*, with a hardware replacement and a rewiring project justified by it. The actual cause was one setting: the access point's transmit power on that band had been left at a fraction of its maximum. Changing it recovered most of the deficit in a single step, and invalidated the conclusion and everything planned on top of it. Nobody had checked the config before proposing hardware.

## The general rule

**A working correction and a correct explanation are different artifacts, and the first one actively suppresses demand for the second.** The symptom disappears, the pressure disappears with it, and eighteen months later a workaround is load-bearing and its origin as a guess has been forgotten.

The mechanism is ordinary and hard to resist. Written-down explanations get quoted. Quoted explanations get treated as established. Nobody re-derives them from the primary evidence, because they're already documented — and documentation is exactly what a re-derivation would produce. A wrong conclusion, once recorded confidently, is more durable than the uncertainty it replaced.

The correction is small: **make the confidence level part of the record.** "Guard corrects this; cause unknown; never reproduced under controlled conditions" is one sentence longer than "caused by X" and it is the sentence that stops someone spending a weekend on hardware.

## How to apply it

- **Write mitigations as mitigations.** In the automation's own comment and in your notes: what is proven, what is assumed, and what has never been reproduced.
- **Keep "never reproduced under controlled conditions" visible.** It's the single most important fact about an intermittent fault and the first one to fall out of a retelling.
- **Before proposing hardware, check the configuration.** Both misdiagnoses here would have been caught by reading a setting. The expensive fix is the one that feels like real engineering.
- **Re-derive from the primary artifact before you act on a recorded conclusion**, especially a confident one, and *especially* if it's old. Your own project's early documents are primary evidence, and they're the ones nobody re-reads.
- **Don't let a new confident story replace an old one.** When a theory is disproved, the honest replacement is usually "unexplained," not the next-best theory. Write "unexplained."
- **Keep the reproduction attempt on the list.** A mitigated fault is lower priority, not closed.
- **Date the mitigation** — same discipline as [dating your watchdogs](../building/reliability.md), for the same reason.

## How to verify

- Every guard and workaround in your config says, in place, what it compensates for and whether the cause is known.
- You can point at the sentence in your notes that says which faults remain unexplained. If you can't find it, everything reads as diagnosed.
- For your most-cited technical conclusion, you can name the primary artifact it came from and the last time anyone checked it against reality.
- No hardware purchase in your plan is justified only by a conclusion nobody has re-derived from measurements.

Related: [deployed is not proven](deployed-is-not-proven.md), [exhaust the hardware before blaming software](exhaust-the-hardware-before-blaming-software.md) — the mirror image, where the wrong layer got blamed in the other direction — and [proving it works](../building/verification.md).
