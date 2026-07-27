# Deploys invalidate open clients

**TL;DR:** Reloading configuration silently no-ops dashboards that were already open — a dead button is indistinguishable from a broken script, so check whether the action ever fired before you debug the action.

## The situation

You deploy a configuration change and reload it. The reload succeeds. The system is healthy, the logs are clean, and every automation you wrote is loaded and correct.

Somewhere in the house, a browser tab has been open since before the reload. It is showing an interface built against the configuration that existed when the page loaded, and it has no idea anything changed. On a laptop this is invisible, because you reflexively refresh. On a wall-mounted tablet that holds its session for days and that nobody ever touches except to tap a button, it is not invisible — it is the default state.

## What bit me

Scripts were reloaded after a deploy. Twenty-two minutes later, a dashboard tap did nothing at all. No error on screen, no entry in the log, and the script's last-triggered timestamp had not moved — the action was never invoked. From the outside it looked exactly like a broken script, and that is where debugging started: reading the script, checking its conditions, questioning a change that was in fact correct.

The same tap worked immediately after the page was reloaded. Nothing else changed.

The tablet on the wall was the likeliest victim precisely because it works so well the rest of the time. It holds a session for days, and a session that old is guaranteed to predate the last several deploys.

## The general rule

**A reload invalidates references held by already-open clients.** The client is not wrong and the server is not wrong; they are describing different generations of the same configuration, and neither of them notices.

What makes this expensive is the signature. The failure presents as *silence* — no error, no log line, no toast, no visible difference from a person who did not press anything. Silence is the hardest failure to attribute, and the natural instinct is to suspect the most recently changed thing, which is the code you just deployed and which is usually fine.

## How to apply it

- **Refresh every open client after deploying and reloading.** Make it the last step of the deploy, not something you remember when a button misbehaves. Always-on tablets first — they are the ones nobody else will refresh.
- **When a control "does nothing," check the last-triggered timestamp before anything else.** If it never moved, the action was never invoked and the problem is the client, not the automation.
- **Distinguish "ran and failed" from "never ran."** They look identical to a person standing at the wall and require opposite investigations.
- **Do not start editing working code on the strength of one silent failure.** Confirm the action fired first.
- **Consider a scheduled client refresh** for kiosks and wall panels, so session age is bounded whether or not anyone remembers the deploy.

## How to verify

- After a deploy, tapping a control on the wall tablet moves its last-triggered timestamp. If it doesn't, the client is stale.
- Your deploy routine has an explicit client-refresh step, and it names which clients.
- You can state, for the last silent failure you investigated, whether the action fired — because you checked before you started reading code.

Related: [the family interface](../building/family-interface.md), where this is one of several failure modes specific to the surface everyone else uses.
