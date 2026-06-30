# Pitfalls

These are cross-cutting Home Assistant platform gotchas — the ones that aren't tied to a single topic. (Topic-specific pitfalls live on their own pages: [integrations](integrations.md), [Music Assistant](music-assistant.md), [scripting](scripting.md), [automations](automations.md).) Each here is a short named trap and the fix.

## Config precedence surprises

**What bites you:** the same thing is defined in two places — a UI-managed config and a file/package, say — and one silently overrides the other. You change the obvious one, nothing happens, and you lose an afternoon before finding the second definition quietly winning.

**The fix:** keep **one authority per item** ([principles](../framework/principles.md): one source of truth). Know which source wins for each kind of config, and don't split a single concern across both. When behavior doesn't match the config you just edited, suspect a second definition before anything else.

## Deprecated vs current syntax

**What bites you:** you copy an example from an aging forum post and it doesn't work, because the syntax changed. The classic is the old `service:` key versus the current `action:` key — but it's a whole family of quietly-renamed options.

**The fix:** follow the **current official docs**, not search results sorted by what's old enough to rank well. When an example fails for no obvious reason, check whether you're looking at deprecated syntax.

## Entity-id drift

**What bites you:** you rename or replace a device, its entity_id changes, and every automation, script, and dashboard referencing the old id silently breaks. Nothing errors loudly — things just stop working.

**The fix:** pin or alias entity_ids where you can, and after any device change, **grep your config for the old id** before you trust it. A two-minute search saves a confusing week.

## Reload vs restart

**What bites you:** you reload the config, the change doesn't take, and you assume your edit was wrong — when actually that kind of change needs a full restart, not a reload.

**The fix:** know which changes a reload covers and which need a restart. When a change "isn't taking" and the config looks right, **restart and re-verify** before you go debugging a non-problem.

## Include / merge surprises

**What bites you:** directory-include and merge patterns combine files differently than you expect, or duplicate keys across files collide and one wins. The result is config that's present but not behaving.

**The fix:** understand your include strategy (how files are merged, whether keys must be unique), and **keep ids unique** across included files. When in doubt, simplify the structure until the behavior is obvious.

## Keep a per-device "do-not-retry" list

**What bites you:** a stubborn device accumulates a dozen dead ends — wrong input keys, a service that 500s, an API that doesn't exist, a cloud path that's flaky — and months later you (or your AI assistant) cheerfully re-test the same broken approaches because nobody wrote them down.

**The fix:** maintain a durable, dated list of what *doesn't* work for each difficult device, alongside what does. It's the single artifact that makes a stubborn device tractable, and it compounds: every dead end you record is days you never spend re-walking.

## Power down sources before displays

**What bites you:** an "all off" that kills the displays first, then the source devices — and a streaming box's CEC "active source" signal promptly wakes the displays right back up. The house won't stay off.

**The fix:** order teardown **upstream to downstream** — sleep the *source* devices first (so nothing is left asserting "active source"), let it settle, then power off the displays last. The same CEC behavior that fights you in the wrong order is harmless in the right one.

## Trust the management UI over the labels

**What bites you:** you automate an IP-controlled power outlet by the number on its sticker — and the management UI numbers them differently, so you reboot the wrong piece of core AV gear.

**The fix:** map each switched outlet to its real device through the controller's own UI before automating power, and trust that mapping over the physical labels. Confirm by switching one outlet and watching what actually changes.

## The one undocumented feed

**What bites you:** at cutover, a matrix input or a display goes dark because the *old controller itself* was quietly feeding it — content nobody had documented.

**The fix:** during discovery, hunt for any feed sourced by the legacy controller and decide its fate deliberately — migrate it or accept the loss on purpose — rather than discovering it the hard way. The [power-off test](../gotchas/power-off-test-the-old-controller.md) is how you flush these out early.

## See also

- [Principles](../framework/principles.md) — especially one source of truth.
- The topic pages above for pitfalls specific to integrations, Music Assistant, scripting, and automations.
- The platform-agnostic [migration gotchas](../gotchas/) for lessons that aren't HA-specific.
