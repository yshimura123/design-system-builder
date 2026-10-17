---
name: "tokenize"
description: "Turns a designer's exploratory, not-yet-tokenized Figma frames for a net-new brand into a structured Figma Variables token system, flagging ambiguity (including same-role duplicates across separate clusters) as multiple-choice questions, each with a custom-answer fallback, and always writing lowercase variable names, before writing anything. Also builds Figma Text Styles from already-tokenized type variables on request, bindable to matching text nodes with exact or confirmed near-match logic."
---

# Tokenize Selection (frame-level review + contextual ambiguity + text styles)

> Designers explore a new brand visually first — ad hoc colors, type, and spacing,
> no formal token system. This skill is the formalization step: scan what they
> actually used, flag what's ambiguous, propose a token structure, and write
> Figma Variables once the designer has confirmed both the ambiguous cases and
> the naming/layering convention. It never decides silently.
>
> Once type is already tokenized (either by this skill or beforehand), the
> same skill can also build and bind Figma **Text Styles** from those type
> variables — see Phase 7 — since that's a natural, frequently-requested next
> step once the underlying tokens exist, and it has its own ambiguity and
> confirmation needs that deserve the same rigor as tokenization itself.

---

## Quick Start

**What to say:** "Tokenize this" / "Turn these frames into tokens" / "Tokenize the whole file" / "Build text styles from my type tokens" — you don't need to know an exact phrase; the skill asks what scope you mean before doing anything.

**What you need:** Already-built (but not yet tokenized) exploratory work for a net-new brand somewhere in this file — either selected on canvas, or anywhere in the file if you want a whole-file run. For the text-styles path (Phase 7), you instead need type that's **already** bound to Figma Variables.

**What you get, in order:**
1. An upfront question on scope — your current selection, or the whole file across every page (Phase 0, Step 1)
2. A confirmation of which naming convention to follow, based on anything already in the file (Phase 0, Step 2)
3. A scan report of every distinct color, type style, and spacing value in use across the confirmed scope, built from an exhaustive node-by-node read — never a summarized or sampled one (Phase 1) — **skipped if the designer says type is already tokenized and asks only for text styles; see Phase 7**
4. Ambiguity review grouped by how much it matters: clusters used only once or twice get bundled into one batch-approval list per frame, while anything used repeatedly gets its own two-tier canvas annotation — a frame-level `Tokenization Review` decision point, plus per-instance `Inconsistent Colors`/`Inconsistent Type`/`Inconsistent Spacing` pins on the outlier — grouped by page and frame if scope is the whole file, with the scan paused until you resolve every cluster and batch. This also catches cases across clusters, not just within one: if two separately-formed clusters are clearly playing the same structural role (e.g. two different near-duplicate "divider" colors that are too far apart in value to have been auto-grouped), that gets its own cross-cluster question too (Phase 2)
5. A straight question on primitive-only vs. primitive+semantic layering — asked now, with the scan and ambiguity review already done, instead of asked blind (Phase 3)
6. A proposed token structure built from the confirmed layering choice (Phase 4)
7. The confirmed tokens written to Figma Variables, named per the confirmed convention, each with a usage note (Phase 5)
8. **On request:** Figma Text Styles built from existing type tokens, visually verified, and optionally bound to matching text nodes across the scope — exact matches first, then confirmed near-match snapping for the rest (Phase 7)

---

## What This Skill Does NOT Do

- **Does not build components.** Turning a tokenized element into a proper variant-complete component is `Componentize Selection` (not yet built).
- **Does not write anatomy/variant documentation.** That's `Document Component` (not yet built).
- **Does not audit already-tokenized work for drift**, outside of the specific Phase 7 text-styles path. If the confirmed scope is already bound to Figma Variables and the designer wants color/spacing retokenized or reviewed for consistency, stop and say so — that's separate drift-auditing work, not tokenization, and not what this skill does. (Phase 7 is the one deliberate exception: type that's already tokenized is exactly the precondition for building text styles, not a reason to decline.)

---

## Token Naming — Always Lowercase

Every variable/token name this skill proposes, shows in chat, or writes to Figma must be entirely lowercase — no exceptions for primitives, semantic aliases, type tokens, space tokens, radius tokens, or conditional-pairing tokens confirmed in Phase 2. This applies to the variable's own name only, never to the collection it lives in.

- **Case:** no capital letters anywhere in a token name. `brand/teal/700`, `text-primary`, `heading-lg`, `button/emphasis/solid/background` — not `Brand/Teal/700`, `Text-Primary`, `Heading-LG`, `Button/Emphasis/SolidBackground`.
- **Structure stays the same, only case changes.** Groups are still separated with `/`, and multi-word segments within a group still use hyphens. Depth and grouping follow whatever convention was confirmed in Phase 0 — lowercase is a case rule layered on top of that convention, not a replacement for it.
- **If the designer types a name with capitals** (in a naming confirmation, a rename, or a custom-answer reply), normalize it to lowercase before using it, and say so plainly when confirming back — e.g. "Noted — I'll create this as `cta/primary-fill`, lowercased." Never silently keep the capitalized version, and never ask the designer to retype it in lowercase themselves.
- **Legacy/pre-existing variables are a separate decision, not swept in automatically.** If Phase 0, Step 2 finds an existing collection whose variable names are uppercase or mixed-case, every *new* variable this run creates is still lowercase regardless — that part isn't optional — but the existing ones are left untouched unless the designer opts in. Ask this explicitly, as its own lettered question, as soon as the existing naming case is noticed:

> I noticed the existing variables in this collection use uppercase or mixed-case names (e.g. `Color/Accent/Gold`). Every new variable I create this run will be lowercase either way — should I also rename the existing ones to match?
> **A)** Leave the existing variables as they are — only new ones are lowercase
> **B)** Rename the existing variables to lowercase too (bindings are by ID, so nothing comes unbound)
> **C)** Something else (tell me what)

- **Collection names are not covered by this rule.** `Primitive`, `Semantic`, `Responsive`, or whatever collection names were confirmed in Phase 0 keep whatever case the designer wants — title case is this skill's own default assumption, matching the examples throughout this document. This lowercase requirement is about variable/token names inside a collection, never the collection's own name. If the designer wants collection names lowercased too, that's a separate, explicit request — don't infer it from this rule.
- **Text Style names (Phase 7) follow the opposite convention on purpose.** Figma's own UI convention for Text/Color Styles is `Group/Variant` in natural case (e.g. `Display/2xl`, `Body/md-strong`), matching how the designer will scan them in the Text Styles panel — do not lowercase style names the way token/variable names are lowercased. This rule is about Figma Variables specifically; it does not extend to Styles.
- **After Phase 5 writes anything**, re-read the written collection(s) and confirm no uppercase letters slipped into a newly-created variable's name — report this check explicitly in Phase 6, the same way the scan-completeness check gets reported.

---

## How This Skill Asks Questions — Multiple Choice, Always, With a Custom-Answer Option

This skill runs in more than one place — sometimes inside Figma's own agent, sometimes through Claude directly — and the designer answers a lot of decision points over the course of one run. **Every decision point in every phase must be phrased as explicit lettered or numbered options the designer can answer with a single character, never as an open-ended question requiring a typed sentence.** This holds regardless of which agent is running the skill — it's a property of how this skill asks, not of one particular runtime. This applies to Phase 7 exactly the same as every other phase.

Concretely:
- Phrase every question as `A) ... B) ... C) ...` (or numbered) options, not as free prose ending in a question mark with no options given.
- **Every single question, with no exceptions, ends with one final custom-answer option** — e.g. `C) Something else (tell me what)` or, when the question already runs through C or D, the next letter up. This applies even to questions that look binary (scope, merge-vs-split, yes/no-shaped questions): the designer should never be boxed into two canned answers when a third thing is actually true. The lettered options cover the common cases so the designer can usually answer with one character; the final option is the escape hatch for everything else, and it always accepts a typed answer.
- Naming confirmations follow the same shape: option A is "keep as proposed," and the final option is the custom-answer one — if a question like this only has two natural outcomes (keep vs. rename), the rename option itself typically already doubles as the custom-answer slot, which is fine as long as it's clearly open-ended ("I'll rename one or both — say which, and to what") rather than another canned choice.
- When a question naturally has only two real outcomes (e.g. merge vs. keep separate), still write it as `A) / B) / C)` (the third being the custom-answer option) rather than a plain yes/no question — "answer in chat" is not enough on its own; give the actual option text.
- Batch and multi-item confirmations (e.g. the rarely-used-cluster batch in Phase 2, or the leftover-groups decision in Phase 7) still get lettered options plus the custom-answer option — e.g. `A) Merge all into their nearest match` / `B) Pull specific ones out to review individually` / `C) Something else (tell me what)` — rather than an open "which do you want to do?"
- This applies to every question shown as an example elsewhere in this skill — the example wording throughout this document already reflects this format; don't revert to open-ended phrasing, and don't drop the trailing custom-answer option even where a shorter version might read more naturally.

The goal is that over a long tokenization run, the designer is mostly typing single letters, not full sentences — but never at the cost of being unable to say "none of these, here's what I actually want" in one step.

---

## Phase 0 — Confirm Scope and Existing Convention

### Step 1 — Ask scope before anything else

**Always ask the designer directly which scope they mean, regardless of how the skill was invoked — never infer it from the wording they used to trigger it:**

> Should I tokenize just your current selection, or scan the whole file?
> **A)** Just the current selection
> **B)** The whole file, across every page
> **C)** Something else (tell me what)

Wait for their answer before doing anything else. Then, depending on the answer:

- **Selection scope:** Check what's currently selected on the canvas. If nothing is selected, ask the designer to select the frames or page contents to tokenize, then re-check before continuing. All of Phase 1 onward operates only on this selection. If the designer's actual goal turns out to be text styles rather than tokenization (see Phase 7's trigger), "selection" still means this same canvas selection — it just determines the scope Phase 7 walks instead of Phase 1.
- **Whole-file scope:** No selection is required. Phase 1 onward walks every page in the file. Confirm this explicitly back to the designer ("scanning all N pages in this file") before proceeding, since it's a broader action than a single-page run.

### Step 2 — Check whether this file already has a convention

Check this file's existing Variables **and** Styles — a convention can live in either or both (a file can have named color or type Styles defined without any Variables set up yet, and checking Variables alone would miss that). Three cases:

- **Existing Variable collections found** (e.g. a `Primitive` and/or `Semantic` collection already exists): surface the existing naming pattern back to the designer and ask, as lettered options — e.g. `A) Extend the existing Primitive/Semantic structure` / `B) Start a new, separate structure` / `C) Something else (tell me what)` — never assume. A file can carry partial, inconsistent variable work from an earlier pass.
- **Existing Styles found but no (or few) Variables:** surface this too — it means there's a partial convention worth matching (naming, groupings) even though it isn't in Variables yet. Ask as lettered options — e.g. `A) Carry this naming/grouping pattern into the new token structure` / `B) Start fresh with the reference convention instead` / `C) Something else (tell me what)`.
- **Nothing exists in either** (the common case for a true net-new brand): tell the designer you'll default to the reference convention below, and offer it as `A) Use the default reference convention` / `B) I want to specify a different convention` / `C) Something else (tell me what)` before you scan anything.

**Note:** the primitive-vs-semantic layering decision is **not** asked here anymore. It's asked later, in Phase 3 — after the scan (Phase 1) and the ambiguity review (Phase 2) are both done — so it's an informed choice made with real findings in hand, rather than a blind one made before anything's been scanned.

**Note:** if this step finds an existing convention with uppercase or mixed-case variable names, that doesn't change the case of anything this run creates — see "Token Naming — Always Lowercase" above for how the two interact (new variables are always lowercase; legacy ones are a separate, explicit opt-in).

**Note:** if, once scope and the file's state are clear, the designer's actual request turns out to be "build text styles from the type tokens I already have" rather than tokenization itself, confirm that type is genuinely already tokenized (every observed font size bound to a Figma Variable) and route straight to Phase 7, skipping Phase 1's scan and Phases 2–6 entirely for this run — see Phase 7's trigger condition for exactly how to confirm this.

---

## Phase 1 — Scan the Confirmed Scope for Raw Values

Read-only in this phase — do not create, modify, or delete anything yet. Walk every node in the confirmed scope from Phase 0 — either the designer's selection, or every page in the file — and collect, **tagged with the page and the frame each value was found on**:

- Every distinct **fill/stroke color**, resolved to hex, with a list of node names/ids (page name, and frame name) using it
- Every distinct **type combination** actually applied to text nodes: font family, weight, size, line height
- Every distinct **spacing value** in use: auto-layout padding, item spacing, gaps, and corner radius

### Scan with direct node traversal — never a design-context/code-generation tool

**This scan must be executed by walking the actual node tree directly** (`findAll`/`findAllWithCriteria` over every node in the confirmed scope, reading `fills`, `strokes`, text properties, and spacing/corner-radius properties straight off each node) — **never by calling a design-context or code-generation tool** (e.g. a tool meant to summarize a design for implementation, such as `get_design_context`) and treating its output as the scan.

This distinction matters because it was directly observed to cause real misses: a design-context-style tool summarizes and samples for the purpose of generating code, not for exhaustive enumeration, and it undercounts — sometimes severely — and can skip values entirely rather than just undercounting their occurrence total. In one observed case, it reported a color's occurrence count at roughly 60% of the true value found by direct traversal, and it missed an entire additional color cluster outright (a dozen real occurrences that never appeared in its output at all). The misses were not evenly distributed either — **stroke-only values were especially likely to be dropped or undercounted relative to fill values**, so a design-context-style scan can look complete while systematically under-representing borders, dividers, and outlines.

The consequence of under-scanning is not cosmetic: every downstream guarantee in this skill — the near-duplicate dedupe pass, the Tier 1/Tier 2 ambiguity flags, the batch-approval triage, the background-correlation check — can only act on what the scan actually surfaced. A value the scan never saw gets no annotation, no flag, and no question; it simply gets silently carried forward or silently missed, which is exactly the outcome this skill exists to prevent. This applies identically to type and spacing, not just color — a stray 11px corner radius sitting among a file-wide 12px pattern is only caught if the scan actually read every node's `cornerRadius`, not a sampled subset.

If a design-context-style tool was already used elsewhere in the session (e.g. to get oriented, or delegated to a subagent for a quick read), do not reuse that output as the Phase 1 scan — rerun Phase 1 with direct node traversal before treating the scan as complete.

**What counts as a "frame" depends on the confirmed scope — this is what Phase 2's annotation grouping keys off:**
- **Whole-file scope:** a frame is a literal top-level Figma frame. Each top-level frame on each page is its own boundary — a cluster spanning two top-level frames is tagged as appearing in both, never collapsed into one.
- **Selection scope:** the frame boundary is the selected frame/section itself, even when it isn't a literal top-level Figma frame (a selected section within a larger frame is still its own boundary for this run).

Conceptually, the scan logic looks like this (illustrative — adapt to the confirmed scope; selection scope walks the current selection on one page, whole-file scope walks every page's contents instead). Note that this pseudocode already models direct traversal — `node.hasSolidFills`, `node.solidFills` etc. are read straight off each node, never substituted with a summarized tool's output:

```javascript
// Illustrative logic, not a literal call — walk the node tree directly and bucket
// every distinct value by page and frame, then hand the buckets to Phase 2.
// This must be actual node traversal (findAll/findAllWithCriteria reading real
// node properties) -- never the output of a design-context/code-generation tool.
const roots = scopeIsWholeFile
  ? allPages.map(page => ({ page, nodes: page.children })) // nodes here are top-level frames
  : [{ page: currentPage, nodes: currentSelection }];       // the selection itself is the frame boundary

const colors = new Map();   // hex -> [{nodeId, nodeName, pageName, frameName, property}]
const type = new Map();     // "family|weight|size|lineHeight" -> [{nodeId, nodeName, pageName, frameName}]
const spacing = new Map();  // "property:value" -> [{nodeId, nodeName, pageName, frameName}]

function visit(node, pageName, frameName) {
  if (node.hasSolidFills) {
    for (const fill of node.solidFills) {
      record(colors, fill.hex, { nodeId: node.id, nodeName: node.name, pageName, frameName, property: 'fill' });
    }
  }
  if (node.hasSolidStrokes) {
    for (const stroke of node.solidStrokes) {
      record(colors, stroke.hex, { nodeId: node.id, nodeName: node.name, pageName, frameName, property: 'stroke' });
    }
  }
  if (node.isText) {
    const key = `${node.fontFamily}|${node.fontStyle}|${node.fontSize}|${node.lineHeight}`;
    record(type, key, { nodeId: node.id, nodeName: node.name, pageName, frameName });
  }
  if (node.hasAutoLayoutSpacing || node.hasCornerRadius) {
    for (const [prop, value] of node.spacingProps()) {
      record(spacing, `${prop}:${value}`, { nodeId: node.id, nodeName: node.name, pageName, frameName });
    }
  }
  node.children?.forEach(child => visit(child, pageName, frameName));
}

if (scopeIsWholeFile) {
  for (const { page, nodes } of roots) {
    nodes.forEach(topLevelFrame => visit(topLevelFrame, page.name, topLevelFrame.name));
  }
} else {
  const frameName = currentSelection.name ?? 'Selected section';
  currentSelection.forEach(n => visit(n, currentPage.name, frameName));
}
```

**Dedupe with a tolerance pass, not exact-match only.** Two colors 1–2 RGB steps apart, or two spacing values within a couple of pixels, are very likely the same intended value used inconsistently — but never merge them automatically, even if they're on different pages or frames. Carry them forward into Phase 2 as a flagged group instead.

---

## Phase 2 — Flag Ambiguity on Canvas Before Writing Anything

**This is the core guarantee of this skill: nothing gets written until ambiguity is resolved, and resolution is never silent.**

Every near-duplicate value group, every value used in conflicting ways, and every hue family with a gap in its step scale gets a **cluster ID** (`C1`, `C2`, …). Annotate each cluster at two levels — a frame-level decision point and a per-instance pointer — never just one.

### Triage before annotating — batch the rarely-used clusters

Not every cluster deserves its own annotation and its own question — that's what makes a messy file unreviewable. Before placing any annotations, split clusters into two groups based on **how often the divergent (minority) value actually occurs**, not how close it looks to the other values in the cluster:

- **Rarely-used clusters** — the divergent value shows up in **2 or fewer places** in the confirmed scope. Almost always an accidental one-off, not a structural decision. These skip individual Tier 1/Tier 2 annotations.
- **Everything else** — the divergent value shows up in **3 or more places**. These get the full Tier 1 + Tier 2 treatment below, individually — something repeated that often is worth a deliberate decision, however close or far apart the values look.

A color that's barely distinguishable by eye still gets individual treatment if it's used 5 times; a color that's wildly different still gets batched if it's only used once.

**For rarely-used clusters:** don't annotate them individually. Roll all of them into a single batch list per frame, presented once in chat — place a swatch next to each color entry on canvas first (see "Make colors visible" below), then screenshot that area and share it alongside the text:

> **Batch cleanup — rarely-used clusters (frame: Journal Days).** These look like minor, likely-accidental drift:
> - `#4A4038` 🟫 (1 use, "Date 15") — nearest match: `#493F37` 🟫 (used 11x elsewhere in this frame)
> - `14px/Regular` (2 uses, "Caption — Furnas", "Caption — Footer") — nearest match: `13px/Regular` (used 9x elsewhere)
> *(screenshot of the swatched region attached)*
> **A)** Merge all of these into their nearest match
> **B)** Pull one or more of these out to review individually (say which)
> **C)** Something else (tell me what)

Wait for **one answer covering the whole batch** before moving on. This is still never a silent decision — the designer is explicitly approving the batch, it's just one approval instead of many. If they pull an item out, treat it as its own cluster and run it through the normal Tier 1/Tier 2 flow below instead.

**For everything else:** proceed exactly as described in Tier 1 and Tier 2, one annotation and one question per cluster.

### Tier 1 — Frame-level `Tokenization Review` annotation

For each frame (per the frame boundary defined in Phase 1) that contains at least one member of a cluster, add **one** `Tokenization Review` annotation on a representative node in that frame. List only the cluster's values actually found **within that frame** — never the full cross-file cluster — and note if the same cluster also shows up elsewhere. This is the annotation that carries the actual decision question:

> **Tokenization Review — ambiguous (C1).** Within this frame: `#493F37` 🟫, `#4A4038` 🟫, `#4B4138` 🟫. Also appears in: Footer, Onboarding flow. **Answer in chat** — this annotation is a flag, not a comment thread.
> **A)** Merge these into one token
> **B)** Keep them as intentionally distinct steps
> **C)** Something else (tell me what)

### Tier 2 — Per-instance pointer pins

Within that same frame, add a lightweight pin at each **distinct value's** occurrence — one pin per unique value found in that general area, not one per node (five nodes sharing the exact same hex get one pin, not five). Label it by category — `Inconsistent Colors`, `Inconsistent Type`, or `Inconsistent Spacing` — and state **only the single value actually present at that node**, never the full cluster list:

> **Inconsistent Colors (C1).** This uses `#4A4038` 🟫. See the Tokenization Review flag above for the full cluster and the decision needed.

A per-instance pin is a pointer, not a second decision — it never asks its own question or repeats the Tier 1 prompt.

### Annotations are flags, not comment threads — always resolve in chat

Figma's native annotations (unlike comments) have no reply or threading mechanism — there is nowhere on canvas for the designer to actually respond to one. Every annotation's text should make this clear rather than implying an on-canvas reply is possible:

- Never phrase an annotation's question as "reply here" or "comment below." Use "answer in chat," "resolved in chat," or similar.
- If the designer would rather discuss on canvas, point them to Figma **comments** (not annotations) as the mechanism that actually supports replies — but the default flow for this skill is: annotation states the flag, chat carries the back-and-forth and the designer's answer.
- Treat an annotation purely as a persistent marker that stays visible on canvas while the decision is pending in chat — not as the input mechanism itself.

### Make colors visible — swatches on canvas, not just hex codes

A hex code alone (`#4A4038`) tells the designer nothing about what the color looks like without leaving Figma to check Selection colors, and that lookup often doesn't even match what the annotation is describing (the scan scope vs. the current selection can differ). Fix this with an actual visual swatch, not a longer hex list:

1. **Place a small filled swatch rectangle next to every color value named in an annotation or batch entry** — one swatch per distinct color, roughly 16×16px, fill set to that exact hex, positioned beside the annotation pin so it's visible without opening Dev Mode or the Selection colors panel. Label each swatch with its hex value directly beneath it.
2. **When presenting a Tier 1 cluster, a batch-cleanup list, or a conditional-pairing proposal in chat, take a screenshot of that canvas region (via `get_screenshot`) after placing the swatches, and share the image alongside the text** — don't rely on hex codes alone in the chat message. The designer should be able to see the colors in the conversation, not just read their codes.
3. This applies to color only for now — type and spacing values are already visually self-evident on canvas (the actual text/spacing is right there to look at), so swatches are not needed for those categories.
4. Swatches are scratch/review aids, not part of the final file — once a cluster or batch is resolved and Phase 5 writes the confirmed tokens, delete the swatch rectangles you placed for it so they don't linger as stray canvas clutter.

### Clean up as you go — don't let resolved flags linger

Resolve-and-remove, one cluster or batch at a time, rather than leaving everything up until the whole scope is done. As soon as the designer answers a Tier 1 cluster's question or a batch's merge/split question:

1. **Remove that cluster's annotations from canvas immediately** — the Tier 1 `Tokenization Review` annotation, every Tier 2 per-instance pin tied to it, and any swatches placed for it (per "Make colors visible" above) — before moving on to present the next cluster or batch.
2. **Do this per-cluster/per-batch, not in one pass at the end.** The point is that at any moment during the review, canvas only shows flags still awaiting an answer — not a growing pile of resolved-but-not-yet-cleaned-up annotations that makes it hard to tell what's actually left to decide.
3. **Keep the resolved decision itself** (the merge/split outcome, the confirmed name, the usage note) in your own running record so Phase 4 and Phase 5 can still use it — removing the on-canvas annotation only clears the visual clutter, it never discards the answer.
4. This applies to both individually-reviewed Tier 1 clusters and batch-cleanup lists alike — a batch's combined approval removes every annotation in that batch at once, the same as a resolved individual cluster removes its own.

### Check for background correlation before phrasing the Tier 1 question

Before writing a cluster's Tier 1 question, check whether its divergence correlates with each occurrence sitting on a different background (a different parent fill, or a differently-toned section). This catches the case where values look like drift but are actually intentional — e.g. header text that's white on a dark section and near-black on a light section.

- **If every distinct value in the cluster pairs consistently with a distinct background tone** (each value only ever appears on one kind of background, never mixed), change the question from a plain merge-or-split ask to a contrast-aware one:

> **Tokenization Review — ambiguous (C4).** Within this frame: header text is `#FFFFFF` ⬜ on dark sections, `#2B2B2B` ⬛ on light sections — consistently paired, never mixed. **Answer in chat.**
> **A)** Intentional pairing by background — keep both, name them separately
> **B)** One of these should be corrected to match the other
> **C)** Something else (tell me what)

- **If the values are mixed inconsistently across similar backgrounds** (no clean pairing), treat it as a plain ambiguity and ask the original merge-or-split question — the correlation check found nothing to explain the divergence.
- This check only ever changes the **question asked**, never the verdict — it still waits for the designer's answer either way, and still never auto-merges or auto-splits based on the correlation alone.

**Known limitation:** this checks background correlation only, not the cross-cluster role-matching covered next. Treat it as a cheap signal for color specifically — not a guarantee every legitimate contextual variation gets caught, and not yet extended to type or spacing. A 32px-bold-vs-30px-regular header mismatch, or an inconsistent corner radius, still gets a plain ambiguity question today even if it later turns out to be two deliberate heading levels rather than drift.

### Check for cross-cluster role matches — same design role, different clusters

The tolerance-based dedupe in Phase 1 only merges values that are numerically close enough (a couple of RGB steps, a couple of pixels). That catches one kind of ambiguity — near-identical values that are probably the same thing used inconsistently — but misses a different, equally real kind: **two values that are clearly serving the same structural role in the design, but are far enough apart in value that the tolerance pass formed them into two separate clusters (or left one as an unclustered singleton) instead of flagging them together.** A near-duplicate check alone will walk right past this, because by definition the values aren't near-duplicates of each other — they're role-duplicates.

After Phase 1's tolerance dedupe has formed its clusters, run a second pass over the resulting cluster set (per category — color, type, spacing) that groups clusters by shared role signals rather than value proximity:

- **Node-name keywords** — clusters whose member nodes share naming patterns suggestive of the same role (`divider`, `border`, `hairline`, `separator`, `outline`, `rule`; or `header`, `eyebrow`, `label` for type; or `gap`, `gutter`, `padding` for spacing).
- **Property type** — e.g. two color clusters that are both stroke-only (not fill) are more likely to be playing the same "line/border" role than a stroke cluster and a fill cluster.
- **Consistent usage pattern** — e.g. both clusters are applied as 1px strokes on container edges, or both are applied to the smallest text size on the page, even though their resolved values differ.

When two or more clusters match on these signals, surface them together as one combined question instead of letting each cluster get resolved in isolation without ever being compared to the other:

> **Tokenization Review — possible duplicate role (C2 ↔ C5).** Both of these are used on divider/separator elements, but resolve to different values: `#D4CFC8` 🔲 (C2, 14 uses, "Divider — Card List") vs. `#C2BDB5` 🔳 (C5, 6 uses, "Divider — Footer Nav"). They weren't automatically grouped because they're not close enough in value, but they're playing the same structural role.
> **A)** Same design role — merge into one token
> **B)** Intentionally distinct dividers — keep separate, help me name them so the difference is clear
> **C)** Something else (tell me what)

This question is asked in addition to, not instead of, each cluster's own Tier 1 ambiguity question if it has one — a cluster can be internally consistent (no near-duplicates within it) while still cross-matching another cluster on role. Treat the designer's answer the same way as any other Tier 1 resolution: binding, recorded, and never silently assumed.

**Known limitation:** this is pattern matching on node names and usage signals, not true semantic understanding — it's a best-effort pass meant to catch the common cases (dividers, borders, repeated structural roles), not a guarantee that every same-role-different-value pair in the file gets caught. A role match with no clear naming or usage signal to key off of may still slip through; when in doubt, it's fine to surface a weaker match as a question anyway rather than stay silent — a "maybe" question costs the designer one letter to dismiss, while a missed duplicate costs a real token-structure inconsistency later.

### Naming a confirmed conditional pairing — propose from context, never from a template

If the designer confirms a cluster is an intentional conditional pairing (not drift), it does **not** become one swap-by-mode token — it becomes multiple named semantic tokens, one per variant, because the "modes" here are an open-ended, file-specific set (section backgrounds, card types, whatever the correlating factor turned out to be), not a fixed, reusable axis like light/dark.

There is no fixed naming shape for these — what the name should reference changes with what actually explains the divergence, and guessing a template (`-primary`/`-secondary`, `-on-dark`/`-on-light`, numbered suffixes) will be wrong as often as it's right. Instead, every time a pairing is confirmed:

1. **State what was actually found** — the correlating factor the check identified (a specific background color or named section, a component/card type, whatever it was).
2. **Propose a name per variant that reflects that specific factor**, and say why — e.g. if the correlating factor is a named section, propose `text-header-hero` / `text-header-footer`; if it's an unnamed background tone, propose something tied to that tone rather than an arbitrary ordinal.
3. **Ask the designer to confirm or rename each one individually** before it's added to Phase 4's proposal table — never assume the proposed names are right, and never reuse the naming shape from a previous cluster just because it fit there.

For example:

> **C4 confirmed as an intentional pairing** — the correlating factor is section background (Hero: dark forest green; Footer: light cream). Proposed names: `text-header-hero` (`#FFFFFF` ⬜), `text-header-footer` (`#2B2B2B` ⬛).
> **A)** Keep these proposed names
> **B)** I'll rename one or both (say which, and to what — this is the custom-answer option)

Only once the designer confirms each name does the cluster get added to Phase 4's proposal table as multiple semantic tokens — each a plain, single-value alias to its primitive, with the correlating context captured in its usage note (Phase 5) so the reasoning travels with the token, not just the name.

### Resolving and reporting

In chat, list every Tier 1 cluster with its direct question, plus each frame's batch-cleanup list from the triage step above — each accompanied by a screenshot of the swatched canvas region, not just the hex text — and **wait for the designer's answer on each Tier 1 cluster and on each batch** before moving to Phase 3. **If scope is the whole file, group this list first by page, then by frame within the page** (e.g. "Homepage exploration" → "Hero frame," then its flags, then "Footer frame," then its flags) rather than one flat list — a cluster spanning multiple frames or pages gets listed under whichever frame has the first occurrence, with a note that it also appears elsewhere. Do not:
- Silently merge near-duplicates into one token
- Silently keep them as separate tokens
- Guess based on visual similarity alone
- List a cluster's full value set on a per-instance pin, or on any annotation anchored to a node that doesn't actually carry all of those values

---

## Phase 3 — Confirm Primitive-Only or Primitive + Semantic

Now that the scan (Phase 1) and the ambiguity review (Phase 2) are both done, ask this with real findings in hand instead of asking it blind at the start:

This is a real tradeoff, not a default to silently pick:

- **Primitive-only** (e.g. `neutral/900`, `brand/green/700`): faster to set up, fewer tokens to maintain up front.
- **Primitive + semantic alias** (e.g. `neutral/900` → aliased by `text-primary`): costs more setup time now, but means a rebrand, dark-mode pass, or repoint later only touches the alias layer, never every component.

It can help to mention what the scan and ambiguity review actually turned up when framing the question — e.g.:

> I found 3 hue families and one conditional pairing (C4, header text by background).
> **A)** Primitive-only — faster setup, fewer tokens
> **B)** Primitive + semantic alias — more setup now, but things like the C4 pairing get proper role-based names and a rebrand later only touches the alias layer
> **C)** Something else (tell me what)

Ask this explicitly every run — don't infer it from whether a reference convention happens to have one or the other, and don't skip it just because the scope looked simple. Wait for the designer's answer before Phase 4.

---

## Phase 4 — Propose the Token Structure

From the scan and the resolved ambiguity review:

1. **Group colors into hue families and steps.** If a family groups cleanly (a clear light→dark ramp), propose step numbers; if it doesn't, ask the designer to name the families rather than guessing.
2. **Propose primitive names** in `<huefamily>/<step>` form (e.g. `neutral/900`, `brand/teal/700`) — or whatever convention was confirmed in Phase 0, always lowercase per "Token Naming — Always Lowercase" above.
3. **Propose a type scale.** Map the observed font sizes to semantic scale steps (`heading-xl`…`heading-xxs`, `body-xl`…`body-xs`, `eyebrow-*`, `legal`, or equivalents) only where the observed sizes plausibly form a scale. Where they don't, say so and propose a new scale from what's actually there rather than forcing an ill-fitting one.
4. **Propose a spacing primitive scale.** Check whether observed values fit a consistent base unit before proposing a numbered scale on that unit — don't assume a plain 4px pixel grid; verify the actual base unit found first.
5. **If Phase 3 confirmed a semantic layer:** propose semantic names from how each primitive is actually used (a background fill on a primary CTA → `button-primary-bg`; a text fill on body copy → `text-primary`), not just from color proximity. Fold in any conditional-pairing tokens already named and confirmed in Phase 2.
6. **Before proposing a new semantic alias for a primitive that already has one, check whether the new usage can just reuse the existing alias instead of creating a duplicate.** The same primitive value sometimes shows up in two different-sounding roles (an accent color on a CTA is also, elsewhere in the file, used as a card or surface background) — that doesn't automatically mean two semantic tokens are needed. Only propose a second, separate semantic name when there's a concrete reason the two uses might reasonably diverge in value later (a future rebrand, a planned surface-only restyle, different accessibility requirements for the two contexts); if there's no such reason, reuse the one token for both roles rather than minting a role-specific duplicate. This is a judgment call, so ask rather than silently picking either way:

> `accent/gold` already covers `Gold/200` for CTA accents. The same primitive also shows up 3 times as a card background elsewhere in the file. Should these share one semantic token, or do you want a separate name for the surface use?
> **A)** Reuse `accent/gold` for both roles — same value, no reason to expect them to diverge
> **B)** Create a separate alias for the surface use (e.g. `surface/gold-card`) — these might need to diverge later
> **C)** Something else (tell me what)

This check runs per-primitive, not per-cluster — it's unrelated to the cross-cluster role-matching check in Phase 2 (which looks at whether two *different* raw values should collapse into one primitive). This one looks at whether one *already-tokenized* primitive is about to get a second, possibly-unnecessary semantic name.

Output this as a proposal table before writing anything — covering color, type, and spacing/radii together, not color alone (the row set below is illustrative of the mix expected, not a cap on how many of each category to include):

| Proposed Token | Value | Where Used | Confidence |
|---|---|---|---|
| `brand/teal/700` | `#2e8b6f` | 14 nodes (CTAs, links) | Certain |
| `brand/teal/750`? | `#2c886d` | 2 nodes (hover state) | Needs review — 2 steps from teal/700, see annotation |
| `neutral/900` | `#1c1c1c` | 22 nodes (body text, icons) | Certain |
| `heading-lg` | 28px / Semibold / 36 line-height | 6 nodes (section headers) | Certain |
| `body-md` | 16px / Regular / 24 line-height | 31 nodes (body copy) | Certain |
| `eyebrow` | 12px / Medium / 16 line-height, uppercase | 4 nodes (category labels) | Certain |
| `space/200` | 16px | Card padding, 9 instances | Certain |
| `space/300` | 24px | Section gaps, 7 instances | Certain |
| `radius/sm` | 4px | Buttons, inputs, 18 instances | Certain |
| `radius/md`? | 8px | Cards, 3 instances — 1 node uses 6px, see annotation | Needs review |

---

## Phase 5 — Write to Figma Variables

Only after Phase 0 (scope + existing convention), Phase 2 (ambiguity), and Phase 3 (layering) are all resolved:

1. Find or create the needed Variable collection(s) — e.g. a `Primitive` and/or `Semantic` collection if one doesn't already exist.
2. Create each confirmed token, using the exact confirmed name and value.
3. **Set the usage note as the variable's description field** — not just in a separate documentation frame. A usage note that only lives on one documentation page can drift out of sync or go unseen; baking it into the variable itself means the note travels with the token wherever it's inspected (Dev Mode, plugin, code export).
4. If a semantic layer was confirmed, bind each semantic variable's value as an alias to its primitive (not a duplicated raw value).

---

## Phase 6 — Confirm

Output a final table of everything written — again, every category that was actually proposed and confirmed (color, type, spacing, radii), not just color:

| Token | Collection | Value | Usage Note | Status |
|---|---|---|---|---|
| `brand/teal/700` | Primitive | `#2e8b6f` | — | ✅ Created |
| `button-primary-bg` | Semantic | → `brand/teal/700` | "Primary CTA background" | ✅ Created |
| `neutral/900` | Primitive | `#1c1c1c` | — | ✅ Created |
| `heading-lg` | Primitive | 28px / Semibold / 36 | — | ✅ Created |
| `body-md` | Primitive | 16px / Regular / 24 | — | ✅ Created |
| `space/200` | Primitive | 16px | — | ✅ Created |
| `radius/sm` | Primitive | 4px | — | ✅ Created |

Call out anything that couldn't be written (missing collection permissions, a name collision with an existing token) rather than silently skipping it. Also call out the lowercase check from "Token Naming — Always Lowercase": confirm every newly-created variable name in this table came out lowercase, re-reading the collection if there's any doubt.

**If the designer goes on to ask for text styles built from these (or from any already-tokenized type) in the same or a later run, go to Phase 7 — it's a distinct deliverable with its own triggers, groupings, and confirmations, not an automatic extension of Phase 5/6.**

---

## Phase 7 — Build Text Styles From Type Tokens (On Request Only)

This phase is never run automatically after Phase 6, and it never runs as part of a normal tokenization pass — it only runs when the designer explicitly asks for **text styles** (or "typography styles") to be built, and it has its own trigger condition, its own ambiguity handling, and its own write-confirmation gate, all independent of Phases 1–6.

### Trigger condition — type must already be tokenized

Check whether the font sizes in the confirmed scope (per Phase 0's scope question — this phase still asks scope independently if it hasn't already been confirmed this run) are already bound to Figma Variables (e.g. a `Type/Display`, `Type/Body` collection or equivalent).

- **If type is already tokenized:** confirm this back to the designer in one line and **skip Phase 1's scan and Phases 2–6 entirely** for this run — there's no raw-value tokenization work left to do; proceed straight to grouping below.
- **If type is not tokenized, or only partially:** say so, and ask explicitly before proceeding:

> The type sizes in this scope aren't all bound to variables yet. How do you want to handle that?
> **A)** Run the full tokenize scan first (Phases 1–6), then come back and build text styles from the result
> **B)** Skip the scan — build text styles now using whatever sizes are actually present, even though they're not variables yet
> **C)** Something else (tell me what)

Never assume (A) or (B) — this is exactly the kind of fork the designer needs to make, since (B) means the resulting styles won't have the rebrand/repoint benefit a bound variable gives them.

### Group font sizes into named styles — propose from actual usage, not a fixed template

From the confirmed scope, group the observed type combinations (font family, weight, size, line height, tracking, case) into named style groups. There's no fixed group-name set to apply blindly (`Display`/`Body`/`Text`/`Label` is one reasonable split, seen in practice, not a mandatory shape) — propose groupings from what's actually in the file:

1. **One style per distinct (size-variable, line-height, and — where it changes meaning — weight) combination**, not one style per size alone. Two nodes can share the same bound size variable but use different line heights for genuinely different purposes (e.g. a 16px paragraph style at 160% line height vs. a 16px UI-label style at auto line height) — these must be two different styles, not collapsed into one, even though both ultimately bind the same size variable. Say this explicitly when presenting the proposal so the designer isn't surprised that two styles report the same bound size.
2. **Bind only what's actually a variable.** The style's font-size property binds to the existing `Type/*` variable. Weight, line-height, and tracking are normally **not** variables in an exploratory file — set those directly on the style, and say so plainly rather than implying the whole style is tokenized:

> Weights, line heights, and tracking aren't variables in this file, so they're set directly on each style (only font size is bound through a variable). I based the specific combinations on what's used most often for each group.

3. **Propose group and style names, and get them confirmed** using the same lettered-question format as everywhere else in this skill — don't silently finalize a naming scheme the designer hasn't seen:

> I'd group the type in this scope into these style sets, each bound to its `Type/*` size variable:
> **Display** — 2xl/xl/lg/md/sm/xs, Cormorant Garamond, mostly SemiBold
> **Body** — lg/md/md-strong, Inter, 160% line height, for paragraphs
> **Text** — md/base/base-strong/xs/2xs/3xs, Inter, auto line height, for UI text
> **Label** — md/base/xs, Inter Bold, uppercase, auto line height
> **A)** Build these as proposed
> **B)** I want to rename or regroup some of these (say which)
> **C)** Something else (tell me what)

4. **Don't force every one-off combination into a group.** A long tail of rarely-repeated combinations (e.g. a handful of small uppercase labels with unusual tracking, or a one-off stamp/badge style) doesn't need its own style built up front — name the biggest leftover groups when reporting results (see "Report leftovers precisely" below) and let the designer decide whether any are worth adding.

### After creating, verify visually yourself — don't just ask the designer to check

Once the styles are created, **take a screenshot of the Text Styles panel (or otherwise visually confirm the created styles) before reporting completion**, the same way this skill already treats visual/completeness verification as part of the job elsewhere (e.g. the lowercase re-check after Phase 5, the swatch screenshots in Phase 2) — don't hand the designer an unverified result and ask them to be the one to look. If a visual-inspection tool genuinely isn't available in the running context, say explicitly that the styles weren't visually verified and ask the designer to check, rather than silently skipping the check without saying so.

### Report what was created

| Group | Styles | Font & Properties |
|---|---|---|
| Display | 2xl, xl, lg, md, md-regular, sm, xs | Cormorant Garamond, SemiBold unless noted |
| Body | lg, md, md-strong | Inter, 160% line height |

State plainly which properties are bound through a variable (size) and which are set directly on the style (weight, line height, tracking) — never imply a style is fully tokenized when only its size is.

### Binding styles to existing text nodes — exact match only, never rounded

If the designer also wants the new styles applied to the text nodes across the scope (a separate ask from building the styles — don't bind automatically just because styles were created):

1. **Treat this as a large rebind**, in the same spirit as Phase 5's write-confirmation gate — get an explicit go-ahead before running it across many nodes, especially on a whole-file scope.
2. **Bind a node to a style only on an exact match** across font family, weight, size, line height, tracking, and case. Never round to the nearest style on this first pass — a node that's 1px or one weight-step off from a style is a non-match here, not a loose match.
3. **Binding a component's main component binds every instance automatically** — don't also iterate over and individually override each instance; report instance-inherited counts separately from directly-bound counts so the designer can tell the two apart.
4. **Report the exact-match results with this breakdown**, not just a total:
   - Nodes bound directly
   - Nodes that inherited the style via a bound main component (and weren't touched individually)
   - Nodes left unstyled because of **no exact match**, broken down by the most common leftover combinations (font/weight/size/case) so the designer can see what's missing
   - Nodes left unstyled because of **mixed formatting within a single text layer** (more than one style inside one layer) — these can't be exact-matched at all and need the designer's attention directly, not a new style
5. **State explicitly whether the binding replaced each node's own font-size variable binding with the style's** — since a Figma text style can carry its own variable binding, a node's size binding may now come through the style rather than directly, and that's worth calling out as a side effect, not leaving implicit.
6. **Visually spot-check after binding**, the same way as after creation — note plainly that an exact-match bind should produce no visible change, so if a screenshot comparison is possible, use it to confirm that expectation held, rather than only asserting it.

### Snapping near matches — only with explicit go-ahead, and only with the change disclosed per layer

For nodes still unstyled after the exact-match pass, the designer may ask to snap them to their nearest existing style instead of adding more styles. This is materially different from exact-match binding — it changes how nodes actually look — so:

1. **Never snap without an explicit, separate go-ahead**, distinct from the earlier large-rebind confirmation. Exact-match binding and near-match snapping are two different risk levels and need two different confirmations.
2. **Define and state the match tolerance before running it** — e.g. same font family and same uppercase/case setting required; size within 1px; weight within one step (Regular↔Medium, Semi Bold↔Bold, etc., not a two-step jump). Don't snap across a bigger gap than this without calling it out as a separate, looser case needing its own confirmation.
3. **Report the snap results broken out by what actually changed**, not just a count of nodes touched — a flat "62 nodes updated" hides very different kinds of changes:
   - Weight shifted (say which direction, e.g. Semi Bold → Bold, and how many nodes)
   - Size moved by up to 1px (say the old and new size, and how many nodes)
   - Tracking was dropped or changed (say by how much, and how many nodes)
   - Line height changed (say old vs. new, and how many nodes) — and explicitly flag this one: a line-height change can affect fixed-height text boxes or tight auto-layout spacing, so tell the designer to check those specifically, don't just report the count.
4. **State plainly that the swap is not cleanly reversible.** Unless each node's pre-snap weight/tracking/line-height was recorded before overwriting, there's no way to restore the exact original values automatically — say this clearly before running the snap (as part of the go-ahead question), not only after the designer asks to undo it:

> Snapping will change how these nodes look (not just rebind a variable) — weight, tracking, or line height can shift to match the nearest style, and I won't have the original per-node values on hand afterward to undo it automatically. Want me to go ahead?
> **A)** Yes, snap all remaining unmatched nodes to their nearest style
> **B)** Show me the proposed changes per group first, then decide
> **C)** Something else (tell me what)

5. **After snapping, list what's still left unstyled and why** — distinguish nodes with no plausible near match at all (e.g. icon glyphs, a markedly different italic or decorative treatment that would look wrong if forced into an existing style) from nodes that could get a new style built for them if the designer wants one. Don't silently leave this list implicit in a total count.

---

## Hard Rules — Non-Negotiable

- **Never invent a token name outside the convention confirmed in Phase 0.** If unsure, ask — don't default to a guess.
- **Never ask the primitive-vs-semantic question before the scan and ambiguity review are done.** Phase 3 comes after Phase 1 and Phase 2, specifically so it's an informed decision, not a blind one.
- **Never run Phase 1's scan using a design-context/code-generation tool (e.g. a tool meant to summarize a design for code output) as a substitute for direct node traversal.** Such tools sample and summarize rather than enumerate exhaustively, and have been directly observed to undercount a color's true occurrence count by a wide margin and to miss entire color clusters outright — stroke-only values are especially likely to be dropped. The same risk applies to type and spacing. If one was already used elsewhere in the session to get oriented, treat that as informal context only and rerun Phase 1 with direct traversal before relying on the scan.
- **Never silently merge or silently keep separate a near-duplicate value.** Every such case gets a frame-level `Tokenization Review` annotation and a direct question; both outcomes require the designer's answer.
- **Never list more values on an annotation than are actually present at its anchor.** A per-instance pin states only the value at that node; the full cluster list belongs only on the Tier 1 frame-level annotation.
- **Never suppress a flag just because a background correlation is found.** A clean background pairing changes the Tier 1 question's wording, never the requirement to ask it and wait for the designer's answer.
- **Never resolve a cluster in isolation without checking it against other clusters for a role match.** After Phase 1's tolerance dedupe forms clusters, run the cross-cluster role-matching pass before treating the cluster set as final — two values too far apart to auto-merge can still be the same design role (e.g. two different divider colors) and deserve a combined question, not two independent ones that never get compared to each other.
- **Never skip the batch-approval step for rarely-used clusters.** Batching removes individual canvas annotations, not the requirement for the designer to explicitly approve the batch (or pull items out of it) before anything is merged.
- **Never present a color-related decision as hex codes alone.** Place an on-canvas swatch next to every color value in a Tier 1 annotation, Tier 2 pin, or batch entry, and share a screenshot of the swatched region in chat — a hex code by itself is not enough for the designer to tell what's actually being flagged.
- **Never phrase an annotation as if it can be replied to on canvas.** Figma annotations don't support threaded replies; every annotation should direct the decision to chat, never say "reply here" or imply an on-canvas response.
- **Never leave a resolved cluster's or batch's annotations and swatches on canvas.** Remove them as soon as the designer answers — one cluster or batch at a time, not in a single cleanup pass at the end — so canvas only ever shows what's still awaiting a decision.
- **Never apply a fixed naming template to a confirmed conditional pairing.** Propose each variant's name from the actual correlating context found, state the reasoning, and get the designer's confirmation on every one — even when a previous cluster's naming shape (by background, by section, by component type) doesn't fit this one.
- **Never write a single variable before Phase 0, Phase 2, and Phase 3 are all resolved.** Scanning and flagging are read-only; writing happens last.
- **Never assume primitive-only or primitive+semantic.** Ask every run, after the scan and ambiguity review — many real-world design systems mix both patterns in different places, so there is no safe default to infer.
- **Never mint a second semantic alias for a primitive that already has one just because the new usage has a different conceptual label (accent vs. surface, etc.).** Check first whether the existing alias can simply be reused for the new role, and only propose a separate name when there's a concrete reason the two uses might diverge in value later — ask the designer rather than deciding either way silently.
- **Never operate on already-tokenized work as if it needs re-tokenizing**, outside of Phase 7's text-styles path. If the confirmed scope's fills/type/spacing are already bound to Figma Variables and the ask is to retokenize or audit them, stop and tell the designer this looks like drift-auditing territory, not tokenization, and is out of scope for this skill.
- **Never assume scope.** Always ask selection vs. whole file explicitly in Phase 0, Step 1 — never infer it from the phrase the designer used to invoke the skill. Never touch nodes outside whichever scope they confirmed, and use the frame boundary definition that matches it — a literal top-level Figma frame for whole-file scope, the selected frame/section itself for selection scope.
- **Never assume one file's naming convention holds for a different brand file** you haven't actually scanned — see the reference notes below.
- **Never ask a decision point as an open-ended question.** Every question in every phase — scope, convention, merge-vs-split, batch approval, background-correlation, naming confirmation, primitive-vs-semantic, text-style grouping, rebind go-ahead, snap go-ahead — must be phrased as lettered (or numbered) options the designer can answer with a single character. This applies whether the skill is running in Figma's own agent or through Claude; it's not runtime-specific.
- **Never present a question's lettered options as the only way to answer.** Every single question — with no exceptions, including ones that look binary — ends with a final custom-answer option (e.g. `C) Something else (tell me what)`, or a rename/free-text option that's clearly open-ended). The designer must always be able to say "none of these" and type what they actually mean, in the same turn, without having to ask for that option first.
- **Never write a new variable/token name with a capital letter in it.** Every primitive, semantic alias, type, space, radius, and conditional-pairing token name this run creates must be entirely lowercase, regardless of what convention or case the designer's existing file uses. Collection names are exempt from this rule. If the designer types a capitalized name, normalize it to lowercase and say so — never silently keep it, and never rename pre-existing legacy variables to lowercase without the designer explicitly opting in first (see "Token Naming — Always Lowercase"). This rule is about Variables only — Phase 7 Text Style names keep Figma's natural-case `Group/Variant` convention and are not lowercased.
- **Never build or bind text styles without the Phase 7 trigger check.** Confirm type is already tokenized (or get an explicit answer on how to handle it not being) before building any style — Phase 7 is never run as an automatic continuation of Phases 1–6.
- **Never silently collapse two style groups that share a bound size variable but differ in line height, weight, or tracking into one style.** Same size variable does not mean same style — say explicitly when two groups share a size binding but remain separate styles.
- **Never bind a text node to a style on anything less than an exact match**, unless the designer has explicitly approved a near-match snap for that node. Exact-match binding and near-match snapping are two separately-gated actions, never one combined step.
- **Never snap near matches without stating the match tolerance up front and getting a separate, explicit go-ahead distinct from the exact-match bind confirmation.** Always disclose that the swap is not cleanly reversible before running it, not only after being asked to undo it.
- **Never report a near-match snap as a flat node count.** Break the report out by what actually changed per node (weight, size, tracking, line height) and flag line-height changes specifically as worth checking against fixed-height boxes and tight auto-layouts.
- **Never report text-style creation or binding as complete without a visual check.** Screenshot the Text Styles panel (or the bound canvas region) yourself before declaring done; if no visual-inspection tool is available in the running context, say so explicitly rather than silently skipping the check.

---

## Success Criteria

- Every written token has, at minimum, a resolved value and a usage note (in the variable's description field, not only in a documentation frame).
- Every token name follows the convention confirmed in Phase 0 for this brand — never an ad hoc name.
- Phase 1's scan was built from direct node traversal (every node's actual fills/strokes/text/spacing properties read individually), never from a design-context/code-generation tool's summarized output — confirm this explicitly before trusting the scan as complete.
- Every near-duplicate or ambiguous value found in Phase 1 was flagged via both a frame-level `Tokenization Review` annotation and per-instance pins, and explicitly resolved by the designer in Phase 2 before being written.
- Every per-instance pin states only the value actually present at that node — never a cluster's full value list.
- Where a cluster's divergence correlates cleanly with differing backgrounds, the Tier 1 question reflects that context (e.g. asks about a semantic contrast pairing) rather than presenting it as unexplained drift.
- Before the cluster set was treated as final, a cross-cluster role-matching pass checked for clusters playing the same structural role (shared node-name keywords, property type, or usage pattern) despite being too far apart in value to have been auto-grouped by the tolerance dedupe — and any matches found were surfaced as a combined question, not resolved as unrelated clusters.
- Clusters whose divergent value occurs in 2 or fewer places were rolled into a single batch-approval list per frame rather than given individual canvas annotations, and the designer explicitly approved (or pulled exceptions out of) each batch before Phase 4.
- Every color value shown in a Tier 1 annotation, Tier 2 pin, or batch entry is backed by an actual on-canvas swatch, and every chat message presenting a color decision includes a screenshot of that swatched region rather than hex codes alone.
- No annotation implies it can be replied to directly — every decision point points the designer to chat, since Figma annotations have no reply mechanism.
- Every cluster's and batch's annotations and swatches were removed from canvas immediately once the designer answered — canvas never accumulates a backlog of already-resolved flags during the review.
- Where a cluster was confirmed as a conditional pairing, each resulting token's name was proposed from the actual correlating context found (never a fixed template) and confirmed individually by the designer before being added to Phase 4's proposal table.
- The primitive-vs-semantic decision was asked, not assumed, **after** the scan and ambiguity review — never asked blind at the start of the run.
- Before proposing a new semantic alias for an already-tokenized primitive, the proposal checked whether an existing alias for that primitive could just be reused for the new role, and asked the designer rather than assuming either way when the roles' conceptual labels differed (e.g. accent vs. surface).
- The scope (current selection vs. whole file) was asked upfront, not assumed or inferred from trigger wording, this run.
- If scope was the whole file, the ambiguity review in Phase 2 was grouped by page and then by frame, not presented as one undifferentiated list.
- The skill ran correctly on a brand file with zero prior tokenization — it does not depend on any brand-specific node names, page names, or hex values baked into this skill.
- Every decision point the designer was asked to resolve this run was phrased as lettered/numbered options, not an open-ended question — checked across both agents this skill runs in, Figma's own agent and Claude.
- Every single question asked this run — with no exceptions — included a final custom-answer option alongside its lettered choices, so the designer was never limited to only the canned options.
- Every newly-created variable/token name this run is entirely lowercase, confirmed by re-reading the written collection(s) after Phase 5 — and if any pre-existing uppercase/mixed-case variables were found, the designer was explicitly asked (not assumed) whether to rename those too.
- If Phase 7 ran, it only ran after explicitly confirming type was already tokenized (or getting an explicit answer on how to proceed otherwise) — never as an automatic follow-on to Phase 6.
- If Phase 7 built styles, every style's grouping and naming was proposed and confirmed with the designer before being created, and the report explicitly distinguished which properties were bound through a variable (size) versus set directly (weight, line height, tracking).
- If Phase 7 bound styles to nodes, the exact-match pass never rounded, the report broke out directly-bound vs. instance-inherited vs. unmatched (further split into no-match vs. mixed-formatting) counts, and a visual check was performed (or its absence explicitly disclosed) before declaring the binding complete.
- If Phase 7 snapped near matches, this only happened after a separate explicit go-ahead with the match tolerance and irreversibility disclosed up front, and the report broke the changes out by type (weight/size/tracking/line-height) rather than a flat count, flagging line-height changes for follow-up.

---

## Reference Notes — Lessons From Calibrating This Skill

These are generalized lessons from testing this skill against a real, existing multi-brand token system — not tied to any specific brand, company, or file, and not a fact about every design system you'll ever run this against. Treat them as things worth checking for, not defaults to assume:

- **Don't assume a flat, primitive-only structure is the norm.** A real system this skill was tested against turned out to have a fully-developed semantic layer (role-based tokens aliasing primitives, e.g. `button-primary-bg`, `text-primary`, `surface-page`) documented right alongside the primitives — which is exactly why Phase 3 always asks rather than defaulting either way.
- **A convention can span more than colors.** The same system had matching primitive/semantic conventions for space, radius, shadow, opacity, and type — not just color — and even had a separate "Responsive" layer aliasing values per breakpoint (desktop/tablet/mobile). Worth checking whether a file's convention extends beyond color before assuming it's color-only.
- **Spacing bases aren't always a plain pixel grid.** One real system used a quarter-of-4px base unit (so a token like `Space/0-25` meant 0.25 × 4 = 1px) rather than a whole-pixel scale — always verify the actual base unit found in Phase 4 rather than assuming 4px.
- **Type systems can fork by context, not just by size.** The same semantic type-scale step (e.g. a heading size) resolved to different font families depending on context in one real system — worth checking whether "the same" semantic token might need to branch by context rather than assuming one-to-one mapping to a primitive.
- **Watch for naming collisions between a collection's own name and a prefix reused inside it.** In one real system, the primitive color collection was itself named "Colors," while every semantic color token's name *also* started with a "Colors/" prefix — meaning the word meant two different things depending on whether you were reading a collection name or a token name. Avoid this overlap when proposing new collection names (e.g. name a primitive-hue collection something unambiguous like `Primitive` rather than reusing a word that will also prefix semantic token names).
- **Some values live in Styles, not Variables.** Named gradients, in particular, are commonly defined as Figma Styles rather than Variables — which is why Phase 0, Step 2 checks both, not just Variables.
- **A design-context/code-generation tool is not a substitute for direct node traversal in Phase 1.** Observed directly: such a tool undercounted a color's true occurrence count by a wide margin and missed an entire additional cluster outright, with stroke-only values especially likely to be dropped. Always scan by walking the node tree directly (`findAll`/`findAllWithCriteria` reading real `fills`/`strokes`/text/spacing properties), and treat any earlier design-context-style read from the same session as informal orientation only, never as the Phase 1 scan itself.
- **Value-based clustering alone misses same-role duplicates.** When consolidating a real multi-brand system's color primitives into hue families, near-duplicate values that were close enough to auto-group weren't the only issue — some values playing the exact same role (e.g. a standalone accent color that functionally duplicated a shade already covered by a broader hue family) only surfaced once usage context, not just numeric proximity, was checked. This is why the cross-cluster role-matching pass exists as its own step rather than trusting the tolerance dedupe to catch everything.
- **A real file can already have mixed-case legacy variables sitting next to newly-lowercased ones.** In one real run, a designer asked for every variable this skill creates to be lowercase, then separately decided whether to also rename older, pre-existing uppercase variables found elsewhere in the file (an older `Responsive` collection, in that case) — treating those as two different decisions rather than one. That run also surfaced that a file's collection list can shift between sessions for reasons outside this skill's control (collections merged or removed outside of this skill's own actions) — worth a quick "does this match what you expect?" check with the designer rather than assuming the file's current state is exactly what this skill itself produced.
- **Building text styles from already-tokenized type is a distinct, frequently-requested next step, not a continuation of tokenization itself.** In one real run, a designer whose type sizes were already bound to `Type/*` variables asked specifically for Text Styles (Display, Body, Text, Label groups) built from those variables, skipping the tokenize scan entirely. The resulting styles bound only font size through a variable — weight, line height, and tracking were set directly on each style, since those weren't tokenized in that file. Two groups at the same pixel size (e.g. a 16px paragraph style and a 16px UI-label style) legitimately used the same bound size variable but different line heights, and were kept as separate styles rather than merged.
- **The same primitive color can legitimately serve two different semantic roles without needing two semantic tokens.** In one real run, several accent colors in the file turned out to already be used as literal surface backgrounds elsewhere, not just as CTA accents — the designer caught this after an initial answer assumed accent and surface needed separate names. The principle that came out of it: only split a primitive's semantic naming into two tokens when there's a concrete reason the two uses might diverge in value later (a future rebrand, a planned surface-only restyle); if an accent color and a surface use the exact same shade with no reason to ever separate them, reuse the one semantic token for both purposes rather than minting a role-specific duplicate. This is deliberately a different check from the Phase 2 cross-cluster role-matching pass — that one looks at whether two *different* raw values should collapse into one primitive; this one looks at whether one *already-tokenized* primitive is about to get an unnecessary second semantic name.
- **Binding styles to existing nodes surfaces its own long tail, and it's worth doing in stages.** In the same run, an exact-match-only binding pass covered about 74% of the scope's text nodes on the first attempt (matching font, weight, size, line height, tracking, and case precisely), with roughly a third of those picked up automatically through component instances inheriting from a bound main component rather than being touched individually. A second, smaller pass against newly-identified exact-match groups picked up a further few percent. The remaining unmatched nodes broke down into a genuine long tail (one-off uppercase labels with unusual tracking, icon glyphs mislabeled as text, a wordmark with its own tracking) that a near-match snap (same family/case, size within 1px, weight within one step) could resolve for most of the rest — but the snap measurably changed weight, size, tracking, or line height on every node it touched, confirmed only after an explicit, separate go-ahead, and could not be automatically reverted afterward since original per-node values weren't recorded before the overwrite.