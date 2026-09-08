# Failures don't always reach the caller

**TL;DR:** A script that deliberately aborts can still return "success" to whatever called it — build the failure contract out of state plus a notification, not out of the platform's return value.

## The situation

You've done the careful thing. Your script validates its inputs, and when something's wrong it stops with an explicit error rather than blundering on: unknown room, unsupported source, device not ready. The abort is right there in the code, it's intentional, and the reason is spelled out.

So you build on it. The dashboard fires the script; if it fails, you'll surface the failure. Another script calls this one; if the inner one aborts, the outer one won't proceed. This is how error handling is supposed to compose.

## What bit me

It doesn't compose. Verified deliberately, against a throwaway script whose entire body was a single deliberate abort with an error flag set:

- **Called directly, it returned `{"success": true}`.** No error field. No failure indication of any kind. Confirmed identically through a second, completely different calling interface, which returned a plain HTTP 200.
- **The script's own execution trace knew perfectly well it had failed** — marked `aborted`, visible in the platform's own trace viewer. That signal never crossed the boundary to the caller.
- The harness had been validated first against a genuinely nonexistent action, which *did* correctly return a failure. So the probe worked; the behavior is real.

A long-standing upstream issue described this for *nested* scripts. It's broader than that. Even a direct, non-nested caller sees success.

The practical consequence: a validation gate that rejects bad input is invisible to everything except a person who goes and reads the trace. The dashboard shows nothing. The calling script proceeds happily. The user taps a button, the system silently declines, and the only evidence lives in a debug view nobody has open.

**The same phase produced the mirror-image failure.** A gate meant to check whether a device supported a requested app read the device's capability list *before* the sequence that wakes the device — and a sleeping device reports no capability list at all. So the gate rejected every request to a room that was actually asleep, which is the only state a room is in when someone decides to watch something. It aborted in 8 milliseconds, every time, and reported success. Two independent defects, one invisible outcome.

## The general rule

**A platform's success/failure return describes whether it accepted your call, not whether the work succeeded.** Between "the message was accepted" and "the intended thing happened" there is a gap, and on some platforms an explicit, deliberate, documented abort falls straight into it.

Don't discover this in production. It is cheap to test directly: write a throwaway script that does nothing but fail, call it, and look at what comes back. Validate your test harness against a genuinely nonexistent action first, so you know it can detect a failure at all.

Then build the contract you actually need out of things that do cross the boundary:

- **A state signal.** Write the rejection reason into a variable your dashboard can render, immediately before the abort.
- **A notification, in the same block.** Fired unconditionally, not as an error handler.
- Keep the abort itself, for its value in the trace.

Two design rules go with this, both learned from the gate above.

**Order gates after the thing that makes their input readable.** A precondition check placed before the wake sequence is asking a question the system cannot answer yet. Moving it to *after* the room comes up costs nothing and makes it correct.

**An optional check must never be able to take down the primary job.** Wanting a specific app is a nice-to-have; getting the room's audio and video up is the job. Rewritten, that gate has no abort in it at all — it branches three ways (landed it, genuinely not available, couldn't check) and the room comes up regardless. A convenience feature that can kill the main feature is a worse trade than the convenience is worth.

## How to apply it

- **Test your platform's failure propagation directly**, with a script that only fails. Don't assume — this behavior differs by platform and by version, and is exactly the kind of thing that changes in a point release.
- **Set the state before the fallible call, and notify in the same block.** Never rely on a return value reaching a caller.
- **Surface rejection reasons where the person who tapped the button will see them** — a text field on the dashboard, not a log line.
- **Put preconditions after the step that produces the state they read.** A check on a sleeping device's capabilities is a check on nothing.
- **Make optional gates non-fatal by construction.** No abort path in a nice-to-have. Branch, record, continue.
- **Distrust the fast abort.** Anything that fails in single-digit milliseconds usually never reached the device. That timestamp is a diagnosis.
- **Be careful with proposed fixes you haven't verified.** A response-passing mechanism was suggested as the clean answer here and was deliberately *not* built on, because nobody had confirmed it behaved as advertised on this version. Verify platform behavior before designing around it.

## How to verify

- Deliberately trigger a validation rejection from the dashboard. Something visible must change within a second or two — a message, a badge, a notification.
- Call a deliberately-failing script from another script. Confirm what the outer one does, and design for what you observe rather than what you expect.
- Check the trace timing on a rejection. A near-instant abort means the gate ran before its input existed.
- Turn off every optional feature's happy path (rename an app, unplug a source) and confirm the room still comes up.

Related: [scripting](../building/scripting.md) for keeping error tolerance surgical, [reliability](../building/reliability.md) for the assert-the-outcome-not-the-command rule this is a specific case of, and [deploys invalidate open clients](deploys-invalidate-open-clients.md) — the other way a tap silently does nothing.
