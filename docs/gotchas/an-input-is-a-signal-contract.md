# An input is a signal contract

**TL;DR:** An input specifies a signal level and an equalization curve, not just a connector shape — routing the wrong level into it isn't a volume problem you can trim away.

## The situation

You are wiring a legacy analog source into a modern AVR and choosing which input to land it on. The inputs all take the same connector. Several of them have names that sound like they were made for your source. The device will happily accept whatever you plug into any of them, and something will come out of the speakers, so the choice feels reversible and low-stakes — a labelling decision you can fix later in software.

It is not a labelling decision. Two inputs with identical connectors can expect signals that differ by more than two orders of magnitude, and one of them may apply a frequency-response curve the other doesn't.

## What bit me

A turntable ran through an outboard preamp that already applied RIAA equalization and output a full line-level signal — several hundred millivolts at low impedance, electrically the same class of signal as any streaming source in the rack.

It was routed into the AVR's dedicated phono input, because that is the input with the name that matched the source. A phono input expects a few millivolts straight off a bare cartridge, and it applies RIAA itself. So the signal arrived roughly 150 times hotter than the input was built for, *and* got the equalization curve applied a second time on top of the curve it already carried.

The sound was wrong in a way that resisted every obvious fix. Turning it down did nothing useful, because the dominant error was never the level — it was the doubled curve, which no gain adjustment anywhere in the chain can undo. The correct destination was an ordinary line-level input, which had been sitting unused the entire time.

## The general rule

**An input is a contract about voltage and frequency response.** The connector tells you the plug fits. It tells you nothing about what the input expects to receive or what it will do to the signal on the way in.

This matters more in a migration than it does in a fresh build, because a migration inherits wiring that someone else chose, often correctly, for reasons nobody wrote down. When you re-route that signal into a new system, you are re-signing a contract you never read. And because the failure sounds like a tone or level problem, it gets debugged as one — in software, at the volume layer, for hours — when the fault is physical and one connector over.

## How to apply it

- **Trace what the source actually outputs**, including anything in the chain that has already processed it. An outboard preamp changes both the level and the curve, which makes the source downstream of it a completely different signal from the source itself.
- **Pick the input whose expected level matches**, not the input whose name matches. Names describe someone's intent; see [automate against reality, not labels](automate-against-reality-not-labels.md).
- **Set the input mode explicitly rather than leaving it on auto-detect.** Auto-detect resolves ambiguity by guessing, and it will guess differently after a power cycle.
- **Balance loudness between sources with per-source level trim** where the device offers it. Do not reach for hardware gain that was set for a different reason — a preamp's gain setting is usually determined by the cartridge, not by how loud you want the room.
- **Accept that some devices expose the trim and some don't.** Where there is no per-source trim, master volume is your only lever, and that asymmetry is worth knowing before you promise anyone level-matched sources.

## How to verify

- For each analog source, you can state its output level and whether anything upstream already equalized it — and name the input contract it was matched to.
- Switching between an analog source and a streaming source at a fixed volume produces a difference in loudness, not a difference in tone.
- The input mode reads back as the value you set, after a power cycle, not just after you set it.
