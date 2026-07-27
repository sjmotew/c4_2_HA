# Power commands have a blast radius

**TL;DR:** A single "power on" can wake zones you never named — scope power commands to the zone, then verify each zone independently, because "I turned it off" is not the same as "it is off."

## The situation

Multi-zone hardware — an AVR driving a main room plus two secondary zones, a matrix amplifier, anything with more outputs than it has front-panel buttons — exposes more than one power command. There is a system-level one that the documentation lists first, and there are zone-scoped ones buried further down. The system-level command is shorter, it appears in every example you find, and it works. So it ends up in your scripts.

It works in the sense that the room you were testing comes on. What it also does is invisible from where you are standing.

## What bit me

A system-level power command sent to a multi-zone amplifier powered its main zone *and* two secondary zones. One of those secondary zones came up on the source it had retained from days earlier, at a normal listening volume, and played into an outdoor area for roughly fifty minutes before anyone noticed. The display in that zone stayed dark the entire time, so there was no visual cue that anything was on — just audio, in a part of the house nobody was in.

The same command had woken a different room's secondary zone an hour earlier, and that one went unnoticed too. The fix that seemed obvious — power off the main zone — left both secondary zones running, because the off command had been scoped to one zone while the on command had not.

## The general rule

**Device power is hierarchical, and the top of that hierarchy is rarely what you want.** A system-level power command is a broadcast to every zone the device owns, each of which comes up on whatever source and volume it happened to retain. You did not choose those. The device did, at some point in the past, and it remembered.

The corollary is about verification. A power-off that returns success tells you a command was accepted, not that a room is silent — and asymmetric scoping (broad on, narrow off) is the specific shape this failure takes. Treat every power-off as unverified until each zone's state has been read back individually.

## How to apply it

- **Enumerate every zone a device exposes before you automate its power.** If you cannot list them, you cannot reason about what a power command reaches.
- **Prefer discrete zone-scoped commands over the system-level one**, for both on and off. Never pair a broad on with a narrow off.
- **After any system-level power event, explicitly power off each zone.** Not the main one — each one.
- **Never treat a dark display as evidence of a silent room.** Audio zones and display zones are independent, and the quiet failure is the zone with no screen attached.
- **Check what source a zone retains.** A zone that wakes on a retained source is a zone that starts playing without being asked to.

## How to verify

- You can name every zone the device exposes, and every power call in your automations targets exactly one of them.
- After a power-off sequence, a state readback on each zone individually reports off — not an inference from the main zone.
- A deliberate test: send the system-level power command, then walk the house. Nothing you did not intend is audible.

Related: [idempotent commands](idempotent-commands.md), and [stale state is not proof of silence](stale-state-is-not-proof-of-silence.md) for the other half of this problem — what your automation platform believes about a room versus what the room sounds like.
