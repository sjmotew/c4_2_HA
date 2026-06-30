# One control authority per device

**TL;DR:** Two controllers — or two grouping mechanisms — fighting over the same physical device will silently break volume, transport, or availability; give exactly one thing authority over each device.

## The situation

Your new system has more than one way to reach a device. A streaming engine fronts a player with its own wrapper entity, *and* the device has a native integration. Or two multiroom grouping mechanisms both exist on the same speaker — the device's own native multiroom protocol, and the grouping your streaming brain builds. Each path works in isolation when you test it, so you wire whichever was convenient in each script. Nothing warns you that two of them are now pointed at the same hardware.

## What bit me

Two distinct failures, same root cause.

The first: a room played at a fixed volume with **no working volume control anywhere** — not the native device entity, not the streaming wrapper, not even the vendor's own phone app. The cause was two grouping planes contending for the same player: scripts had joined the room using the device's native multiroom group, while the streaming engine had independently built its *own* group on its wrapper entities. Two groups, one piece of hardware, neither willing to yield authority. Only a hard power-off broke the deadlock.

The second: a device's native control entity kept dropping to **unavailable**, taking all room and volume control with it. The device only allowed a limited number of concurrent control sessions, and the streaming engine was holding one of them open — starving the native integration. Two controllers, one session budget.

## The general rule

**Each device gets exactly one control authority.** Pick which layer owns a device — the native integration *or* the streaming engine, one grouping plane *or* the other — and make every other layer leave it alone. Sharing authority over one device between two controllers is not "redundancy"; it's a race for a resource only one of them can hold.

This holds because real devices have finite control surfaces: a single grouping leader, a small session budget, one transport state. Two controllers each assume they own it, and their assumptions collide in ways that don't surface as clean errors — they surface as dead volume controls and entities that flicker to unavailable, which are miserable to diagnose because every individual path "works."

## How to apply it

- **Choose one grouping plane per device.** If your streaming engine builds groups, don't also join the same player into a native multiroom group. Standardize on one.
- **Give one integration ownership of each device's session.** If a streaming engine and a native integration both want a device, disable the player on whichever layer isn't the owner so it stops holding a session.
- **Never run two instances of the streaming engine against one device** — during a migration, stop the old one before pointing the new one at the same hardware.
- **Decide ownership deliberately, per device, and write it down.** "This room is owned by the streaming engine; that AVR is owned by its native integration" is a design decision, not something to leave to whichever script you wrote first.

## How to verify

- Volume and transport controls respond on the layer you designated the owner — every time, not just on a fresh restart.
- The device's control entity stays available; it doesn't drift to unavailable under normal use.
- You can name, for each contested device, which single layer owns it — and nothing else groups or sessions against it.
