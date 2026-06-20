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

## See also

- [Principles](../framework/principles.md) — especially one source of truth.
- The topic pages above for pitfalls specific to integrations, Music Assistant, scripting, and automations.
- The platform-agnostic [migration gotchas](../gotchas/) for lessons that aren't HA-specific.
