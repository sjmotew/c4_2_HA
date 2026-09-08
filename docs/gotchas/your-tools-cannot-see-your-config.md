# Your tools can't see your whole config

**TL;DR:** An audit tool built for the platform's default layout will confidently report your entire working system as dead — it isn't lying, it's answering a narrower question than its name implies.

## The situation

You've organized your configuration well — split into modules, grouped by concern, kept out of the platform's default single-file layout because that file becomes unmanageable at any real size. This is the recommended thing to do and you should keep doing it.

Then you run a maintenance tool. Find unused entities, find dead references, clean up after a migration. It gives you a list. The list is long, it's specific, and every entry looks plausible.

## What bit me

Mid-cleanup, a "find dead entities" tool returned **130 dead entities**. Among them: the master script that starts whole-home audio, the script that brings up a room's video chain, the reconciliation routine that keeps room state honest, and the reliability guard protecting the living room. In other words, most of the working system — the parts that had been used by ear, that day.

The evidence that it was wrong was in the tool's own output. Its summary reported the config as containing 2 scripts and 13 automations. The real numbers were an order of magnitude higher. The tool compares the entity registry against the platform's *top-level* script and automation files, and it cannot see configuration organized into modules — so everything the project actually defines was invisible to it and therefore, by its logic, orphaned.

Acting on that list would have deleted the AV system.

A second, quieter version of the same thing turned up in the same session. Deleting three registry entries appeared to succeed — gone from the list, no errors. They were all back after the next restart, because an integration was still providing them. The registry row was never the entity; deleting it was a permanent no-op. Only one of the four deletions stuck, and the difference was visible in a flag on the entry marking it as *restored* — meaning the provider was gone and it really was an orphan.

## The general rule

**A tool's name describes its intent; its implementation describes its question.** "Find dead entities" sounds like it examines your whole system. What it actually does is compare two specific sources, and if one of those sources isn't where your configuration lives, its answer is not merely incomplete — it is inverted. Everything correct looks broken.

This is worse than an obviously broken tool, because the output is well-formed. It's a specific list of real entity names with a plausible verdict attached. Nothing about it signals that the tool was looking in the wrong place.

The generalization, which is the same one behind the maxim about [device status codes](device-status-codes-can-lie.md): **before you trust an instrument, point it at something whose answer you already know.** Run the audit and check whether your most-used script appears on the dead list. That's a five-second check and it's the difference between a maintenance tool and a loaded weapon.

The deletion case adds a second rule: **a delete that "succeeded" is not a delete that persisted.** Anything with a live provider will come back. Verify after a restart, not after the call returns.

## How to apply it

- **Sanity-check every audit tool against a known-good entity before believing any of its output.** If something you used an hour ago is on the "dead" list, stop.
- **Read the tool's summary counts, not just its findings.** The counts are where the wrong-place-looked shows up. Here, "2 scripts" against a config with dozens was the whole diagnosis, printed by the tool itself.
- **Never bulk-act on a generated list.** Especially not deletions. Spot-check enough entries by hand to establish that the list means what it claims.
- **Know where your config actually lives, and whether each tool can reach it.** If you've moved off the default layout — and you should — assume default-layout tools are blind until proven otherwise.
- **Write the exclusion down where the next person will hit it.** A one-line note saying *this tool cannot see our module layout; exclude everything defined there* is cheaper than re-learning it.
- **Verify deletions after a restart.** Gone-from-the-list is not gone.
- **Prefer additive cleanup.** Disable, observe for a week, then delete. The thing you deleted correctly and the thing you deleted wrongly look identical five minutes later.

## How to verify

- Run your audit tool and confirm your most-used script or automation is *not* on the dead list. If it is, the tool can't see your layout.
- Its reported totals match what you actually have. A wrong denominator invalidates everything downstream of it.
- Anything you deleted is still gone after a full restart.
- Anything that came back is documented as provider-owned, with the actual removal path noted (remove it at the integration, or disable it) rather than re-attempted.

Related: [proving it works](../building/verification.md) for the wider instruments-lie problem, [device status codes can lie](device-status-codes-can-lie.md) for the same shape at the protocol layer, and [naming as infrastructure](../building/naming.md) — this bites hardest during exactly the cleanup passes that generate these lists.
