# Keeping it running

Every other page in this section is about making the system *work*. This one is about making it work **when you aren't there** — after a power cut at 3 a.m., during an update you didn't schedule, on the evening a device decides to stop answering. That is the whole content of [Stage 5](../framework/05-operate-and-harden.md), and it is where a build stops being a project and becomes something the household depends on.

The patterns below are small. Their absence is not.

## Cold-start reconciliation

**What it is:** something that runs at startup, reads what the devices are actually doing, and writes that back into your platform's state.

After a restart, your platform's idea of the world and the world have diverged. Helpers come back at their defaults or their last stored values; the AVR is on the source it was on before the outage; a room your state says is grouped isn't. Nothing errors — the two just disagree. The first interaction after every power event then behaves unpredictably, because a script that starts by "clearing what's active" clears something that was never active and skips something that is.

Reconcile on startup rather than assuming. Read the amplifier's power and selected input, read which players are actually grouped, and set your intent state to match reality — not the other way around. If you can only afford one reliability pattern, make it this one: it is the difference between a system and a demo, and it is the pattern most often skipped because everything works fine until the first unplanned reboot.

## Availability pre-checks

**Check the device is reachable before you act on it, and record what you skipped.**

Acting blind on an unreachable device produces the worst outcome available: a partial result reported as a success. Four rooms of five come up, the script completes, nothing anywhere says that the fifth was never reached. The person standing in the fifth room has no way to distinguish that from having mis-tapped.

```yaml
# Illustrative — pre-check each target, act on the reachable ones,
# and keep the skipped set so the interface can say what didn't happen.
- variables:
    targets:
      - media_player.kitchen
      - media_player.patio
    skipped: >-
      {{ targets | select('is_state', 'unavailable') | list }}
- repeat:
    for_each: "{{ targets | reject('is_state', 'unavailable') | list }}"
    sequence:
      - action: media_player.join
        target: { entity_id: "{{ repeat.item }}" }
        data: { group_members: [media_player.home_stream] }
        continue_on_error: true
- action: input_text.set_value            # surface it, don't swallow it
  target: { entity_id: input_text.last_skipped_rooms }
  data: { value: "{{ skipped | join(', ') }}" }
```

Note the `continue_on_error` on the join alone, not on the sequence — the [scripting](scripting.md) rule about keeping error tolerance surgical applies here too.

## Degrade visibly

A skipped room is only useful if somebody can see it was skipped. Expose the skipped set as state your dashboard renders — a badge, a line of text, anything that turns silence into information.

This is the failure mode that erodes trust fastest. A person who taps a control and gets nothing concludes, correctly, that the system is unreliable; a person who taps and reads "patio unavailable" concludes that a speaker is off, which is a fact they can act on. The system is equally broken in both cases. Only one of them keeps being used.

The rule that generalizes: **assert the outcome, not the command.** A command that returned success proves a message was accepted. Confirm the state you were trying to reach, and when you can't, say so.

## Tiered recovery

Escalate; don't lead with the biggest hammer.

1. **Retry the action.** Most transient failures are one dropped message.
2. **Reload the integration.** Recovers a stuck connection without disturbing anything else.
3. **Restart the service.** Everything goes quiet for a minute — real disruption, real blast radius.

Each tier is more disruptive than the last, and a recovery mechanism that restarts the whole platform because one device didn't answer is worse than the fault it fixes. Put a ceiling on retries, too: an unbounded retry loop against a device that is genuinely gone will hammer it forever and hide the outage from you.

## Watchdogs earn their keep, and their retirement

A watchdog compensating for a bug you haven't found yet is legitimate engineering. It buys you a working house while you diagnose. What makes it dangerous is that it works — the symptom disappears, the pressure to find the cause disappears with it, and the workaround quietly becomes permanent.

So **date every watchdog and record what it compensates for, in the watchdog itself.** Name it for the fault, not for the mechanism. One here reloaded an integration for weeks on the theory that two controllers were contending for a device; the real cause was a failing network interface, and after the hardware was replaced the automation sat in the configuration doing nothing, with nothing to explain it. See [exhaust the hardware before blaming software](../gotchas/exhaust-the-hardware-before-blaming-software.md) — the diagnosis and the workaround's retirement are the same piece of work.

## Isolate what became load-bearing

Components change status without announcing it. Something added as a convenience gets adopted by every room, and then a routine restart of whatever host it shares takes the whole house's audio down with it.

Watch for the moment a failure stops being local. When one component's downtime is everyone's downtime, it has earned its own lifecycle: its own container or host, its own resources, its own restart, and a recovery path you have actually rehearsed. Moving the streaming engine off the primary host was the single change here that stopped ordinary maintenance from being a household event.

Two cautions when you move something like this. Preserve the player identity every script targets, or you will be rewriting call sites for a week. And verify a **cold** boot, not a warm restart — a service that starts at the same time as its dependencies can lose a startup race that never appears when you restart it by hand.

## See also

- [Stage 5 — Operate & harden](../framework/05-operate-and-harden.md) — the stage this page implements.
- [The family interface](family-interface.md) — the surface these failures are visible on.
- [Architecture](architecture.md) — why room state needs one owner before you can reconcile it.
- [Scripting](scripting.md) — idempotency, state waits, and surgical error tolerance.
