# Principles

The 4-stage model tells you *what order* to do things in. These principles cut across all four stages — they're the rules that, once internalized, keep you out of the most common and most painful holes. Each one is stated as a short rule with the reasoning behind it, because a rule you understand is a rule you'll actually follow.

## Automate against what's connected, not vendor labels

Model your automations on physical reality, not on the names a device shipped with. The label on an input or a zone is whatever someone typed once — it may not match what's actually plugged in, and it definitely won't match what's plugged in after you rearrange things.

**Why it holds:** the moment you automate against a label instead of the real connection, you've built on a fiction. It works until the label and the wiring disagree, and then you're debugging a problem that doesn't exist in the place you're looking. Audit every default name against what's physically there, and rename things in your own control layer to describe reality.

## Idempotency or bust

Every command you send should be safe to send twice. Sending "turn on" to something already on should do nothing harmful; sending it again after a failed first attempt should just work.

**Why it holds:** in a real house, commands get lost, repeated, and raced. Automations fire while you're also pressing buttons. If a command is only safe exactly once, you will eventually fight your own system — toggling something off that you meant to keep on, desyncing state, chasing ghosts. Check state before you act, prefer discrete commands ("set to on") over toggles ("flip it"), and your system stops surprising you.

## One source of truth per concern

For any given thing your system controls, there should be exactly one place that defines its behavior. Don't let the same setting live in two files, two layers, or two systems where one can silently override the other.

**Why it holds:** duplicate configuration is a landmine that goes off weeks later. You change the obvious place, the behavior doesn't change, and you lose an afternoon before discovering a second definition was quietly winning. The failure isn't the duplication itself — it's that the override is *silent*. Keep one authority per concern and the system stays explainable.

## Human-readable names over indices

Name things by room and function — what a person would call them — not by zone numbers, port numbers, or raw API indices.

**Why it holds:** indices are an accident of how the hardware enumerates itself, and they drift. Six months later, "zone 4, input 2" tells you nothing and might not even be the same thing anymore. A name like "kitchen, turntable" survives reorganizations and is readable by anyone — including future you at 11pm trying to remember why something broke. Names also catch mistakes: a wrong name is obvious in a way a wrong number never is.

## Zero family disruption is a hard requirement, not a nice-to-have

The household must not notice the migration in progress. Treat that as a non-negotiable constraint, not an aspiration you'll honor when convenient.

**Why it holds:** this single requirement dictates your entire sequencing. It's *why* the old system stays live through parallel operation, *why* you migrate one zone at a time, *why* every change needs a same-day rollback. The day you let "I'll fix it tonight" become acceptable is the day you lose the household's trust — and that trust is the actual currency of a successful migration. Protect it like the scarce resource it is.

## Write it down as you go

Document decisions, discoveries, and fixes in the moment, not "later." Later never comes, and the detail you'll need most is the one you were sure you'd remember.

**Why it holds:** your future self is the first and biggest beneficiary. The dependency you uncovered, the workaround that finally worked, the reason you chose one approach over another — six months on, that's all gone unless you wrote it down. This very guide exists because the migration behind it was documented as it happened. Writing it down is also how a private project eventually becomes something that helps a stranger. Do it as you go.
