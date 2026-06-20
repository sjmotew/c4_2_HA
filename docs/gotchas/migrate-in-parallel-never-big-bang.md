# Migrate in parallel, never big-bang

**TL;DR:** Keep the old system live, move one zone at a time, and always have a same-day rollback — the family is your real acceptance test, and they must never notice the migration in progress.

## The situation

You've done your discovery, you've proven the hard part, and now you're ready to actually move the house onto the new system. There's a powerful temptation here: rip the old system out in one decisive weekend, stand up the new one, and be done. One clean break. It feels efficient, it feels brave, and it promises to end the awkward in-between period quickly. Everything in you wants to just do it all at once.

## What bit me

The temptation is a trap, and you can reason your way to why without having to live through it.

A whole-home system has more hidden dependencies and edge cases than any plan accounts for — discovery surfaces most of them, never all of them. A big-bang cutover surfaces every one of those surprises *at the same time*, with the old system already gone, in a house full of people who expected things to keep working. You're now debugging multiple unrelated failures simultaneously, under pressure, with no fallback, while the household discovers one broken thing after another. The pressure makes you sloppy, the lack of a fallback makes every mistake expensive, and the audience makes the whole thing worse. It's the single fastest way to lose the household's trust — and that trust is the thing the entire migration depends on.

The few times I was tempted to move faster than one-zone-at-a-time, the rough edges I'd have lived with quietly instead became things other people noticed immediately. That's the difference between a problem and a crisis.

## The general rule

**Migrate incrementally with the old system as a live safety net, and treat zero disruption as a hard requirement.** Move one zone, live with it, fix what's rough while the old system still covers everything else, and only then move the next. Never remove your fallback until the new system has earned its place zone by zone.

This holds because incremental migration shrinks the blast radius of any failure to a single zone, and a live old system means every failure is recoverable the same day. Surprises become contained and survivable instead of simultaneous and catastrophic. And the zero-disruption requirement isn't politeness — it's what forces the sequencing that keeps you safe. The household not noticing *is* the proof that a zone is genuinely done.

## How to apply it

- **Run both systems at once.** Keep the old system fully operational throughout the migration. It's your safety net, and it stays up until the very end.
- **Move one zone at a time.** Migrate a single zone, then stop. Don't start the next until the current one has been lived with and is solid.
- **Keep a same-day rollback for every change.** Before you touch a zone, know exactly how to return it to the old system today if something goes wrong. If you can't roll back quickly, you're not ready to make the change.
- **Pick the order deliberately.** Start with a low-stakes zone to build confidence and shake out your process; save the zones the household depends on most for when you're sure of yourself.
- **Use the household as your acceptance test.** A zone isn't done when it works for you in a test. It's done when the people who use it daily haven't noticed anything changed.
- **Resist the clean-break urge.** The weekend rip-out feels efficient and is the highest-risk thing you can do. Boring and incremental wins.

## How to verify

- At any point during the migration, you can name the single zone you're currently working on — and it's only one.
- For the zone in progress, you can describe the exact steps to roll it back to the old system today.
- The old system is still fully functional and could take back any zone immediately.
- The household has not reported a single disruption they traced to the migration — and you only consider a zone done once that's true.
