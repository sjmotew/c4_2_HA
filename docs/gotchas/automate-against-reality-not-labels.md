# Automate against reality, not labels

**TL;DR:** The names printed on a device's inputs and zones describe what someone once intended, not what's physically connected today — automate on the real wiring, and rename things in your control layer to match reality.

## The situation

You're bringing an audio/video receiver, a matrix switch, or a multi-zone amplifier into your new control system. Each of these has inputs and outputs with names already on them — "DVD," "CBL/SAT," "Media Player," "Zone 2," and so on. They look authoritative. They came from the manufacturer or from whoever set the system up before you. The natural move is to take them at face value and start wiring up automations that say "switch the living room to the DVD input."

## What bit me

The label and the wiring didn't agree.

An input named for one kind of device had something completely different plugged into it. A label that sounded like it described one room actually fed a different room entirely. In one case the same physical source reached two rooms by two different paths, so "the right input" depended on *where you were sending it* — a fact no label could have told me. I wrote automations against the names, tested them, and got behavior that made no sense: the wrong source in the wrong room, or silence where there should have been sound. The bug wasn't in my logic. It was that my logic described a house that didn't exist.

Worse, this kind of failure is quiet and intermittent. It works for the inputs where the label happens to be right and fails for the ones where it isn't, so you waste time looking for a complex problem when the real one is that you trusted a sticker.

## The general rule

**A label is documentation, and documentation lies.** It records an intention from some moment in the past, and intentions drift — devices get re-plugged, rooms get repurposed, installers take shortcuts, and nobody updates the name. The only ground truth is the physical connection: what is actually plugged into what, and where the signal actually goes.

This holds because automation is a model of the world, and a model built on stale labels is a model of a world that no longer exists. The closer your model tracks physical reality, the fewer "impossible" bugs you'll chase.

## How to apply it

- **Audit every input and zone against the wiring.** For each one, physically confirm what's connected — follow the cable, send a test signal, watch what actually responds. Don't assume; verify.
- **Build your own mapping from reality.** Write down the real source-to-input and zone-to-room relationships you confirmed, independent of the factory names.
- **Rename in your control layer to describe what's actually there.** In your new system, name an input for the device truly connected to it and a zone for the room it truly serves. Let your automations reference those honest names.
- **Capture the weird cases explicitly.** If one source reaches two places by different paths, document both paths as distinct routes. Don't try to flatten a real complexity into a single label.
- **Treat the old labels as untrusted until proven.** A name is a hypothesis to test, not a fact to build on.

## How to verify

- For every input and zone you automate, you can point to the physical evidence — a cable traced or a test signal observed — that confirms what it really connects to.
- Selecting a source by its name in your control layer produces the correct device in the correct room, every time, including the cases that route more than one way.
- None of your automations reference a factory label you haven't personally confirmed against the wiring.
- A second person reading your control-layer names could walk the house and find each thing exactly where the name says it is.
