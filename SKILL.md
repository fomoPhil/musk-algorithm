---
name: musk-algorithm
description: Use when planning, scoping, or reviewing any feature, system, process, pipeline, or automation; before optimizing, accelerating, or automating anything; when a request arrives as a fully specified component list; when scope is ballooning or a design feels over-engineered; or when the user asks to run the algorithm, the five steps, or first principles on something.
---

# Musk Algorithm

## Overview

Elon Musk's five-step algorithm for improving anything, run in strict order. Core principle: "The most common mistake of smart engineers is to optimize a thing that should not exist."

The order IS the algorithm. Skipping ahead means polishing, speeding up, or permanently locking in things that should have been deleted. Musk: "I've gone backwards so many times where I've automated something, sped it up, simplified it, and then deleted it."

## When to Use

- The user proposes a feature, system, process, or automation, especially a fully specified one ("add X with Y and Z, then run it on a schedule")
- Before writing any plan or design doc, before optimizing anything, before any cron job, scheduled task, CI step, or other standing automation
- Reviewing an existing feature, pipeline, or process that feels heavy
- NOT for: bug fixes, trivial edits, tasks with no scope decisions

## The Five Steps (strict order)

**1. Question every requirement.** Restate the underlying problem in one sentence before touching the component list. Every requirement gets a named owner and observed evidence. "Best practice," a tutorial, an old design doc, a previous conversation, or "everyone does it" is not an owner. The user naming a component is not evidence that component solves their problem; ask what they actually observed. Requirements from smart sources are the most dangerous because nobody questions them. The ownership test works for solo builders too: ownership is about where a requirement came from, not who builds it. A solo builder citing a real observation (their own use, a named user's ask) is a valid owner. A competitor's screenshot, a tutorial, or a fear of missing out owns nothing.

**2. Try to delete.** For each surviving component, ask: what breaks if this does not exist? Actually propose deletions; adding back later is cheap. If the deletion list is empty, the pass did not happen. Target enough deletion that roughly 10% has to come back later. Common delete targets: scoring or judging layers where a human already reviews the output, logging and tuning infrastructure for unproven features, configuration surface, abstraction layers with one caller, "for later" hooks.

**3. Simplify and optimize.** Only now improve what survived. Any optimization idea for a component that has not passed steps 1 and 2 is the trap the whole algorithm exists to prevent.

**4. Accelerate the cycle.** Speed up only what deserves to exist. The cycle that matters is idea to real feedback: a build in users' hands, real usage, real results. When code is cheap to produce (as with AI-assisted development), raw build speed is rarely the bottleneck; the feedback loop is.

**5. Automate.** Last, always. Automation locks a process in and makes it invisible. A process earns a cron job, scheduled task, or CI step only after the manual loop has run enough times to prove it deserves permanence. Tesla automated the Model 3 line before steps 1 through 4 and had to rip the robots back out.

## Output Format

Lead with the verdict: what should exist and what got deleted, in 2 to 3 sentences. Then a short table, one row per step, with the finding. End with exactly one recommended next action, not a menu.

When the target is an existing product or project with many components (rather than a single proposed feature), also produce a component table: one row per component, with columns for what it is, where it stands (working, half-built, not started), and the verdict (keep, finish, cut, don't start). Plain language in every cell; the reader may not be technical. This table is usually the clearest artifact the run produces, and the per-step findings exist to back it up.

## Red Flags (stop, return to step 1)

- Opening with "your plan is sound" before any deletion attempt
- Producing phases or architecture for every requested component
- Improving a component (better scoring, better schema, better caching) before asking whether it should exist
- Adding logging, tuning, or config infrastructure to a feature that has not proven value
- "There's a manual gate, so the automation is safe." Safe is not the bar; should-exist is the bar.
- "While we're at it"

## Rationalizations

| Excuse | Reality |
|---|---|
| "The user explicitly asked for all of it" | Requirements are always dumb to some degree, whoever wrote them. Question with evidence, not obedience. |
| "Deleting their ideas feels disrespectful" | Letting someone build and maintain a thing that should not exist wastes their actual scarce resources: time and attention. |
| "This optimization is objectively better" | Better at existing is irrelevant if it should not exist. |
| "It's already built, so just improve it" | Sunk cost is exactly how the order gets violated. Deleting built things is still cheaper than maintaining them. |
| "I ran step 1 mentally, it all passed" | If nothing got questioned by name and nothing got deleted, the steps did not run. |

For the source quotes and case stories (fiberglass mats, deleted radar, falcon-wing doors, the over-automated Model 3 line), read references/stories.md.
