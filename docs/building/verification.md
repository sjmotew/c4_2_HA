# Proving it works

Every other page in this section is about building the thing. This one is about the question that comes immediately after, and that most home systems never answer honestly: **how do you know?**

The answer is harder than it looks, because a whole-home AV system is unusually good at producing convincing evidence that nothing is wrong. The script ran. The trace is green. The entity says `playing`. The dashboard shows the room as active. All four of those can be true while a room is silent — every one of them was, here, on a night when the room was silent.

This page is what's left once you stop accepting those as proof.

## The standard: by ear, against a prediction written first

Two halves, and both are load-bearing.

**By ear (or by eye at the screen).** The system's purpose is sound in a room and a picture on a panel. That is the only thing worth verifying, and no state reading substitutes for it. "The script executed" means a message was accepted. "The entity says `playing`" means an integration reported something at some point. Neither is a measurement of the room.

**Against a prediction written before the test runs.** Write down what you expect to observe — specifically enough that a wrong outcome is recognizable as wrong — and write down what would falsify it. Then run it. Without the prediction first, you will find yourself explaining whatever happened, because a system this complex offers a plausible story for every outcome. The prediction is what converts a demo into a test.

The second half is what people skip, and skipping it is why so much verification is theater. A test you can't fail isn't one.

## A passing test can still be worthless

This is the most useful thing in this guide about testing, and it cost an owner an unnecessary trip to a room to learn.

The test: with a display verified cold, issue one command asking for a named app. The prediction, written in advance: the app appears on screen. It ran, the app appeared, and it was recorded as a pass.

It was worthless. The app requested happened to be the last app that device had been using, and that device resumes its last app when it wakes. **A working command and a completely broken command produce an identical screen.** The result was consistent with the feature working and equally consistent with it doing nothing at all.

The fix was not to run the test again more carefully. It was to **change the variable**: ask for an app that is *not* the last-used one. That version passed, and the reciprocal — waking with the second app last-used and asking for the first — passed too. Two runs, opposite directions, one conclusion that means something.

The habit to build:

> **Before accepting a result, ask what else would produce this same observation.**

If a second mechanism explains it — a device's own resume behavior, a cached state, a thing that was already on, a command that silently no-ops — the test has not told you what you think it told you. This has its own [gotcha](../gotchas/ask-what-else-explains-it.md), because it generalizes far past app launches.

## Deployed is not proven

Reliability code is the specific case where this bites hardest, because it is written for a condition that by definition isn't happening while you write it. A guard that has never fired is not a guard. It is a hypothesis with good syntax.

Two things it's worth being blunt about:

**Live-fire it.** Actually cut the network port. Actually unplug the device. A guard that catches an unreachable component has to be tested against an unreachable component — there is no desk-side substitute, because the whole reason the guard exists is that state was lying, and a desk test asks the same state.

**Test at the timing that actually fails.** The first live-fire here looked like a pass and wasn't: the retry came several minutes after the cut, by which point the platform had long since marked the device unavailable and the guard had an easy, honest signal to act on. The failure the guard exists for happens in the first seconds, while the state still reads healthy and stale. Re-running it inside a ten-to-twenty second window is a completely different test, and it is the only one that matters.

Write down the elapsed time. A live-fire whose timing you didn't record can't be repeated, which means it isn't evidence — one earlier run here recorded only "port disabled" and had to be redone from scratch because nobody could reconstruct what had actually been tested.

## A mitigation is not a diagnosis

A correction that reliably restores the right behavior feels like a solved problem. It usually isn't, and the distinction is worth protecting in writing.

A recurring fault here — surround collapsing to the front speakers — got a guard: detect the bad state, correct it, notify either way. It works. It has fired, been heard working, and is genuinely valuable.

The underlying fault has still never been reproduced under controlled conditions. Nobody knows what puts the device into that state. The guard proves that *if* it lands there, it recovers — it proves nothing about why it lands there, and the confident explanation that was written down before the guard existed turned out, on re-examination, to be contradicted by the system's own documented topology.

So: **record the mitigation as a mitigation.** Say in the code and in your notes what is proven and what is assumed. The failure mode is that the symptom disappears, the pressure to diagnose disappears with it, and eighteen months later the workaround is load-bearing and nobody remembers it was ever a guess. This is the same discipline as [dating your watchdogs](reliability.md), applied to a fix that seems too good to need it.

## Your instruments lie, individually and specifically

Not "in general" — each one lies in its own way, and each one has to be caught once. The four found here, as a template for the ones you will find:

| Instrument | The lie | Consequence |
|---|---|---|
| A TV's reported power state | Lagged reality by **~11 seconds** on a clock-synchronized test | Any conclusion about actuation *timing* or *ordering* read from it carries an 11-second error bar. Several earlier questions turned out to be unanswerable from that data |
| The state API | Cannot see the platform's own notification system at all — that surface stopped being entities years ago | Reports **zero** notifications while one is live on screen. A check built on it produces a confident false failure |
| Streaming boxes | Report no app name, no app id, no source | There is no entity-level substitute for a person looking at the panel. The by-eye rule is the only instrument available, not ceremony |
| A "find dead entities" audit tool | Compares the registry against the platform's own top-level files and **cannot see config organized into packages** | Reported 130 entities as dead — including the core of the working AV system. Acting on that list would have deleted everything |

The rule underneath all four: **before building a check on an instrument, verify the instrument reads correctly in a case where you already know the answer.** Point it at something you can confirm by hand. It takes minutes, and it is the difference between a monitor and a random number generator.

That last row deserves emphasis. A tool built for the platform's default layout, pointed at a config organized differently, will confidently report your entire system as garbage. It isn't lying about the registry — it's answering a narrower question than its name suggests. See [your tools can't see your whole config](../gotchas/your-tools-cannot-see-your-config.md).

## Record the starting state, or the test proves nothing

A "cold room" test needs the room to have actually been cold. The single most common way a physical test gets invalidated is that something touched the room shortly beforehand — a device woken for unrelated work minutes earlier, a display already on from a previous check.

Three rounds of testing here were undermined by this: one whole set of room results had to be thrown out because the boxes had been woken by adjacent work moments before, and the starting-state table had been left blank so nobody could tell which. Fill it in. It costs one line per room and it's the difference between evidence and an anecdote.

## Some verification requires a person, and that's a schedule item

A material fraction of what needs proving here can only be proved by somebody standing in the room: cutting a live network port during real playback, forcing a device into a bad state and judging the recovery at the couch, confirming a picture on a panel that reports nothing useful.

Treat those as calendar events, not as tasks. They compete with everyone's evening. Two of them here waited weeks past the code being ready — which is fine and honest — but they only got done once they were scheduled as sittings rather than left as open items. And when two tests drive the same device, run them in separate sittings: back to back, they confound each other and you learn nothing from either.

The corresponding discipline in the notes: an item awaiting physical verification is **not** a defect and should never be filed as one. Something built correctly whose real-world effect hasn't been observed routes to *schedule the sitting*. Something built wrong routes to *fix the code*. Confusing the two sends you back to re-engineer working code.

## How to verify (this page, on itself)

- Every reliability guard you rely on has fired at least once, on purpose, and somebody heard the result.
- For each of your last three "it works" conclusions, you can name what would have falsified it.
- You have a written list of the instruments you don't trust, and why — specific readings, not a general disclaimer.
- Your physical tests record the starting state and the elapsed time, and could be re-run from that record by someone else.

## See also

- [Reliability](reliability.md) — the patterns this page tells you to go prove.
- [Stage 5 — Operate & harden](../framework/05-operate-and-harden.md) — where all of this lives as a standing condition.
- [Ask what else explains it](../gotchas/ask-what-else-explains-it.md) · [Deployed is not proven](../gotchas/deployed-is-not-proven.md) · [A mitigation is not a diagnosis](../gotchas/a-mitigation-is-not-a-diagnosis.md) — the lessons here in gotcha form.
