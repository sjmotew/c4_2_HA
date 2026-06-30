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

## Keep your state truthful, however playback starts

People start audio in ways your scripts never see — a native media browser, a phone casting directly to a player. If your [source-of-truth helpers](architecture.md) only get written by your own scripts, they go stale the moment someone bypasses them, and your dashboard starts lying.

The fix is a **reactive automation** that watches the player and writes the source helper by inspecting the player's attributes — so the UI stays honest no matter how playback began. Two details make it robust:

- **Detect the provider by a stable attribute** (a `media_content_id` prefix, say), not a transient field — and if you don't recognize the provider, *leave the helper unchanged* rather than guessing.
- **Debounce the "stopped" transition.** Flip the helper to "nothing playing" only after the player has been idle `for:` a short window, so a brief pause between tracks doesn't clear your state.

## Re-apply the group after out-of-band playback

When someone starts playback directly on the streaming player — a native browse, a cast handoff — your room group never got applied, so the audio reaches only that one player. Catch it: trigger on the player entering "playing" **and** on its `media_content_id` changing (so successive browse picks, which don't re-transition the state, still fire), then re-apply the active group by calling your idempotent `room_add` scripts, each gated on its room boolean.

Make this automation **self-sufficient** — have it apply the group itself rather than depending on another automation whose gate might be false in the cast case. And hold each trigger with a short `for:` to de-dupe it against the source-detection automation watching the same entity.

## Reconcile state instead of trusting it

Helper booleans and device "on" states drift from reality: a room shows active but plays nothing; a display reports "on" in two different modes. Don't build a toggle on a single state you haven't confirmed (see [avoid double-toggling](#avoid-double-toggling)). Where a device's own state is unreliable, **use a companion signal as a proxy** — a downstream device that's only on when the thing really is — and consider a periodic reconcile that clears "phantom active" rooms. This is [automate against reality, not labels](../gotchas/automate-against-reality-not-labels.md) at the automation layer.

## Pitfalls

- **Triggering on a state your own action sets** — the infinite loop.
- **Blind toggles** that turn off already-off (or destructively-off) devices.
- **Unguarded repeats** — firing volume or source changes without a condition, so duplicates stack up.
- **State helpers only your scripts write** — they go stale the instant someone starts playback another way.
- **Guessing a provider you don't recognize** — leave state unchanged instead of writing a wrong value.

## See also

- [Architecture](architecture.md) — the shared state these automations keep truthful.
- [Principles](../framework/principles.md) — idempotency, zero disruption.
- [Scripting](scripting.md) — the actions automations call.
- Gotchas: [idempotent commands](../gotchas/idempotent-commands.md), [automate against reality, not labels](../gotchas/automate-against-reality-not-labels.md), [one control authority per device](../gotchas/one-control-authority-per-device.md).
