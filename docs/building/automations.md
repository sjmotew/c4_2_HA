# Automations

If [scripts](scripting.md) are the verbs — the sequences you run — automations decide *when* to run them. The danger is unique to automations: they react to state, and state includes the state they themselves change. Get that wrong and an automation chases its own tail, or leaves the house in two contradictory states at once.

## Enforce a single active source

In a whole-home system, two sources playing at once is almost never what anyone wants. Make it structurally impossible: **selecting a new whole-home source stops the others first.**

```yaml
# Illustrative — choosing a whole-home source stops the others first (single active source).
automation:
  - alias: "Single active source"
    trigger:
      - trigger: state
        entity_id: input_select.house_source
    action:
      - action: script.stop_all_sources           # stop current before starting new
      - action: script.turn_on
        target:
          entity_id: "script.source_{{ states('input_select.house_source') }}"
```

Stop-then-start is the safe order. The new source never has to coexist with the old one, so you never get the "why is the patio still playing?" call.

## Design triggers and conditions to be idempotent

Assume every automation can fire **more than once** — because in a real house, it will. Guard it so a second firing does no harm: condition on current state before acting, so an automation that runs twice doesn't undo itself or stack up commands.

**Why it holds:** this is [idempotency](../framework/principles.md) at the automation layer. Events get duplicated, triggers get noisy, and you press buttons while automations run. An automation that's only correct when it fires exactly once is a bug waiting for a busy evening.

## Avoid feedback loops

The classic trap: an automation that **triggers on a state its own action changes.** It fires, changes the state, which fires it again. Break the loop with a condition that stops the re-fire, or by triggering on something the action doesn't touch. If an automation ever seems to "run forever," look here first.

## Avoid double-toggling

Before flipping a device, **check whether it's already in the target state.** Don't turn off a TV that's already off — especially where "off" is destructive (some devices disable their own network control on a naive power-off; see the gotchas). Condition on current state, then act only if needed.

## Pitfalls

- **Triggering on a state your own action sets** — the infinite loop.
- **Blind toggles** that turn off already-off (or destructively-off) devices.
- **Unguarded repeats** — firing volume or source changes without a condition, so duplicates stack up.

## See also

- [Principles](../framework/principles.md) — idempotency, zero disruption.
- [Scripting](scripting.md) — the actions automations call.
- Gotchas: [idempotent commands](../gotchas/idempotent-commands.md), [automate against reality, not labels](../gotchas/automate-against-reality-not-labels.md).
