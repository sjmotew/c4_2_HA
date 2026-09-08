# Ask what else explains it

**TL;DR:** A test that passes exactly as predicted can still be worthless — if a second mechanism produces the same observation, you've proved nothing. Change the variable.

## The situation

You're verifying something you can only check by looking: a picture on a screen, a sound in a room, a device that came up the way you asked. There's no useful state to read, so a person has to go stand there and watch.

You do it properly. You write the prediction down before the test. You confirm the starting conditions. You run the command, you observe exactly what you predicted, and you record the pass.

This is the good version of testing. It is still not enough, and the reason is subtle enough that it survives all that discipline intact.

## What bit me

The feature: one command that wakes a room and lands its streaming box on a named app. The test: verify the box is genuinely asleep, ask for an app by name, confirm on screen that the app appears.

Prediction written in advance. Cold state confirmed and timestamped — over an hour untouched. Command issued. The app appeared on screen, precisely as predicted. Recorded as a pass.

It was confounded and unusable. The app I'd asked for happened to be the last app that box had been running, and that platform resumes its last app when it wakes. So the observation — *the app is on screen* — was produced equally well by the feature working perfectly and by the feature doing absolutely nothing beyond turning the device on.

The owner caught it seconds after being told it passed. Not by finding a flaw in the test, but by knowing the device's behavior and asking the obvious question nobody had asked.

The fix wasn't to re-run it more carefully. It was to **change the variable** — ask for an app that was *not* the last one used:

| Run | Last-used app | Asked for | Landed on | Reads as |
|-----|--------------|-----------|-----------|----------|
| 1 | Peloton | Peloton | Peloton | confounded — proves nothing |
| 2 | Peloton | YouTube TV | **YouTube TV** | clean |
| 3 | YouTube TV | Peloton | **Peloton** | clean, and reciprocal |

Two runs in opposite directions. The same amount of walking to the same room, and this time it meant something.

The identical failure had already voided an earlier round of testing in three separate rooms — the boxes there had been woken by unrelated work minutes before, so "the room came up" was equally explained by the room already being up. Same shape, different disguise, twice.

## The general rule

**Before accepting any result, ask: what else would produce this same observation?**

A prediction that comes true only tells you something if the outcome could plausibly have been different. When a device's own default behavior, a cached state, a thing that was already running, or a command that silently no-ops would produce an identical result, the test has not distinguished between "working" and "broken" — and those are the only two states you were trying to tell apart.

This is not a testing-rigor problem. Rigor is what made the test look trustworthy: the prediction was written first, the cold state was verified, the observation matched. Every one of those steps was done right, and the result was still uninformative. The missing step isn't *more care*, it's a different question — and it takes about five seconds to ask.

It bites hardest exactly where you have the least instrumentation. When a device reports nothing useful and a person has to look, the observation is coarse: *the right thing is on the screen*. Coarse observations have many causes. The thinner your telemetry, the more deliberately you have to design around confounds.

## How to apply it

- **Say the confound out loud before you run.** One line in the test: "this would also pass if ___." If you can fill that blank, redesign before you spend anyone's time.
- **Change the variable.** Ask for the thing the system would *not* have done on its own. If a device resumes its last state, test with anything other than that state.
- **Run it reciprocally.** Do it once in each direction. Two opposite-direction passes rule out a whole class of "it was already like that."
- **Record the starting state, per room, every time.** Blank starting-state notes are how three rooms' worth of results became unusable — not because they were wrong, but because nobody could tell afterwards whether they were.
- **Prefer a negative control.** Ask for something that should *fail*, and confirm it does. A test suite where nothing can fail is a demonstration.
- **When someone challenges a pass, take it seriously.** The strongest challenge here came from the person who knew the device best, immediately after being told the good news.

## How to verify

- For your last three "confirmed working" results, you can state what would have falsified each one. If you can't, they're anecdotes.
- Every by-eye or by-ear test in your notes records the starting state and what the system would have done unprompted.
- At least one test in your set is a negative control that you have actually watched fail.
- A skeptical reader given your test and your device's documentation cannot name an alternative explanation you didn't already rule out.

Related: [proving it works](../building/verification.md) for the fuller method, and [stale state is not proof of silence](stale-state-is-not-proof-of-silence.md) — a sibling failure where the misleading evidence comes from an entity instead of a screen.
