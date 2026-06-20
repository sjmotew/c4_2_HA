# Idempotent commands

**TL;DR:** Some devices misbehave or even disable themselves when sent naive on/off or toggle commands — always check state first and prefer discrete, idempotent commands that are safe to send twice.

## The situation

You're automating power and input control for a mix of devices — displays, players, amplifiers, sources. The obvious approach is the simplest one: to turn something on, send "on"; to turn it off, send "off"; to switch it, send a toggle. It reads cleanly and it works in your first test. So you build a whole layer of automations on that pattern and move on.

## What bit me

Two failure modes, both nasty.

The first: a device that did something destructive in response to a naive "off." Instead of a clean standby, the off command put the device into a state where it stopped responding to the control system entirely — it effectively dropped off the network and couldn't be woken the normal way. A command meant to be routine had a hidden side effect that took the device out of reach, and recovering it meant getting up and doing something manual. An automation that's supposed to make life easier had instead made a device unreachable.

The second: toggles that desynced. A toggle assumes you know the current state. But commands get lost, automations fire while a person is also pressing buttons, and two things race. Send "flip it" when your assumption about the current state is wrong and you get the opposite of what you wanted — something switched off that should be on, then an automation "correcting" it, then a person fighting the automation. The system ends up oscillating, and you can't tell who started it.

The common thread: a command that's only safe under perfect conditions is a command that will eventually fail under real ones.

## The general rule

**Prefer commands that are safe to send twice, and check state before you act.** An idempotent command — "be on," "be set to this input" — produces the same correct result whether the device was already in that state or not. A non-idempotent command — "toggle," or any command with a hidden destructive side effect — produces different results depending on context you may not actually know.

This holds because real systems are full of lost messages, retries, races, and human hands acting at the same time as your automations. Idempotency is what lets all of that chaos resolve to the right state instead of compounding into the wrong one. And checking state first means you never issue a command whose outcome depends on an assumption you haven't verified.

## How to apply it

- **Read before you write.** Before sending a power or input command, query the device's current state and only act if a change is actually needed.
- **Choose discrete over relative.** Where the device offers it, use "set to on" / "set to off" / "select this input" rather than "toggle." Discrete commands carry their intended end state with them.
- **Find the destructive commands before they find you.** During discovery, test each control command on each device in isolation and watch for any that leave the device unreachable, reset configuration, or behave unexpectedly. Flag those and design around them — use a safer alternative, or a different recovery path, instead.
- **Make every automation convergent.** The goal of any sequence should be a known end state, reached the same way regardless of where the device started.
- **Guard the irreversible.** If a command can put a device out of reach, never fire it speculatively or on a timer that might catch the device in the wrong state.

## How to verify

- Run any automation twice in a row. Nothing breaks, nothing oscillates, and the end state is identical both times.
- Fire an automation while the device is already in the target state. It's a harmless no-op, not an error or an unexpected change.
- You have a documented list of any commands that are unsafe on your specific devices, and none of your automations send them blindly.
- Trigger an automation at the same time as a manual action and the system settles into the correct state instead of fighting itself.
