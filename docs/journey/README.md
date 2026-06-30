# The Journey

Here's the actual arc, compressed — proof the framework came from a real migration, not theory. The four-stage model in `../framework/` wasn't designed on a whiteboard; it's the shape this project took once it was done, looking back at what actually worked. Below is that arc mapped onto the stages, in generalized terms: no zone-by-zone diary, no gear list, just what each stage looked like in practice and the lesson it left behind.

## Timeline

| Stage | What I did | What I learned |
|-------|------------|----------------|
| Discovery & inventory | Walked the whole system before touching anything — cataloged every device, traced every signal path, and wrote down which zones depended on which boxes. Discovered the documentation I'd inherited didn't match the wiring. | The map is never the territory. The single most valuable artifact of the whole project was an accurate, self-made inventory — and building it surfaced surprises that would have blown up later if found mid-cutover. |
| Prove the hard part first | Picked the one integration with no obvious clean path and built it in isolation before committing to a timeline. Refused to plan the easy zones until the scary one actually worked. | The riskiest integration sets your schedule whether you acknowledge it or not. Proving it early turned an unknown into a known and let me plan the rest with confidence instead of hope. |
| Parallel operation | Ran the old system and the new one side by side, migrating one zone at a time, and kept a same-day rollback ready for every move. The house stayed fully usable the entire time. | Nobody at home should be able to tell a migration is happening. Per-zone moves with instant rollback made the whole thing boring — which, for the people who actually live in the house, is exactly the goal. |
| Cutover & decommission | Retired the old system only after the new one owned every zone and had quietly run them for a while with no complaints. Kept a short list of things worth preserving; let the rest go. | "Done" isn't when the new system works — it's when the old one can leave without anyone noticing. Earning that confidence took patience, and rushing it would have undone all the trust built in the parallel phase. |

## Decisions that shaped it

A few choices did more than their share of the work, and they're worth lifting out of the timeline:

- **A status-coded signal-path matrix as the single source of truth.** Every zone's source → path → amp → speaker chain went into one living document, each entry tagged *confirmed*, *legacy-dependent*, *unused*, or *migration-ready*. The rule that made it useful: an "unknown" is a blocker, and you don't cut over until the unknown count is zero.
- **Proving the old controller was an orchestrator, not a router.** A controlled power-off test (now its own [gotcha](../gotchas/power-off-test-the-old-controller.md)) turned a scary decommission into a small, known list of real work — early, before the plan was built around a worst case nobody had confirmed.
- **Deferring true multi-stream on purpose.** Playing different sources in different rooms at once — the full multi-zone experience — was consciously postponed. The honest ceiling is the matrix's input count, not the number of endpoints, and real household demand looked like two or three room clusters, not N. The plan was to *measure* how often two sources were actually wanted before spending on hardware, and to add independent streams additively later. Deferring a feature you can add cleanly later is a legitimate decision, not a failure.

## What I'd do differently

- **Build the inventory even more obsessively, and earlier.** Almost every nasty surprise traced back to a gap in what I thought I knew about the wiring. Time spent here pays back many times over.
- **Resist the urge to do the fun, easy zones first.** It feels like progress, but it postpones the integration that actually determines whether the whole plan is viable. Front-load the risk.
- **Write things down the same day, not "later."** The details that matter most — the odd workaround, the why behind a choice — evaporate within a week. This guide exists only because some of it got captured in time, and is thinner where it didn't.

Want the play-by-play? It's growing — and you can [add yours](../../CONTRIBUTING.md).
