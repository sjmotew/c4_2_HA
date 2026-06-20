# Prove the riskiest integration first

**TL;DR:** The one integration nobody has a clean, documented path for is the one that sets your entire timeline — isolate it and prove it works before you plan anything around it.

## The situation

A whole-home migration is mostly made of integrations you can be reasonably confident about. Common devices, common protocols, plenty of people who've done it before — you can look those up when you get to them. But there's almost always one that's different: a source, a device, or a routing requirement that doesn't have a clean path, where the forum threads end in "I never got it fully working" or contradict each other. It's tempting to start with the easy, satisfying wins and leave the scary one for "later," once you've got momentum.

## What bit me

I sequenced by comfort instead of by risk.

I built out the parts I understood first, because they were quick and felt like progress. By the time I got to the hard integration, I'd already made plans, set expectations, and committed to an order that assumed it would just work like everything else. It didn't. The approach everyone seemed to recommend turned out to be a dead end for my case — it simply didn't support what I needed, and no amount of persistence was going to change that. The real solution was a different path entirely, one I'd have found much sooner if I'd gone looking for it first.

The cost wasn't only the time spent on the dead end. It was that every plan built on top of the assumption had to be reconsidered. The hard part didn't just take longer than expected — it invalidated decisions I'd already made because I'd treated it as a known quantity when it was actually the biggest unknown in the project.

## The general rule

**The riskiest integration sets your timeline, so it has to be resolved first.** The easy parts have predictable durations — you can estimate them and you'll be roughly right. The hard part has an unknowable duration until you've actually proven it, because you don't yet know whether the obvious approach even works. Anything you plan before resolving that unknown is planning on a guess.

This holds because risk doesn't shrink by being ignored; it just moves to a worse moment. Resolved early, the hard part costs you a contained spike before you've committed to anything. Resolved late, it costs you the spike *plus* every decision you built on the assumption that it would be easy.

## How to apply it

- **Name the riskiest integration explicitly during discovery.** Ask: which one thing, if it can't be made to work, changes the whole plan? That's the one. There's usually exactly one obvious candidate — trust your unease.
- **Spike it in isolation, before the rest of the plan exists.** Strip it down to the single question "can this work, and how?" and answer that on its own, disconnected from the larger system.
- **Distrust the popular answer until you've confirmed it for your case.** The most-recommended approach may not support what you specifically need. Verify it does before you depend on it.
- **Define "proven" up front.** Proven means it works reproducibly and you understand *why* — not that it worked once by luck you can't explain.
- **Let the result drive the rest of the plan.** Only after the hard part is settled do you sequence everything else around it.

## How to verify

- You can state, in one sentence, which integration is the riskiest and why.
- That integration works on demand, repeatably, and you can explain the mechanism that makes it work.
- You confirmed the approach you're depending on actually supports your requirement, rather than assuming the common recommendation applies.
- No part of your broader plan depends on an unproven assumption about the hard integration — by the time you planned, it wasn't an assumption anymore.
