# Names outlive the hardware they describe

**TL;DR:** Entity names quietly stop describing reality when devices get replaced or rooms get repurposed — nothing breaks, and everyone who reads the config afterwards starts from a false statement.

## The situation

You name things sensibly at the start — often after the device, because that's what's in front of you and it's unambiguous on day one. Then, over a couple of years, a receiver fails and gets replaced, a guest room changes function, a zone gets re-tasked.

Every one of those changes has an obvious right move in the moment: keep the existing name so nothing breaks. It works. Automations keep running, dashboards keep rendering, nobody notices anything.

## What bit me

Two, found in the same audit.

**The most-referenced entity in the whole config was named after a receiver that no longer existed in the building.** Its physical replacement had been installed a month earlier; the name survived the swap because renaming it would have meant touching fifty-plus call sites. Every one of those sites worked perfectly. Every one of them addressed hardware by the name of a device sitting in a box in the garage.

**A family of eleven entities read as one room and controlled a speaker in a different one.** Named for a bathroom, physically in a guest bedroom. They'd been that way long enough that *other* things had been named to match — the wrong name had become the local convention, and the actual bathroom's audio was on a completely unrelated entity nobody would guess. Their human-readable labels and their ids disagreed with each other, which is the clearest possible tell and had been visible the entire time.

Nothing had ever failed. That's the point. There was no incident, no broken automation, no bad night. There was just a system where the names couldn't be trusted, and every change started with someone re-deriving what a name actually meant.

The cost came due when the next feature needed rooms as parameters. It turned into a whole phase — an audit, a written convention, sixty-two renames, and a cutover window with the owner present — before that feature could be built on a vocabulary that meant anything.

## The general rule

**Names decay in one direction only. Nothing in your system will ever make them accurate again, and nothing will warn you that they've stopped being accurate.** A wrong name is not a bug your platform can detect; it's a true statement about the past attached to a thing in the present.

The real cost isn't broken behavior — it's that every reader begins from a false premise. Future you at 11pm. Whoever you hand this to. Any AI assistant you point at the config, which will confidently reason from the name because the name is the only evidence it has.

Two rules follow from the specific shapes above.

**Name endpoints after the room, name infrastructure after the chassis.** If the household would name it when asking for something — *the gym TV* — it's an endpoint and it belongs to one room. If it's plumbing nobody names, or if one physical box serves more than one room, it's infrastructure and it should say so. Naming a shared chassis's second zone after the one room it currently feeds hides the fact that two rooms share a power state, a firmware version, and a failure.

**When the human-readable label and the id disagree about what something is, that's a defect.** Not a cosmetic inconsistency — the same class of thing as a mislabeled breaker.

## How to apply it

- **Audit names on a schedule, not on an incident.** There won't be an incident. Once a year, or after any hardware replacement, read the list and say aloud what each entity physically is.
- **Rename during the hardware swap, not after.** The one moment the cost is unavoidable anyway is the one moment you're already touching every call site.
- **Write the convention before the renames**, or the first fifty encode a rule you're still arguing about. The [naming page](../building/naming.md) has the one used here.
- **Treat a whole-system rename as a cutover.** Freeze the map, run the search and parse gates, do it in one window with someone present, and prove it by ear afterwards. Half-renamed is worse than either end state.
- **Search for the old names — then read the places search can't reach.** A room name also exists as a display string with no entity id in it, and no search for a retired id will ever find the button that passes that string to a script.
- **Know which rename mechanism your platform wants.** Registry-owned and config-owned entities rename differently, and using the wrong one silently produces suffixed duplicates.

## How to verify

- For your ten most-referenced entities, you can name the physical device without a caveat. A caveat is the defect.
- No entity's human-readable label contradicts its id about which room or device it is.
- A search for every retired name returns nothing — and you've hand-checked the display-string sites the search can't see.
- Someone else, or an AI assistant, reading only your config, describes each name's physical referent correctly.

Related: [naming as infrastructure](../building/naming.md) for the full convention and the rename mechanics, [entity-id drift](../building/pitfalls.md) for what happens when a platform renames something underneath you, and [automate against reality, not labels](automate-against-reality-not-labels.md) — the same disease one layer down, in the wiring.
