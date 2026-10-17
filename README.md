# Design System Builder Skills

A set of Claude skills that build a design system in Figma in three stages, plus a coordinator skill that runs them one stage at a time.

| Stage | Skill | What it does |
|-------|-------|--------------|
| 1 | `tokenize` | Turns exploratory Figma frames into a structured Figma Variables token system (Primitive + Semantic) |
| 2 | `componentize` | Turns a tokenized element into a variant-complete, token-bound component set |
| 3 | `documentation` | Documents component sets and builds the token library pages |
| — | `design-system-builder` | Coordinator: checks which stage the file is ready for, runs ONE stage, verifies, writes a handoff note |

## Requirements

- Claude with Skills enabled (code execution and file creation on)
- The Figma connector enabled for each user
- All four skills installed. The coordinator hands work to the three stage skills, so it does not work without them.

## Install

**In Claude:** upload each `.skill` file from `dist/` (Skills > upload a skill), or have an org owner provision them. Install or share all four together.

**Source:** each skill's source is in `skills/<name>/SKILL.md`. `dist/` holds the packaged `.skill` files built from those folders.

## How to use

1. Start a chat and say: "Build the design system for this file" plus the Figma link.
2. The coordinator inspects the file (read-only), tells you which stage is next, and runs that stage.
3. After each stage it reports what was created, skipped, and any errors, and writes a handoff note.
4. Review, then reply "continue". For heavy stages, start a fresh chat and paste the handoff note.

The coordinator deliberately runs one stage at a time. Running several skills back to back in one run has produced bugs, so each stage ends at a checkpoint.

## Updating the skills

- Edit in this repo, not in the installed copies.
- Update the four skills together. If the coordinator and a stage skill get out of sync (e.g. a renamed stage or a changed handoff format), runs can break.
- After editing, repackage the changed skill into `dist/`, bump the version in `CHANGELOG.md`, and tag a release.
- Re-upload or re-publish to Claude, and tell the team. Start a fresh chat after an update so the new version is the one in use.

## Owner

Add the name of the person responsible for keeping the four skills in sync here: _TBD_
