# Stale state is not proof of silence

**TL;DR:** A source device reporting "off" doesn't mean the room is quiet — ask the amplifier what it's playing, not the source.

## The situation

You want to know whether a room is in use. The obvious place to look is the thing that produces the audio: the streaming box, the media player, the source entity your automation platform exposes. It has a state, that state says `off`, and building logic on it feels sound. Half the automations in a multiroom system end up asking some version of this question — is anything playing here, is it safe to switch sources, can I turn this off.

The state you are reading is the last thing the integration heard. It is not a measurement.

## What bit me

A secondary amplifier zone powered on as a side effect of a system-level power command. HDMI-CEC woke the streaming box connected to that zone, and the box resumed the show it had been playing days earlier, on its own, with nothing in the automation platform involved at any point.

The platform still reported that streaming box as `off`. By then the state was roughly two hours stale. Audio was playing in a real room, at a normal volume, while every entity the system could see agreed that nothing was. There was no error, no unavailable marker, no stale-state warning — just a confident, wrong answer to the only question that mattered.

## The general rule

**A source device's reported state describes what the integration last heard, not what the room sounds like.** CEC, native apps on the device, physical remotes, and the device's own resume behavior all change what a box is doing without informing your automation platform. The gap between the two can be hours wide and gives no signal that it exists.

The reliable question is one layer down. Anything answering *is this room quiet?* must interrogate the amplifier zone — its power state and its selected input — because that is the component actually driving the speakers. A source can be playing into an input nobody is listening to; an amplifier zone that is on, unmuted, and holding an input is audible by definition.

This is the same failure family as [device status codes can lie](device-status-codes-can-lie.md): in both, a value that looks authoritative is reporting something other than what you assumed it reported.

## How to apply it

- **Build "is anything playing" logic on the amplifier, not the source.** Zone power plus selected input plus mute state is close to ground truth. A source entity's state is a hint.
- **Treat any source entity's `off` as unverified.** Never let it be the sole condition guarding an action that assumes silence.
- **When diagnosing audible-but-invisible audio, start at the amplifier.** Ask which zone is powered and what input it holds, then work backwards to the source — not the other way around.
- **Expect CEC to wake things.** Powering an amplifier zone can start a chain reaction through everything downstream of it, none of which reports back.
- **Prefer a freshness check over a trust check** where your platform exposes one — a state's age is often more informative than its value.

## How to verify

- Every automation that acts on "the room is quiet" reads amplifier zone state, and you can point at that condition in the code.
- A deliberate test: start playback from the device's own remote or app, without touching your platform, then ask the system whether the room is in use. It should say yes.
- After a power event, the state your platform reports for each room matches what you hear when you walk through the house.

Related: [Stage 5 — Operate & harden](../framework/05-operate-and-harden.md), which covers this class of failure as a standing operating condition rather than a one-off bug.
