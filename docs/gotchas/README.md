# Gotchas

Every one of these started as a problem that cost real time and taught a rule worth keeping. They're written generalized on purpose — the specific gear doesn't matter, the principle does. The bar for every lesson here is the same: *would this help a stranger with a different house?*

Each lesson follows the same shape — TL;DR, the situation, what bit me, the general rule and why it holds, how to apply it, and how to verify you got it right — so you can scan for the rule and dig in only where you need to.

| Lesson | TL;DR | Link |
|--------|-------|------|
| Automate against reality, not labels | The names on inputs and zones describe someone's intent, not your wiring — automate on what's physically connected. | [Read](automate-against-reality-not-labels.md) |
| Idempotent commands | Every command should be safe to send twice; check state first and prefer discrete commands over toggles. | [Read](idempotent-commands.md) |
| Prove the riskiest integration first | The one integration nobody has a clean path for sets your whole timeline — isolate and prove it before planning the rest. | [Read](prove-the-riskiest-integration-first.md) |
| Migrate in parallel, never big-bang | Keep the old system live, move one zone at a time, always have a same-day rollback — the family is your acceptance test. | [Read](migrate-in-parallel-never-big-bang.md) |

More are coming as the migration behind this guide gets written up — and as others contribute theirs. Got a hard-won lesson? [Add yours](../../CONTRIBUTING.md).
