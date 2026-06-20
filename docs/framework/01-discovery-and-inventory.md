# Stage 1 — Discovery & Inventory

**Goal:** Know every device, signal path, and dependency in the house before you touch anything.

This stage produces nothing you can show off. No zone plays music, no button does anything new. It is also the single highest-leverage stretch of the entire migration, because every mistake you make here is free — it's on paper — and every mistake you *don't* catch here gets paid for later, at the worst possible time, with the household watching.

Do this while the old system is still running. A live system is observable. You can press a button and watch what happens. Once you start pulling things apart, that ground truth is gone.

## Inventory every device

Walk the house. Every device that produces, routes, amplifies, displays, or controls audio or video goes on the list. For each one, record:

- **What it is** — make, model, and where it physically lives.
- **How it's controlled** — over the network, by infrared, by serial, by a proprietary bus, by HDMI-CEC? This is the field that determines how much work each device will be. Write down the actual method, not a guess.
- **What it connects to** — what feeds it, what it feeds.
- **What it depends on** — does it need something else powered on or selected first to function at all? (More on this below; capture the hint now.)
- **Anything weird** — every device that "you have to do this one odd thing or it doesn't work." Those notes are gold.

The bar is the same one from the [principles](principles.md): nothing is "I think it works like..." Confirm it by observation. The forgotten device is the one that breaks the migration.

## Trace the signal paths

A device list is not enough. You need to know how signal actually flows — and this is where old systems hide their surprises.

Trace each source to each place it can play, and draw it. The thing to watch for: **the same source that reaches different destinations by different physical paths.** A source might go to most rooms through one route and to one particular room through a completely different one. If you model it as "one source, one path," that one room will break, and you'll debug it in the wrong place.

Also watch for:

- **One-to-many fan-out** — a single source distributed to many zones at once.
- **Shared infrastructure** — splitters, matrices, or amplifiers that several paths run through, where a change to one path can disturb another.
- **Paths that only exist sometimes** — routing that depends on what else is active.

Draw it as a map, not a list. You're looking for the structure, and structure is visual.

## Identify the control protocols

Group your devices by how they're controlled, because each protocol is a different chunk of work and a different failure mode:

- **Networked devices** are usually the friendliest — discoverable, scriptable, stateful.
- **Infrared** is one-way and fire-and-forget: you can tell a device to do something but often can't read whether it worked. That has consequences for how you'll automate it.
- **Serial** is old but reliable and frequently the most direct control you can get on legacy gear — sometimes the *only* control that still works when newer methods fail.
- **Proprietary buses and CEC** sit in between: powerful where supported, quirky and inconsistent where not.

This grouping tells you, before you've committed to anything, where your hard problems probably live — which feeds directly into [Stage 2](02-prove-the-hard-part-first.md).

## Build the dependency map

This is the deliverable that separates a discovery done well from a device list. For everything you inventoried, capture what it *needs*:

- **Power dependencies** — device A is useless unless device B is already on.
- **Wake chains** — a device that won't respond to a bare command until something upstream has been powered and the right input selected. (A streaming player that ignores "play" until its display is on and switched to the right input is the classic case.)
- **Routing dependencies** — sending signal somewhere requires something else to be set first.
- **Destructive behaviors** — devices that react badly to the obvious command. Some hardware will disable its own network control if you send it a naive "off." You need to know *before* you send it, not after. (See the [gotcha on idempotent commands](../gotchas/idempotent-commands.md).)

A wrong assumption in this map is what produces the maddening, intermittent failure later — the one that only happens in a particular order, that you can't reproduce on demand. Map the dependencies now and most of those bugs never get a chance to exist.

## Exit criterion

You're done with Stage 1 when you have, in writing:

1. An inventory of every device, with how each is controlled and what's odd about it.
2. A map of every signal path, including the ones that route differently per destination.
3. A dependency map of what powers, wakes, or routes through what.

And critically — none of it is a guess. Every line was confirmed against the running system. When that's true, you're ready to find the hard part.
