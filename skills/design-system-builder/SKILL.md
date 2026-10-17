---
name: design-system-builder
description: Coordinator for the three-stage Figma design system pipeline (tokenize → componentize → documentation). Use whenever the user wants to build, continue, or check progress on a design system in Figma end to end, says "run the pipeline", "build the design system", "what stage is my file at", "next stage", "continue the design system", or pastes a design system handoff note. Decides which stage the file is ready for, runs ONE stage at a time through the existing skills, verifies the result, and writes a handoff note so the next stage can start cleanly, in this chat or a fresh one. Does not replace the three stage skills; they stay the source of truth.
---

# Design System Builder (coordinator)

This skill contains no tokenizing, componentizing, or documenting logic. It decides which stage to run, hands off to that stage's own skill, checks the result honestly, and writes a handoff note. All design rules (naming, binding, variants, doc layout) live in the stage skills.

The stages, in order:

1. `tokenize` — exploratory frames → Figma Variables (Primitive + Semantic)
2. `componentize` — tokenized element → variant-complete, token-bound component set
3. `documentation` — component sets → documentation frames, plus the token library pages

## Core rule: one stage per run

Run exactly one stage, verify it, write the handoff note, then stop and ask whether to continue. Never begin the next stage's writes in the same turn that finishes the previous one.

Why: chaining stages in a single run is where bugs come from. Instructions from one skill bleed into the next, the context fills up with tool output, and an early mistake silently becomes the foundation for everything after it.

The user may say "continue" after a stage. Continuing in the same chat is allowed only if the stage finished with zero unresolved errors and the chat is still light. If the chat has been long or the previous stage was heavy (thousands of nodes touched, many retries), recommend a fresh chat and give the handoff note to paste.

## Step 1: Orient (read-only)

Before any write:

1. Get the Figma file link. If the user pasted a handoff note, read it first and trust its "what exists" section as a starting point, but confirm against the file.
2. Load the Figma `figma-use` skill before any `use_figma` call.
3. Do a read-only inspection and report what you find:
   - Variable collections present (expect `Primitive` and `Semantic` after stage 1), and rough binding coverage
   - Whether component sets exist, and whether they are bound to variables rather than hardcoded values
   - Whether documentation frames and token library pages exist
4. State the stage the file is ready for and why. If the user asked for a specific stage but its prerequisite is missing or incomplete, say so plainly and offer to run the earlier stage first. Do not skip ahead silently.

## Step 2: Run the stage

1. Tell the user which stage skill you are about to use and what scope it will touch (page, selection, or whole file).
2. Read that stage skill's SKILL.md and follow it exactly. Do not blend in instructions from the other stage skills.
3. Do not delete existing variables, collections, components, or pages unless the user explicitly asked. Never ship cleanup code that you have not shown the user.
4. Large writes (mass rebinding, many nodes in one call) need an explicit, unambiguous user confirmation before they run. Prefer batches over a single huge call.
5. If an error occurs, stop. Report what failed, what already changed, and the options (retry, skip, undo). Do not push on to the next step hoping it resolves. When retrying, re-check that no duplicate collections or components were left behind by the failed attempt.

## Step 3: Verify before saying "done"

Report what actually happened, not what was intended. Include:

- What was created or changed (counts where available)
- What was skipped, left unbound, or only partly done
- Any errors hit and how they were resolved
- Anything that needs the designer's eye before the next stage (ambiguous choices, near-duplicates, naming decisions)

Do not describe a stage as complete if there are unresolved items. Say "complete except for X" and list X.

Quick checks by stage:

- After `tokenize`: both collections exist, names follow the tokenize skill's naming rules, no duplicate collections, count of nodes still using raw values
- After `componentize`: component set exists, every variant is token-bound, variant property names are consistent, nothing outside the selection was altered
- After `documentation`: a doc frame exists for each component set on its own category page, token library pages exist, page order matches the expected structure

## Step 4: Write the handoff note

At the end of every stage, produce this note in the chat (and offer it as a file if the user wants one). Keep it short and factual.

```
# Design System Handoff — [file name or brand]

## File
[Figma link] (page / scope touched)

## Status
Stage completed: [tokenize | componentize | documentation]
Next stage: [name]

## What exists now
- Collections / components / pages created, with the key names
- Counts that matter (e.g. bindings made)

## Decisions made
- Choices the designer confirmed (naming, merges, what was left out and why)

## Known leftovers
- Anything skipped, unbound, or needing review

## Gotchas hit
- Anything that went wrong and the fix, so it isn't repeated

## To continue
Paste this note into a new chat and say: "Run the next stage of the design system builder."
```

## Known gotchas to carry forward

These have come up in real runs; mention them in the handoff note when relevant:

- Figma can silently substitute fallback fonts for glyphs the real fonts lack (symbols, stamps). Preload fonts per text segment (`getStyledTextSegments(['fontName'])`), not only the node-level font, or variable binding on text can fail with `unloaded font`.
- A mass rebind touching thousands of nodes may be blocked by the permission classifier the first time. It needs an explicit user confirmation to retry, not a smaller version of the same action.
- When creating collections instead of extending existing ones, make sure cleanup of orphaned collections is part of every retry, or duplicates are left behind.

## Tone

Be plain and precise. No inflated claims about coverage or quality. If something is uncertain, say so and say how to check it.
