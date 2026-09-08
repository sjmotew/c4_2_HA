# Naming as infrastructure

Names look like a cosmetic concern for about eight months. Then a device gets replaced, a zone gets repurposed, and you discover that a third of your automations are addressing hardware by a name that no longer describes anything in the building.

The [principles](../framework/principles.md) page already says to name by room and function rather than by index. That's the right instinct and it isn't sufficient on its own, because it doesn't answer the question that actually comes up: *this box serves three rooms — which room's name goes on it?* This page is the rest of that answer, and the mechanics of fixing names once they're already wrong.

## Names drift, and nothing warns you

The failure here is slow. A receiver fails and gets replaced; the integration hands the replacement an entity id derived from the old model number, or you keep the old id deliberately so nothing breaks. Both choices are reasonable in the moment. What you have afterwards is a system where the most-referenced entity in the config is named after a device that is physically in a box in the garage — dozens of call sites, all working perfectly, all lying about what they address.

The cost isn't broken automation. It's that every future change starts by re-deriving what the name means, and every person you ever hand this to — including yourself in a year, and any AI assistant you point at the config — starts from a false statement. One of the two worst names in the system here read as a bathroom and addressed a speaker in a guest bedroom. Nothing had ever broken. It was simply wrong, and it had been wrong long enough that other names had been chosen to be consistent with it.

**Names decay in one direction only.** No process makes them accurate again except a deliberate pass.

## The grammar: endpoints room-first, infrastructure device-first

The rule that survives new hardware:

- **An endpoint** is something the household would name when asking for something — *put the game on in the gym*, *turn the bedroom TV off*. It belongs to exactly one room. Name it `<room>_<role>`: `media_player.gym_tv`, `media_player.kitchen_speakers`.
- **Infrastructure** is plumbing nobody in the house names, **or** any single chassis that serves more than one room. Name it `<chassis>_<zone-or-role>`: `media_player.main_avr_zone2`, `media_player.matrix_output_3`.

The second half of that infrastructure test is the load-bearing part. A multi-zone amplifier's second zone drives one room today. Naming it after that room hides the fact that it shares a chassis — and its power state, its firmware, its network interface, and its failure modes — with a different room entirely. When you later ask *why did the patio go down when I restarted the living room?*, the names have to be able to answer.

**Tie-breaker:** if renaming something to a room name would let two entities in different rooms both claim the same physical chassis, it is infrastructure.

**The cost, stated honestly:** a reader has to know which category a thing is in before they can guess its name. That's a real price. It buys you names that stay true when one box starts serving two rooms, which is the situation that breaks the simpler rule.

## Pick the household's words, then never deviate

Whatever the people who live there call a room is the correct name, and it has to be the *only* name. Not the builder's floor-plan label, not the vendor's default, not a synonym you find more natural. If the household says "primary bedroom," then `main_bedroom`, `master_bedroom`, and bare `bedroom` are all defects — including in the one file where using the short form was convenient.

This matters more than it seems, because that vocabulary becomes an interface. Anything you later build that takes a room as a parameter — a dashboard control, a voice command, a schema of valid values — is built on the assumption that the room set is closed and unambiguous. If it isn't settled first, you are debugging synonyms inside a feature instead of fixing names.

## The same room has more than one spelling — and grep only finds one

This is the part that turns a rename from an afternoon into a project.

A room name typically exists in at least three forms at once, and they are deliberately different:

| Form | Looks like | Lives in |
|------|-----------|----------|
| Identifier token | `primary_bedroom` | entity ids, script ids, dictionary keys, arrays in dashboard code |
| Display / parameter string | `Primary Bedroom` | the value passed to a script's `room:` field, card titles, any text a person reads |
| Platform friendly name | whatever the integration set | the entity registry |

The trap: **the display form contains no entity id.** Searching the config for a retired entity id will never find the place where a dashboard button passes `"Bedroom"` to a script that compares it against a hard-coded list of room labels. Those sites are invisible to the search you would naturally run, they are the ones that break, and the breakage surfaces as *the button does nothing* with no error anywhere.

Two rules follow:

- **Change the identifier form and the display form together, as one unit of work.** They are the same rename wearing two costumes.
- **Enumerate the grep-blind sites before you start.** Anywhere a room is a string compared against another string, rather than an entity reference — selector option lists, equality tests in templates, arrays of labels, the dashboard's own code — and check each one by reading it, not by searching it.

## Renaming mechanics: the wrong method silently makes duplicates

How you rename depends on who owns the entity's identity, and the two mechanisms are not interchangeable.

**Integration-owned entities** — anything a device integration created. The integration owns the unique id; the entity id is just a label pointing at it. Rename through the entity registry (the UI's rename, or the equivalent API). The registry entry keeps its identity, history follows the entity, and nothing is left behind.

**Config-owned entities** — helpers and scripts you defined in YAML. Here the key in your config *is* the identity. Rename by editing the key and reloading. **Do not also rename it through the registry.** If you do, the old registry entry claims the new id, your reloaded config registers a second entry under the same name, and the platform resolves the collision by appending a suffix — leaving you with exactly the `_2` junk the rename was supposed to remove. Accept that a config-key rename does not carry history forward; that's the trade.

**Automations** are their own case on most platforms: the entity id is generated from the friendly name at *first* registration and is sticky forever after. Editing the name later updates what's displayed and nothing else. Renaming one for real means a registry rename.

One more, learned the expensive way: **deleting a registry row does not delete an entity that an integration is still providing.** It disappears, the integration re-creates it on the next restart, and you conclude the delete "didn't take." The distinguishing signal is whether the platform marks the entity as *restored* — a restored entity is a genuine orphan and will stay deleted; a live-provided one has to be removed at the integration, or disabled. Deleting its row is a permanent no-op.

## Treat the rename as a cutover, not a refactor

A whole-system rename touches the live house. Everything [Stage 3](../framework/03-parallel-operation.md) says about parallel operation applies, compressed into one window:

1. **Freeze the map first.** One document, one row per entity: old name, new name, why. Amend it in place when reality disagrees; don't let the plan and the house diverge silently.
2. **Land the convention before any rename.** Otherwise the first fifty renames encode a rule you're still arguing about.
3. **Run the search gate and the parse gate before touching the registry.** Zero references to retired names anywhere in the config; every touched file still parses.
4. **Rename in one window, with someone present.** Half-renamed is the worst state available — the old name is gone and the new one isn't wired everywhere yet.
5. **Prove it by ear before you call it done.** One command that lights up two rooms on two different distribution paths tells you more than any number of green state readings. See [proving it works](verification.md).

And expect the map to be wrong somewhere. The pass here amended its own frozen map mid-window when entities turned up that the audit had missed. That's the process working — the map is a plan, the house is the truth.

## A false alarm worth knowing about

The first by-ear check after the rename here came back **no sound**, which reads exactly like a rename that broke the audio path.

It hadn't. The command under test selected *which rooms* play, not *what* plays, and nothing was playing — so every room correctly joined silence. The names were never at fault, and the evidence that proved it was already on screen before anyone walked to a room.

Before you spend a trip to a room, ask what *else* would produce the observation you just got. It is the whole content of [ask what else explains it](../gotchas/ask-what-else-explains-it.md), and it is the cheapest debugging habit in this guide.

## How to verify

- Pick your three most-referenced entities and say out loud what physical device each one is. If any answer needs a caveat, the name is wrong.
- Search the config for every retired name. Zero hits — then read the grep-blind sites by hand, because the search cannot reach them.
- Ask someone else, or an AI assistant, what a name refers to using only the config. A name that needs you to explain it isn't done.
- After a rename window: config parses, the family-facing interface renders with no missing-entity cards, and one command produces audible sound in two rooms on two different paths.

## See also

- [Principles — human-readable names over indices](../framework/principles.md) — the shorter rule this page extends.
- [Pitfalls — entity-id drift](pitfalls.md) — the platform-level version of what happens when a name changes underneath you.
- [Proving it works](verification.md) — how to check a rename actually landed.
- [Names outlive the hardware they describe](../gotchas/names-outlive-the-hardware.md) — the lesson, in gotcha form.
