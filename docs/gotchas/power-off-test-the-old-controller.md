# Power-off test the old controller before you plan its removal

**TL;DR:** Before you plan to decommission the legacy controller, physically cut its power and watch what actually goes dark — don't reason about whether it's in the signal path, prove it.

## The situation

You're staring at the old controller wondering how scary it is to remove. The fear is that it sits *in* the signal path — routing audio or video, switching matrix inputs, the thing every zone secretly depends on. If that's true, pulling it takes the house down. If it's false, removal is almost trivial. Most planning happens in this fog, because the honest answer is "nobody documented what this box actually does."

## What bit me

The assumption was that the legacy hub was load-bearing — that it carried signal, not just commands. That assumption shaped a cautious, drawn-out decommission plan built around a worst case nobody had confirmed.

The fix was embarrassingly simple: a controlled power-off test. Kill power to the controller, then walk the house and check what's still reachable and still playing. The result reframed the entire endgame — every display, amplifier, and source still had its own independent network path and kept working. The controller was an *orchestrator* (it sent commands) not a *router* (it didn't carry the signal). What actually remained was a much smaller, clearer list: scene orchestration to rebuild, one undocumented feed to chase down, and the remotes/keypads to replace.

## The general rule

**An integrated controller is usually an orchestrator, not a signal router — but the only safe way to know is an empirical power-off test, not deduction.** Many whole-home systems leave the actual AV signal flowing through independently-addressable devices and use the controller purely to issue commands. When that's the case, the controller can leave and the signal path doesn't notice.

This holds because the failure of reasoning here is expensive in both directions: assume it's load-bearing and you over-engineer a removal that didn't need it; assume it isn't and you're wrong, and you take the house dark at cutover. A five-minute test collapses the uncertainty that a week of planning can't.

## How to apply it

- **Do it early, during discovery** — not at cutover. The answer reshapes your whole plan, so you want it before you commit to a sequence.
- **Make it a controlled test.** Pick a low-stakes window, tell the household, and have a way to restore power fast.
- **Pull power to the controller and walk every zone.** Note what's still reachable on the network, what still passes audio/video, and what genuinely went dark.
- **Anything that went dark is your real remaining work** — that's what actually depended on the box. Everything that survived is decoupled already.
- **Watch for the one surprise feed.** If a matrix input or a display was being driven by the controller itself, this is when it reveals itself (see also the note on undocumented feeds in [pitfalls](../building/pitfalls.md)).

## How to verify

- With the controller powered off, every zone you care about is still reachable and can still play.
- You can list, precisely, the things that *did* stop — and each one is now a tracked migration task rather than an unknown.
- Your decommission plan is sized to what the test revealed, not to a worst case you never confirmed.
