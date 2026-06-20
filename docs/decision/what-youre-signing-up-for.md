# What you're signing up for

If [why-leave-control4](why-leave-control4.md) is about *whether* to go, this page is about the bill. Not to scare you off — to make sure you walk in with your eyes open. The people who abandon a migration halfway aren't the ones who lacked skill. They're the ones who underestimated the cost in the column they didn't look at.

Here's the honest ledger: time, skills, money, and the household.

## Time: where the hours actually go

The work is not evenly distributed, and it does not go where beginners expect. Plan against the real shape of it:

- **Discovery is slow and feels like nothing.** Walking the house, tracing wires, confirming how things are actually connected — it produces no working features and it is the highest-leverage time you will spend. Budget real days for it. Skipping it doesn't save time; it moves the time to later and multiplies it.
- **The hard part eats a disproportionate share.** One integration — the thing nobody has a clean path for — will consume more hours than several easy zones combined. This is normal and correct. It's why you [prove it first](../framework/02-prove-the-hard-part-first.md): so the unpredictable cost lands early, not at the end.
- **The easy zones are genuinely easy** once the hard part is solved and your patterns are established. They go fast, in a satisfying rhythm.
- **The last 10% hides.** Polish — clean fallbacks, consistent behavior, the small annoyances that a professional install smoothed over — takes longer than you think. The system "works" long before it feels finished.

A whole-home migration is a project measured in months of evenings, not a weekend. Anyone who tells you otherwise is selling something or hasn't done one.

## Skills: day one versus learn-as-you-go

You do **not** need to be an expert to start. You need a floor, and a willingness to climb.

**Need on day one:**
- Basic home networking — you can find a device's IP, understand what a subnet is, and aren't afraid of your router.
- Comfort reading and editing structured text (config files). Not programming — editing.
- Patience for methodical troubleshooting: change one thing, observe, repeat.

**Learn as you go (and you will):**
- The specific control protocols your devices speak.
- How your chosen platform models entities, automations, and state.
- The art of making things idempotent and fault-tolerant — you'll learn this by getting burned, which is fine, because in parallel operation the burns are survivable.

The line that matters isn't current skill. It's whether learning this sounds like the point or like a chore. See [is this for you?](is-this-for-you.md).

## Cost: the math, honestly

The money story is better than people expect, but it's not instant.

- **What you'll spend:** a controller/server to run the new platform, possibly some bridges or interfaces to reach older devices, and your time (which is not free — count it).
- **What you'll stop spending:** every service call, every "send someone out to change a thing," and any dealer-managed licensing or subscriptions the old system carried.
- **When it turns positive:** on an *active* system — one you change often — the recurring fees you stop paying overtake the hardware outlay within a small number of years, sometimes much faster. On a system you never touch, the math is weaker, which is itself a signal you may not need to migrate at all.

The real return isn't the dollars. It's that changes cost minutes instead of money-plus-scheduling. But don't pretend the upfront cost is zero.

## The family acceptance factor

This is the column that sinks migrations, and it has nothing to do with technology.

The household did not sign up for a project. They signed up for a house that works. The day the kitchen won't play music during dinner, or the living room is dark when someone wants to watch a game, your migration acquires an opponent — and it's the person you live with.

This is why [zero family disruption is a hard requirement](../framework/principles.md), not an aspiration. It's the reason the old system stays live through [parallel operation](../framework/03-parallel-operation.md), the reason you migrate one zone at a time, and the reason every change needs a same-day rollback. Get this wrong and the most technically successful migration still fails, because the people who live there have lost faith in it.

Account for it like the real cost it is: your political capital at home is finite, and you spend it every time something the household relied on stops working. Spend it carefully.

## The bottom line

You're signing up for months of evenings, a learning curve you'll mostly climb mid-flight, a real-but-recoverable upfront cost, and a standing obligation to never break the house. In return you get a system that is genuinely yours.

If that ledger reads as a fair trade, the next question is whether your specific situation fits — that's [is this for you?](is-this-for-you.md).
