---
name: "documentation"
description: "Documents componentized Figma component sets as readable, token-bound documentation frames placed on each component's own category page under its Documentation banner (anatomy with leader lines, properties, a specimen-style variants and states layout, usage, rules, content guidelines, accessibility, engineering handoff with a token-to-property mapping, and for new projects a three-layer code reference), and builds a separate token library on its own pages: a Tokens page (primitive and semantic tables with swatch, value and usage), a Colors page (swatch cards by family) and a Typography page (type specimens). Optional Markdown files. Organizes the file's page list: a title page and the token library pages (alphabetical) at the top, a divider, a Components title page, a divider, then every other page. Use for 'document this component', 'write docs for this', 'document my tokens', 'build the colors and typography pages', or 'hand this off to engineering'."
---

# Documentation (evidence-based component docs, token library, engineering handoff)

> Turns a componentized, token-bound component set into documentation a designer, a reviewer, and an engineer can all work from. Third skill in the chain Tokenize Selection -> Componentize Selection -> Document Component.

> Documentation goes stale the moment it is written by hand. This skill reads
> what the component actually is (its layers, properties, variants, bindings,
> and real usage in the file), drafts the parts that need a human voice
> (purpose, rules, guidance), has the designer confirm those, and then builds
> docs from live instances and real token bindings so they stay in sync. It
> never states something as fact that it did not read from the file, and it
> never invents a token, a variant, or a rule silently.

---

## Quick Start

**What to say:** "Document this component" / "Write docs for the button" / "Document my tokens" / "Hand this off to engineering". You don't need an exact phrase. The skill asks what scope you mean before doing anything.

**How it asks:** Every question comes as tappable multiple-choice options with a custom-answer choice always available. See "How This Skill Asks Questions".

**What you need:** A component set (or component) built by `componentize`, whose fills, strokes, radius, spacing, and type are bound to Figma Variables. For the token library pages, you need Variable collections (and, for Typography, text styles) in the file.

**What you get, in order:**
1. Upfront questions on what to document, what to produce, where it goes, and whether engineering needs generated code (Phase 0)
2. A prerequisite check: the component is bound, tokens exist, and any existing docs are surfaced (Phase 0)
3. A read-only evidence ledger: anatomy, properties, variants, bindings, untokenized values, real usage, computed contrast (Phase 1)
4. Drafted narrative content (purpose, usage, rules, content guidelines, behavior) for you to confirm, with every checkable rule tested against the file (Phase 2)
5. A token library on its own pages: Tokens (primitive and semantic tables), Colors (swatch cards by family) and Typography (type specimens), each under a title banner (Phase 3)
6. Engineering handoff (Phase 4): a token-to-property mapping by default, or, for new projects with no built components, a three-layer code reference (tokens, component, usage) generated from the real bindings in your developers' stack (React with TypeScript by default)
7. A proposal of everything that will be written and where (Phase 5)
8. The documentation, built on each component's category page under its Documentation banner, replacing the placeholder frame, and the page list organized into a Brand Guidelines group and a Components group (Phase 6)
9. A verification report with anything that could not be written (Phase 7)

---

## What This Skill Does NOT Do

- **Does not create or edit tokens, components, or variants.** Missing tokens are reported and routed to `tokenize-selection-v2`; missing or unbound components are routed to `componentize`. Writing usage notes back onto Variable descriptions is a separate, opt-in step (Phase 3) because it modifies shared resources.
- **Does not document unbound work.** If the component is mostly raw values, stop and route to `tokenize-selection-v2` then `componentize`.
- **Does not ship production code.** Snippets are reference implementations generated from the design. They show the token usage, structure, props, and states. Engineering owns behavior, data flow, and testing. Say this in the docs.
- **Does not audit drift across the file.** Rule checks (Phase 2) are scoped to the rules being documented.
- **Does not publish anywhere outside the file or the session's output folder** (no repo commits, no wiki pages) unless the designer asks and a connector for it is available.

---

## How This Skill Asks Questions

**Every question is multiple choice, and every multiple-choice question always includes a custom-answer option as the last choice.**

- **Use the structured-choice tool** (`ask_user_input_v0`) when available. Otherwise write a lettered list and tell the designer they can reply with a letter.
- **Custom option is always last**, labeled plainly ("Something else (I'll type it)"). If chosen, wait for the typed answer and restate it back before acting.
- **At most 3 real choices plus the custom option.** Group or split decisions rather than dropping a real choice.
- **Recommended option first**, marked "(recommended)", only when there is a real reason.
- **One decision per question, at most 3 questions per round.**
- **Name what you ask about precisely** (layer name, page, component set name, count, node ids). Do not attach screenshots by default.
- **When the designer asks "what are you referring to?"** (or "which one?", "what do you mean?"), do not reword. Show the exact component, token, or frame (screenshot, or native annotation on the exact nodes), with its name, page, and node ids, then repeat the same question unchanged.
- **Temporary markers are removed** once the question is answered.
- **Batch approvals are still multiple choice:** "Approve all", "Review individually", plus the custom option ("approve all except these").
- **Stop after asking.** End the turn and wait.
- **Never turn a statement of fact into a question.** If the file answers it, read the file.

---

## Phase 0 - Confirm Scope, Outputs, Conventions, Prerequisites

### Step 1 - Ask scope, outputs, and destination (Round 1)

Always ask, regardless of how the skill was invoked.

1. **What should I document?** "Selected component set `<name>` (`<node id>`)" (first, recommended when something is selected), "All component sets on this page (N found)", "Whole file (N component sets across M pages)", custom. If nothing is selected, say so and list the component sets found.
2. **What should I produce?** "Component docs + token library pages (recommended)", "Component docs only", "Token library pages only", custom.
3. **Where should it go?** "Docs frames in Figma (on each component's category page) + Markdown files (recommended)", "Docs frames in Figma only", "Markdown only", custom. If the session cannot create files, say so and offer the Markdown in chat instead.

### Step 2 - Ask whether code is needed, then conventions (Round 2)

Generated code is **optional**. Where engineering has already built the components, or engineers and AI tools can read the real component code, generated snippets compete with the real source and drift from it. The props table and the token-to-property mapping stay useful either way. Code earns its place mainly in new projects with no built components.

Before asking, look for evidence and report what you found: Code Connect mappings on the component (`get_code_connect_map`), and any codebase sample the designer has pasted.

1. **Does engineering need generated code for this?** "No: components are already built, or engineers/AI reference the real code. Give me the token-to-property mapping instead", "Yes: new project, no built components yet", custom. Mark the option the evidence supports as "(recommended)"; with no evidence, recommend neither.

2. **Which stack will the developers use?** Ask only if code is wanted, and phrase it plainly ("what your developers build the interface in"). "React with TypeScript (default if you're not sure)", "React with JavaScript", "WordPress (PHP)", custom (for example Vue, Angular, or plain HTML and CSS).
3. **Which kind of WordPress theme?** Ask only if WordPress was chosen. "Classic theme: PHP templates and a stylesheet (default if you're not sure)", "Block theme: configured with `theme.json`", custom.

React is a library, and TypeScript and JavaScript are the languages it is written in; WordPress is built on PHP. Designers often don't know which applies. If they are not sure, use React with TypeScript, and state in the docs that the stack is a default that has not been confirmed with engineering. When Code Connect mappings exist, they record the framework they target; use that as evidence and recommend the stack they indicate.

The three layers (tokens, component, usage) are the same for every stack. Only the component layer and the usage example change with the stack (see 4e). Token naming in code is confirmed in Phase 4a, not asked here.

---

### Step 3 - Prerequisite check (read-only)

- **Component exists.** Each target is a component set or component. A loose frame is routed to `componentize`.
- **Bound.** Walk every descendant of every variant and list any fill, stroke color, stroke weight, corner radius, spacing, font size, or effect not bound to a Variable. Report the count and nodes. A few leftovers: ask whether to document them as "untokenized" (they appear in a flagged list) or pause to bind them first. Mostly unbound: stop and route.
- **Tokens exist.** The file has Variable collections. State which collections exist and their modes (for example light/dark). Never assume a semantic layer exists.
- **Page structure.** Read every page's top-level nodes. A file built by the current `componentize` has category pages (for example Buttons, Cards, Navigation), each with a **Title banner**, a **Documentation banner** over a left column, and a **Components banner** over the component sets on the right. The left column holds an `Overview / <Component>` placeholder for each component. Record, per page: the banner node ids, their widths and x positions, the placeholder node ids and positions, and the component sets. Documentation is built in the left column under the Documentation banner (see Phase 6). If a page has no banners, ask where to place the docs.
- **Existing docs.** Look for frames named `Docs/<Component>` or similar and for real documentation (anything beyond a placeholder). A placeholder is a frame whose text says it is an overview only. Placeholders are replaced in place, after one explicit yes in Phase 5 ("Replace each placeholder in place (recommended)", "Keep them and build the docs below each one", custom). If real documentation for the same component exists, ask: "Build a new version beside it (recommended)", "Replace it in place", "Cancel", custom.
- **Token library pages.** Search the file's pages by name (case-insensitive: Colors, Color, Typography, Type, Tokens, Foundations, Design tokens) before creating anything. Reuse a matching page, and ask before replacing real content on it. If none exists, the pages are created in Phase 6 with title banners that match the file's existing banners.
- **Page list.** Read the order of every page and note any title or divider pages the file already uses (empty pages whose names are an emoji and a title, or a row of dashes). Reuse the file's own convention if it has one. Otherwise Phase 6 organizes the page list as described under "Page organization".
- **Fonts.** Check that a monospace font is available for code blocks. If none loads, report it and ask which font to use; never fall back silently.
- **Surface context.** Note whether the file has Text Styles, a docs/cover convention, and a `surface/*` token suitable for the docs page background and code blocks. Docs furniture must use existing tokens (see Hard Rules).

---

## Phase 1 - Read the Component (read-only, evidence ledger)

Use **direct node traversal** (`findAll` / `findAllWithCriteria`, reading real properties). Never use a design-context or code-generation tool as a substitute: such tools sample and summarize and will miss variants, stroke-only values, and hidden layers. Load `figma-use` before the first `use_figma` call.

Build an evidence ledger per component:

**1a - Anatomy.** The layer tree of the default variant: layer name, type, role, auto-layout direction, padding, item spacing, sizing mode (hug/fill/fixed). For each part, record whether it is **required** or **optional** (optional = visibility is driven by a boolean property) and which property controls it. Record nested instances by their main component (atoms inside the molecule) and whether each is swappable.

**1b - Properties.** From `componentPropertyDefinitions`: name, type (variant, boolean, text, instance-swap), default, allowed values, and which layers each property is linked to (`componentPropertyReferences`).

**1c - Variants.** Every variant with its property values and size. Read each variant's description for internal bookkeeping only (whether the variant was drawn in the file or authored from existing tokens); **do not print this origin in the docs, the code comments, or the Markdown**, because it is not information readers need. Record variants that are missing for a combination (for example Size=Sm exists only for one Style and Theme) so the docs can list them in one line.

**1d - Bindings.** For every variant, every node, every bound property: the Variable, its collection, its alias chain, its resolved value per mode, and its scopes. Build a token-by-variant matrix. Note any primitive bound directly (not through a semantic token).

**1e - Untokenized values.** Font weight, line height, letter spacing, stroke weight, effects, and opacity are not covered by `tokenize-selection-v2` and are often literal. List each literal value found, per variant. These feed the code snippets as flagged literals.

**1f - Real usage.** Within the confirmed scope: instance count per variant, which pages and frames they sit in, the nearest ancestor fill (token name) for each, text and property overrides, and any remaining look-alikes that are not instances. This is evidence for the "when to use" and "rules" sections.

**1g - Computed accessibility.** For every text and background pair across variants and states, resolve both colors through their alias chains, composite semi-transparent fills over the nearest opaque ancestor, and compute the WCAG contrast ratio: `(L1 + 0.05) / (L2 + 0.05)` using relative luminance. Thresholds: 4.5:1 normal text, 3:1 large text (at least 24px, or 18.66px bold), 3:1 for non-text UI boundaries and focus indicators. Also record the rendered height and width of interactive components against a 24px minimum target (WCAG 2.2 AA) and a 44px recommended target. Where a pair cannot be resolved (gradient, image, unresolved alias), report "not computed" with the reason. Never estimate by eye.

**1h - Existing text.** The component set's description and each variant's description as written by `componentize`. Reuse and extend them; do not contradict them.

Present a short summary of the ledger in chat (counts, anything surprising, anything that failed Phase 0). Do not paste the whole ledger.

---

## Phase 2 - Draft and Confirm Narrative Content

**Core guarantee: every sentence in the docs is either read from the file, a stated chain convention, or confirmed by the designer. Nothing unconfirmed is published.**

Draft each item below, tagging it internally as **Evidence** (read from the file), **Convention** (a chain-wide rule from this skill), or **Proposed** (needs a human decision). Show tags in chat only; the final docs do not carry them.

| Section | Source | What to draft |
|---|---|---|
| Purpose | Proposed, informed by 1f usage context and the existing description | One or two sentences: what it is and what job it does |
| When to use / When not to use | Proposed, informed by 1f | Short lists. "Not to use" should name the component to use instead when one exists in the file |
| Choosing a variant | Evidence + Proposed | One line per variant axis value, grounded in where each is used in the file |
| Rules | Evidence, Convention, Proposed | See "Rules" below |
| Content guidelines | Evidence + Proposed | Label length range, casing, and punctuation observed across instances; flag outliers |
| Behavior | Proposed | Hover/press/focus/disabled behavior and transitions. Figma geometry cannot prove transitions; ask |
| Semantics and keyboard | Proposed | The HTML element to use (for example `button` vs `a`), keyboard interaction, and ARIA, per standard patterns for that element. Ask which element; do not infer it from appearance |
| Responsive | Evidence | From sizing modes (hug, fill, fixed) and constraints |
| Status | Proposed | Stable, beta, or deprecated, plus a version label if the designer uses one |
| Related components | Evidence | Components nested inside it, components it nests in, and look-alikes in the file |

### Rules

Sort every rule into one of three groups and say which group in the question:

- **Evidence rules:** facts the file shows (for example "every Primary instance sits on a `surface/*` background", "the icon is only ever shown with a label").
- **Convention rules:** chain-wide rules (bind semantic tokens, respect scopes, no hardcoded values).
- **Proposed rules:** human-judgment guidance (for example "one Primary per section"). Propose only what 1f evidence makes plausible, and phrase it as a proposal.

**Test every checkable rule against the file before asking about it.** For a rule such as "one Primary per section", traverse the confirmed scope and count violations. Report "N of M sections follow this; violations at: <layer names, pages, node ids>". The designer then decides: "Keep the rule and list violations as follow-ups", "Soften or rescope the rule", "Drop the rule", custom. Never publish a rule the file contradicts without the designer knowing.

### Confirmation flow

Present the drafts grouped by section, then ask per group, as multiple choice: "Approve all", "Review individually", custom. Edits arrive through the custom option; restate the edited text back before treating it as confirmed. Cap each round at 3 questions.

---

## Phase 3 - Token Library (separate pages, not part of the component docs)

Runs when the designer chose the token library (alone or with component docs). Read-only until Phase 6. The component docs do **not** carry a per-component "tokens used" table: it is low value there. Each component's docs show only the token-to-property mapping and the tokens shown inline in the anatomy legend. Full token detail lives here.

### 3a - Read every variable in the confirmed scope

Per collection and mode, and per variable:

- Name, collection, type (color, float, string), and whether it is primitive or semantic (semantic = aliases another variable; primitive = holds a raw value)
- Resolved value per mode, and the alias target for semantic variables
- Scopes (`variable.scopes`)
- Description (the usage note `tokenize-selection-v2` writes, if present)
- Usage count by direct traversal of bound nodes, and which component sets bind it
- For primitives: which semantic tokens alias them ("aliased by")

Also read the file's **local text styles** (name, font family and style, size, line height, letter spacing, case, and whether size is bound to a variable) and any **paint styles** with gradients. Read every mode: if the file has responsive text modes (for example Desktop, Tablet, Mobile), the Typography page shows one card per mode.

### 3b - Three artifacts, each on its own page, each under a title banner

Model them on the designer's reference screens: clean white cards, large readable type, real fills.

1. **Tokens page.** A table of primitive tokens and a table of semantic tokens, grouped by family or role with a subheading per group. Primitive columns: **token** (a round or square swatch that is a real fill bound to the token, then the name), **value** (hex for colors, px for numbers; opacity shown as stored with a note), **usage** (the Variable's description, or the approved drafted note). Semantic tables add an **alias** column. Scopes, rules, usage counts and "used by" do not go in these tables: put the scope rules in a short Rules section above them and the counts and zero-usage flags in Known issues. Keep each table narrow enough to read at normal zoom.
2. **Colors page.** Swatch cards grouped by family (for example Neutral, Brand/Green, Brand/Spice), the way the designer's reference does it: a large swatch that is a real fill bound to the token, then the level or name, a one-line usage, and the hex. Semantic colors get a second section grouped by role, each card also showing its alias. Gradients, if the file has paint styles for them, get cards that show the styled gradient and its stops. A token whose scopes forbid a fill (text-scoped, stroke-scoped, effect-scoped) is shown inside its scope: an "Aa" on a contrasting backdrop for text, an outlined box for strokes, a labeled shadow for effects.
3. **Typography page.** One card per type group (Display, Body, Label, and so on, or per mode), each listing every text style as a row: a small spec column (style name, font family and weight, size, line height, letter spacing, case) beside a specimen line that **uses the real text style** (apply it with `setTextStyleIdAsync`) with the same sample sentence throughout, so the page tracks the styles. Show the type-size variables (name and value) in a compact table beside or below the cards. Add the note the file's own team would need, such as which frames or modes the styles require, only if it is read from the file.

Only tokens and styles that exist are shown; never add a level the file does not have.

### 3c - Token rules

Three sources, same grouping as component rules:

- **Convention (always stated):**
  1. Components bind semantic tokens. A primitive is bound directly only with explicit designer approval.
  2. A token is bound only to properties inside its scopes. A text-scoped token is never a fill or stroke; a stroke-scoped token is never a fill.
  3. All token names are lowercase.
  4. Do not create a second semantic token for a use that has no reason to diverge from an existing one. Split only when the two uses might reasonably diverge in value later.
  5. New or changed tokens go through `tokenize-selection-v2`, not through component work.
  6. Engineers consume tokens by name through the token layer (Phase 4), never by copying resolved values.
- **File-derived rules (detected, then confirmed):** patterns the traversal actually shows, for example alias chain depth (semantic -> primitive only, or deeper), a semantic-only family (a role with no standalone primitive), a role named by tone instead of structural role, or a base unit for spacing. Detect these from the data; do not assume them.
- **Per-token usage and rules:** use the Variable's own description when present. When a token has no description, draft a usage note from its scopes, its role prefix, and where it is actually bound, and ask the designer to approve the batch. Never invent a rule for a token from its name alone.

### 3d - Flags to surface (report, don't fix)

- Tokens with zero usages (for example newly added levels not yet bound to any node)
- Primitives bound directly by components
- Semantic tokens whose scope does not match how they are bound
- Mixed-case or otherwise nonconforming names
- Two semantic tokens with the same resolved value and no stated reason to diverge
- Collection or mode differences from what the designer expects

Ask once about the flags as multiple choice: "Document them in a 'Known issues' section (recommended)", "Leave them out of the docs", custom.

### 3e - Optional write-back (separate, explicit)

If tokens lack descriptions, offer to write the approved usage notes onto the Variables' descriptions so Figma's own UI shows them. This modifies shared resources. State the exact count, ask as its own question ("Write N descriptions", "Keep notes in the docs only (recommended)", custom), and expect the environment's permission check to ask for an explicit confirmation. Do not retry a smaller version if blocked.

---

## Phase 4 - Engineering Handoff (token mapping, or code reference)

Branch on the Phase 0 answer. Generate everything from the Phase 1 ledger, never from visual appearance.

### Branch A - Components already built (default): token-to-property mapping

No code. Produce, per component, a table engineers can check the real component against:

| Part | Property | Variant / state | Token | CSS custom property |
|---|---|---|---|---|
| `<layer name>` | background | emphasis=primary, state=hover | `cta/primary-fill-hover` | `--cta-primary-fill-hover` |
| `<layer name>` | padding | size=md | `space/sm`, `space/lg` | `--space-sm`, `--space-lg` |

Follow it with the props table (Phase 1b) and the untokenized literals (Phase 1e), both of which engineers need whether or not code is generated.

### Branch B - New project: three-layer code reference in the developer's stack

The three layers were confirmed by the team's engineer from a reference example (written there in React with TypeScript and CSS Modules). Always produce them in this order, adapted to the stack chosen in Phase 0 (see 4e):

1. **Tokens layer.** For every stack except a WordPress block theme, `tokens.css`: CSS custom properties on `:root`, grouped with comments: primitives first, then semantic tokens that **reference the primitives** (`--surface-cream: var(--tan-500);`) so the alias relationship survives into code, then space, radius, and type. One block per mode (for example `[data-theme="dark"]`) if the file has modes. Include every token the documented components use; the full list lives on the Tokens page. For a WordPress block theme, the tokens layer is `theme.json` instead (see 4e).
2. **Component layer.** The component and its styles, in the chosen stack's idiom. Figma variant properties become the stack's native way of choosing a variant (typed props in React with TypeScript, validated arguments in PHP), boolean properties become booleans, text properties become `children` or a named argument, and instance-swap properties become slots. States come from the set's State values, not guessed ones. Every bound value references a token. The root element is the one confirmed in Phase 2. In the default stack this is two files: `<Component>.tsx` (typed props mirroring the Figma properties, class composition, rest props passed to the native element) and `<Component>.module.css` (base rule first, then `/* Variants */`, `/* States */`, and `/* Sizes */` sections, every bound value `var(--token)`).
3. **Usage example.** A short file in the chosen stack that renders the component in context: a default, then one per variant axis, then optional parts on and off. Styling in the example uses tokens too, not literals.

### 4a - Token-to-code mapping (shown and confirmed)

Default mapping mirrors the Figma names, with no added category prefix:

| Figma variable | CSS custom property |
|---|---|
| `surface/cream` | `--surface-cream` |
| `cta/primary-text` | `--cta-primary-text` |
| `tan/500` | `--tan-500` |
| `space/2xl` | `--space-2xl` |

Rule: slashes become hyphens. If engineering prefers a category prefix (for example `--color-surface-cream`), apply one consistent prefix map. Show the mapping table for this file and get approval before generating.

**WordPress block theme.** Names follow the custom properties WordPress generates from `theme.json`, so the mapping differs by token type:

| Figma variable | `theme.json` location | Generated custom property |
|---|---|---|
| `surface/cream` (color) | `settings.color.palette`, slug `surface-cream` | `--wp--preset--color--surface-cream` |
| `space/sm` | `settings.spacing.spacingSizes`, slug `sm` | `--wp--preset--spacing--sm` |
| `type/body/md` (font size) | `settings.typography.fontSizes`, slug `body-md` | `--wp--preset--font-size--body-md` |
| `radius/md`, primitive families | `settings.custom` (nested keys) | `--wp--custom--radius--md`, `--wp--custom--tan--500` |

Under `settings.custom`, camelCase keys become kebab-case and each nesting level is joined with `--`. Show this table for the file and get approval before generating, as with the default mapping.

### 4b - Code rules

- **Tokens, not values.** Every bound property references a token. A raw hex, rgb, or pixel value in a property that is bound in Figma is a defect.
- **Untokenized literals are flagged, not hidden.** Weight, line height, letter spacing, stroke weight, effects, and opacity that are literals in Figma appear as literals with a trailing `/* untokenized in Figma */` comment, and are listed again in the docs under "Untokenized values".
- **State parity.** Every variant and state in the Figma set has a matching style. Missing combinations are not implemented and are listed.
- **No invented behavior.** Do not add transitions, animation, loading states, or props the component set does not have. Behavior confirmed in Phase 2 may be added and commented as such.
- **Never remove a focus indicator without a replacement** that passes the Phase 1g contrast check.
- **Match the chosen stack's idioms.** Write the component the way developers in that stack write it (typed props and CSS Modules in React with TypeScript; JSDoc-documented props in React with JavaScript; template parts with escaped output in classic WordPress). Do not carry one stack's patterns into another.
- **Escape all dynamic output in PHP** (`esc_attr`, `esc_html`, `esc_url`). Never echo a raw argument.
- **Never present a default stack as confirmed.** If the designer was unsure, the docs say the stack is a default.
- **Reference quality.** Show imports (or `get_template_part` / enqueue calls). Keep each file short enough to read on one screen where possible.

### 4c - Verify the code

- Compare the code's variant and state styles against the Phase 1 variant list: counts and names must match, with every gap listed.
- Scan for literal colors and pixel values on properties that are bound in Figma; there should be none.
- When a shell is available, syntax-check the snippets (for example compile the `.tsx` with `esbuild` or `tsc --noEmit` against stubbed imports, check JavaScript with `node --check`, and lint PHP with `php -l`). If it cannot be run, say so plainly and do not claim the code was compiled.

### 4d - Skeleton: React with TypeScript, the default (fill every part from the ledger, never ship placeholders)

```css
/* tokens.css - generated from Figma Variables (collections: <primitive>, <semantic>) */
:root {
  /* Primitive */
  --tan-500: <resolved>;

  /* Semantic (aliases) */
  --surface-cream: var(--tan-500);

  /* Space */
  --space-sm: <resolved>px;

  /* Radius */
  --radius-md: <resolved>px;
}
```

```tsx
// <Component>.tsx
import React from 'react';
import styles from './<Component>.module.css';

/** Figma component set: <Set name> (<node id>) */
export interface <Component>Props
  extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  /** Figma property: Emphasis (default: primary) */
  emphasis?: 'primary' | 'secondary';
  /** Figma property: Size (default: md) */
  size?: 'sm' | 'md';
}

export const <Component>: React.FC<<Component>Props> = ({
  children,
  emphasis = 'primary',
  size = 'md',
  className = '',
  ...props
}) => {
  const classes = `${styles.root} ${styles[emphasis]} ${styles[size]} ${className}`;
  return (
    <button className={classes} {...props}>
      {children}
    </button>
  );
};
```

```css
/* <Component>.module.css */
.root {
  border-radius: var(--radius-md);
  font-weight: <literal>; /* untokenized in Figma */
}

/* Variants */
.primary { background-color: var(--cta-primary-fill); color: var(--cta-primary-text); }

/* States */
.root:hover:not(:disabled) { background-color: var(--cta-primary-fill-hover); }
.root:focus-visible { /* from Figma State=Focus, tokens only */ }
.root:disabled { /* from Figma State=Disabled, tokens only */ }

/* Sizes */
.md { padding: var(--space-sm) var(--space-lg); }
```

```tsx
// Usage.tsx
import { <Component> } from './<Component>';

export default function Example() {
  return <<Component> emphasis="primary" size="md">Label</<Component>>;
}
```

### 4e - Stack adapters

| Stack | Tokens layer | Component layer | Usage example |
|---|---|---|---|
| React with TypeScript (default) | `tokens.css` | `<Component>.tsx` + `<Component>.module.css` | `Usage.tsx` |
| React with JavaScript | `tokens.css` | `<Component>.jsx` + `<Component>.module.css`; no type annotations, props documented with JSDoc, defaults in the function signature | `Usage.jsx` |
| WordPress, classic theme | `assets/css/tokens.css`, enqueued by the theme | `template-parts/<component>.php` + `assets/css/<component>.css` | A `get_template_part()` call |
| WordPress, block theme | `theme.json` | A block pattern, or a block's `render.php`, plus its stylesheet; tokens referenced as `var(--wp--preset--...)` or `var(--wp--custom--...)` | Pattern markup or block usage |
| Anything else the designer names (Vue, Angular, plain HTML and CSS) | `tokens.css` | The framework's own component idiom and styling convention; for plain HTML and CSS, a markup snippet with classes plus a stylesheet | A short usage file |

For a stack not in this table, keep the same three layers and the same token and state rules, follow that stack's common conventions, and say in the docs that the snippets for that stack are best effort and engineering should confirm them.

**WordPress, classic theme (skeleton).** Use the element confirmed in Phase 2 (`<a>` or `<button>`):

```php
<?php
/**
 * Template part: <Component>
 * Figma component set: <Set name> (<node id>)
 *
 * Usage: get_template_part( 'template-parts/<component>', null, array(
 *   'emphasis' => 'primary', 'size' => 'md', 'label' => 'Label', 'url' => '#',
 * ) );
 */

$args = wp_parse_args(
	$args,
	array(
		'emphasis' => 'primary', // Figma property: Emphasis (default: primary)
		'size'     => 'md',      // Figma property: Size (default: md)
		'label'    => '',        // Figma text property: Label
		'url'      => '',
	)
);

$emphasis = in_array( $args['emphasis'], array( 'primary', 'secondary' ), true ) ? $args['emphasis'] : 'primary';
$size     = in_array( $args['size'], array( 'sm', 'md' ), true ) ? $args['size'] : 'md';
$classes  = array( '<component>', '<component>--' . $emphasis, '<component>--' . $size );
?>
<a class="<?php echo esc_attr( implode( ' ', $classes ) ); ?>" href="<?php echo esc_url( $args['url'] ); ?>">
	<?php echo esc_html( $args['label'] ); ?>
</a>
```

```php
// functions.php: load tokens first, then the component stylesheet
add_action( 'wp_enqueue_scripts', function () {
	$version = wp_get_theme()->get( 'Version' );
	wp_enqueue_style( 'theme-tokens', get_theme_file_uri( 'assets/css/tokens.css' ), array(), $version );
	wp_enqueue_style( 'theme-<component>', get_theme_file_uri( 'assets/css/<component>.css' ), array( 'theme-tokens' ), $version );
} );
```

```css
/* assets/css/<component>.css */
.<component> {
  border-radius: var(--radius-md);
  font-weight: <literal>; /* untokenized in Figma */
}

/* Variants */
.<component>--primary { background-color: var(--cta-primary-fill); color: var(--cta-primary-text); }

/* States */
.<component>--primary:hover { background-color: var(--cta-primary-fill-hover); }

/* Sizes */
.<component>--md { padding: var(--space-sm) var(--space-lg); }
```

```php
<?php get_template_part( 'template-parts/<component>', null, array( 'emphasis' => 'primary', 'size' => 'md', 'label' => 'Label', 'url' => '#' ) ); ?>
```

**WordPress, block theme (tokens layer).** Keep the project's existing `$schema` and `version`. Primitives live under `settings.custom`, and palette entries reference them so the alias relationship survives:

```json
{
  "settings": {
    "custom": {
      "tan": { "500": "<resolved>" },
      "radius": { "md": "<resolved>px" }
    },
    "color": {
      "palette": [
        { "slug": "surface-cream", "name": "Surface Cream", "color": "var(--wp--custom--tan--500)" }
      ]
    },
    "spacing": {
      "spacingSizes": [
        { "slug": "sm", "name": "Small", "size": "<resolved>px" }
      ]
    }
  }
}
```

---

## Phase 5 - Proposal and Confirm

Show one table of everything that will be written, with where each item goes:

| Item | Source | Destination |
|---|---|---|
| Header, overview, purpose | Phase 2 (confirmed) | Docs frame, Markdown |
| Anatomy: live instance, numbered markers joined to parts by leader lines, legend | Phase 1a | Docs frame, Markdown (legend table) |
| Properties table | Phase 1b | Docs frame, Markdown |
| Variants specimen: sizing cards, content options, state matrix by style and theme | Phase 1c | Docs frame (Markdown lists the variants) |
| Usage: when / when not / choosing | Phase 2 | Docs frame, Markdown |
| Rules (evidence / convention / proposed) | Phase 2 | Docs frame, Markdown |
| Content guidelines, behavior, semantics and keyboard | Phase 1f / Phase 2 | Docs frame, Markdown |
| Accessibility (computed) | Phase 1g | Docs frame, Markdown |
| Untokenized values | Phase 1e | Docs frame, Markdown |
| Engineering handoff: token-to-property mapping, then (new projects) the three code layers at the bottom | Phase 4 | Docs frame, Markdown |
| Related components, known issues, changelog stub | Phase 1/2 | Docs frame, Markdown |
| Tokens page, Colors page, Typography page (each under a title banner; existing pages reused) | Phase 3 | New or existing pages, Markdown (tokens) |
| Page list: Brand Guidelines title page, the token library pages in alphabetical order, a divider, a Components title page, a divider, then all other pages in their current order | Phase 0 page list | Page list (pages are only moved, never deleted or renamed) |

State the placement plainly: which page each component's docs go on, that they replace the `Overview / <Component>` placeholders in the left column under the Documentation banner, that the Components column is not touched, and which token library pages will be created or reused. Mark anything that cannot be produced as **Blocked** with the reason. Ask once: "Build as proposed", "Remove some sections", custom. Wait for the answer before Phase 6.

---

## Phase 6 - Build

Only after Phases 0 through 5 are resolved. Load `figma-use` and `figma-generate-design` before the first `use_figma` call.

**Helper code.** If the build uses reusable helper code stored in the file, keep it on a temporary page (for example `__docs_tmp__`), never on the designer's pages, and delete that page at the end and say so. A single `use_figma` call returns at most about 20 KB; a call that returns more is rolled back, so return counts and ids, not content.

### Placement

1. **Component docs go on the component's own category page, in the left column under the Documentation banner.** Width equals the Documentation banner's width (read it; 960 in the reference file) and x equals the banner's x. Name each frame `Docs/<Component>`. The Components column and the component sets are never moved.
2. **Replace the placeholder.** After the Phase 5 yes, build the new frame first, verify it, then delete that component's `Overview / <Component>` placeholder. If a build fails, the placeholder stays. A page may hold an extra overview for a related frame (for example a specimen): ask before deleting anything that is not the component's own placeholder.
3. **Stack the column.** Docs are much taller than placeholders. Stack them top-down from the first placeholder's y, in the same order as the component sets, with a gap bound to the largest `space/*` token. Never overlap any node.
4. **Token library pages.** Reuse pages found in Phase 0. Create the rest with a **title banner that matches the file's existing banners** (clone an existing Title banner: same fill token, radius, padding, type style, and a width that fits the page content; set the text to the page title). Place cards under the banner with the same left margin the other pages use.

### Look and feel

Readable, specimen-like, token-bound. These are the designer's stated preferences:

- Large readable type: body text at the 16px size token, notes at 14px, section headings at the 24px display size. Avoid 12px and smaller except code. Generous spacing from the `space/*` tokens.
- A frame width of the banner (960), with padding and gaps from tokens. Fills use `surface/*`, strokes `border/*`, text `text/*`, within their scopes. Cards and bands are rounded with a `radius/*` token.
- No internal bookkeeping on the canvas: no Found or Authored labels, no node-id dumps beyond one pointer line in the header.
- Never put an instance on a background it blends into (see "Stage contrast").

### Sections, in order

1. **Preload fonts per styled text segment** (`getStyledTextSegments(['fontName'])`), plus the monospace font, before creating any text.
2. **Header:** component name, one-line purpose, and a single pointer line (component set name and node id, variant count, and the code stack with the note that a default stack is not confirmed with engineering).
3. **Overview:** the default instance, live, centered on a stage (see "Stage contrast").
4. **Anatomy** (modeled on the designer's reference): a stage card on the left holding a live instance, and a legend on the right.
   - Turn on every optional boolean property on **the display instance only** (never on the component) so every part is visible, including parts hidden in the default variant.
   - Number the parts in reading order (cap at 10; deeper parts go in the mapping table). Each number is a dark circle (`surface/dark` fill, `text/inverse` number) placed **outside** the instance in the stage margin and joined to its part by a **straight leader line** that ends on the part's edge with a small end dot. Choose the side by shape: markers above and below for wide components, left and right for tall ones; spread them so no two markers or lines overlap or cross a label.
   - Verify the geometry: every leader's end point lies on its part's bounds, and every marker sits outside the instance.
   - Legend rows: the same numbered circle, the part name in bold, a second line "type, required or optional, controlled by <property>", and a third line with at most three key properties and their tokens (for example "Fill cta/primary-fill, Radius radius/sm, Padding space/md space/xl"). Nested instances name their main component.
   - Find parts in the display instance by name and type, not by child index: hidden parts are not returned among an instance's children, so indexes shift.
   - A component wider than the stage is scaled to fit (rescale the display instance) with a caption "Shown at N%". A component wider than about 560px gets a full-width stage with the legend underneath instead of beside it; give that row `HUG` height and turn clipping off, or the stage and legend are cut to a 100px strip.
5. **Properties:** a compact table: property, type, values, default, what it controls. It equals `componentPropertyDefinitions`, name by name.
6. **Variants** (modeled on the designer's specimen frame; organized by axis, never one flat grid):
   - **Sizing:** one card per Size (or Layout) value with a live instance and spec lines measured from the instance: heights (with and without optional parts), padding tokens, gap token, corner radius token, label text style (family, weight, size token, case), and nested icon sizes. A component with no size axis gets one Dimensions card.
   - **Content options:** the boolean, text, and instance-swap options as live instances with plain captions (for example "Arrow only (default)", "No arrow", "Leading icon and arrow", "Leading icon only"), up to four combinations.
   - **State matrix:** for each size that has states, a header row of state names (conventional order Default, Hover, Pressed, Focus, Disabled when those names exist, otherwise definition order), and one row per combination of the other variant axes (for example Style by Theme). Row labels sit in a narrow column on the frame background (wrap long labels; the label column hugs its height so a second line such as "on accent/terracotta" is never clipped); instances sit on a **band** behind them. Every band has a 1px `border/default` stroke, so a band whose fill matches the frame is still visible. Size each cell to hug its instance and turn clipping off, because a fixed-height cell crops outlines and shadows. Rows for a theme designed for colored backgrounds sit on the real host color read from Phase 1f (named in the row label, for example "on colored background"). Sizes that are only built for some combinations get their own smaller matrix with a heading that says so.
   - Sets with no State axis: show **every** variant, grouped under one heading per value of the first axis (for example "Size = Sm", then "Size = Md"), each variant captioned with its other axis values; a single-axis set uses one "By <axis>" grid. Never show only the variants whose other axes are at their defaults, because that hides the cross combinations (for example Md with Green). List combinations that do not exist in one "Not built" line; where the file pairs axis values by design (a Theme that only exists for one Layout), say that instead of implying something is missing.
   - Each variant appears exactly once, as a live instance. Missing combinations are listed in one plain line ("Not built: ..."), not as empty cells.
7. **Usage:** when to use, when not to use, choosing a variant.
8. **Rules:** grouped as the designer approved.
9. **Content guidelines.**
10. **Behavior, semantics and keyboard.**
11. **Accessibility:** a compact computed contrast table (layer, pair, text size, ratio, required, result), target size, focus indicator, and "not computed" rows with reasons. Failing pairs also go to Known issues.
12. **Untokenized values:** a short list of the literals from 1e.
13. **Engineering handoff:** the token-to-property mapping table. **The code reference goes at the bottom of the frame**, labeled with the chosen stack (marked as a default when not confirmed with engineering) and the statement that it is a reference implementation, not tested production code. Each layer is its own dark block labeled with its filename and language: `tokens.css` (only the tokens the component uses), the component file, the stylesheet, and `Usage`. Split long code across text nodes of at most about 4000 characters.
14. **Related components, known issues, changelog stub** (a dated "Documented" entry).

**No per-component "tokens used" table.** Full token detail is in the token library.

### Stage contrast

A stage, band, or panel must make the instance visible. For each placed variant, compute the contrast between the instance's outermost visible fill or stroke and the stage fill. If it is below 1.3:1 (the instance would blend in), choose the next in-scope surface token that passes, and where a theme is built for a colored background, use that host. Never place an instance on a background it blends into, and never leave outline or transparent variants floating on a color that matches the frame. Report any case where no in-scope token works.

### Token library pages

Build the three artifacts from 3b on their own pages, each under its title banner. Swatches are real fills bound to the token they show (set the stored paint color to the token's resolved value). The Typography page applies the real text styles. Write the Markdown for the tokens (`tokens.md`, `tokens.css`) to the session's output folder.

### Page organization

Run this after the token library pages exist. It changes only the order of pages and adds empty title and divider pages. It never renames, deletes, or edits an existing page.

1. **Target order, top to bottom:**
   1. a title page named `🎨 Brand Guidelines`
   2. the token library pages in alphabetical order (Colors, Tokens, Typography)
   3. a divider page named with a row of dashes (`------------------------`)
   4. a title page named `⚙️ Components`
   5. a divider page (the same dashes)
   6. every other page, in the order it already has
2. **Title and divider pages are empty pages.** They exist only to label and separate groups in the page list. Create only the ones that do not already exist, and match the file's own convention if it has one (Phase 0).
3. **Alphabetical means by plain name** (ignore emoji); if the file already has other brand-guideline pages (for example Illustration or Icons), include them in the top group in alphabetical order, and say so in the proposal.
4. **Move pages with `figma.root.insertChild(index, page)`** and then read the order back from `figma.root.children`. Do not rely on creation order.
5. **Report the final page list** in the summary, top to bottom.

### Markdown files

For each component, write `<component>.md` and, for the token library, `tokens.md` and `tokens.css`, mirroring the frame content, with code in fenced blocks and tables as Markdown tables. A single `use_figma` call cannot return a large page, so assemble the Markdown from compact data returned in several small calls, or generate it in the sandbox from that data. Present the files when done, and say plainly which were not written.

### Docs furniture is bound too

Page backgrounds, dividers, labels, table cells, markers, leader lines, swatches, and code blocks use existing tokens within their scopes (`surface/*` for fills, `border/*` for strokes, `text/*` for text). If no token fits a piece of furniture, do not hardcode it and do not create a token: report the gap and ask how to proceed ("Use the closest in-scope token", "Leave it unbound and flag it", custom).

### Text, retries, limits

- **Wrap text in labeled frames.** Set `textAutoResize = 'HEIGHT'` with a width equal to the frame minus its padding so long captions and code lines do not overflow.
- **If any step fails**, report it. Never silently skip a section, a table, or a binding. If a retry is needed, make sure every cleanup and teardown step ships in the retry so partial frames are not left behind, and that a retry removes the partial frame it created before rebuilding.
- Do not modify the component sets, their variants, or any node other than the placeholders you were told to replace and the frames you create. Do not create tokens.
- A large operation may be stopped by the environment's permission check as modifying shared resources. If so, ask for explicit confirmation and retry the **same** operation. Do not retry a smaller, quieter version.

---

## Phase 7 - Verify and Report

Walk the docs frames and the component sets by direct traversal and assert:

- Every fill, stroke, radius, spacing, and font size on the docs furniture is bound to a Variable within its scopes, or is listed as an acknowledged gap. Live instances are excluded.
- No frame clips its content: each Anatomy row is at least as tall as its stage and its legend.
- Every docs frame is on its component's category page, in the left column, with the Documentation banner's width, and overlaps no other node. Each replaced placeholder was deleted only after its replacement was verified.
- Every variant instance resolves to a real variant of the set (no detached copies), each variant appears exactly once, and the count equals the set's variant count minus the listed missing combinations.
- No instance blends into its stage: recompute the stage contrast for every placed variant.
- Anatomy: every leader line's end lies on its part's bounds, every marker is outside the instance, no two markers overlap, and the legend numbers equal the marker numbers.
- The properties table equals `componentPropertyDefinitions`, name by name, with defaults.
- Every contrast ratio in the docs was recomputed from resolved values and matches the report.
- If code was generated, its variant and state styles match the variant list, and no literal color or pixel value appears for a property that is bound in Figma. If it was not, the token-to-property mapping covers every bound property of every variant.
- The token library pages exist under title banners, every variable in scope appears once, and swatches are bound to the tokens they show; the Typography page uses the real text styles.
- The page list reads, top to bottom: the Brand Guidelines title page, the token library pages in alphabetical order, a divider, the Components title page, a divider, then every other page in its original relative order; no page was renamed, deleted, or edited, and the title and divider pages are empty.
- Every text node's fonts are loaded and match what was requested.
- The component sets, their variants, and the original file content are unchanged (compare variant counts and node ids before and after), and any temporary page was deleted.

Report as a table:

| Section | Status | Notes |
|---|---|---|
| Anatomy | Created | 7 parts, 2 optional, leaders verified |
| Variants | Created | 18 of 18, 3 combinations not built |
| Accessibility | Created | 2 pairs not computed: gradient fill |
| Engineering handoff | Created | Mapping and code reference; compile check: run / not run |
| Tokens, Colors, Typography pages | Created | 3 zero-usage tokens flagged |

Share screenshots of each docs frame. Call out anything that could not be written, every flagged rule violation left as a follow-up, and every untokenized value. Then offer next steps as multiple choice: "Write token descriptions back to Variables", "Document the next component", "Set up Code Connect mappings for this component (uses `figma-code-connect`)", custom.

---

## Hard Rules - Non-Negotiable

- **Never create or edit tokens, components, or variants in this skill.** Missing pieces are reported and routed. Variable descriptions are written only after a separate, explicit yes (Phase 3e).
- **Never publish a claim the file does not support.** Every statement is Evidence, Convention, or designer-confirmed. Proposed content is confirmed before it appears.
- **Never publish a rule the file contradicts without telling the designer.** Test checkable rules and report violations first.
- **Never hardcode a value in code or in the docs furniture.** Bound properties reference the theme in code and Variables on the canvas. Untokenized literals are flagged as such.
- **Never estimate contrast or target size by eye.** Compute from resolved values or report "not computed".
- **Never infer the HTML element, keyboard behavior, or transitions from appearance.** Ask.
- **Never use screenshots where a live instance works.** Variant grids and anatomy use instances so the docs track the component.
- **Never present generated code as compiled or tested unless it was.**
- **Never generate code for a stack the designer did not choose, and never present a default stack as confirmed.** If they were unsure, use React with TypeScript and say so in the docs.
- **Never output unescaped dynamic values in PHP.**
- **Never bind a token outside its scopes**, including on docs furniture.
- **Never replace or delete an existing docs frame without an explicit yes.**
- **Never run Phase 1 with a design-context/code-generation tool as a substitute for direct node traversal.**
- **Never assume scope, stack, or naming.** Ask in Phase 0.
- **Never ask an open-ended question when options can be offered.** Every question is multiple choice, with the custom option last and at most 3 real choices.
- **Never answer "what are you referring to?" by rewording.** Show the exact element, then repeat the same question.
- **Never act on a question before it is answered**, and never treat a custom answer as confirmed until it is restated.
- **Never silently skip a failed step.** Report every section, binding, or file that could not be written.
- **Never place an instance on a background it blends into.** Compute the stage contrast and use a contrasting in-scope token or the real host.
- **Never print internal origin labels** (Found, Authored) in the docs, code comments, or Markdown.
- **Never build component docs outside the component's category page,** and never outside the left column under its Documentation banner, unless the page has no banners and the designer chose another place.
- **Never delete a placeholder before its replacement is built and verified,** and never delete a frame that is not the component's own placeholder without asking.
- **Never rename, delete, or edit an existing page when organizing the page list.** Only move pages, and add empty title and divider pages that do not already exist.
- **Never leave helper code or temporary pages in the designer's file.**
- **Never add a per-component tokens-used table.** Token detail lives in the token library pages.
- **Never "correct" token names in the docs.** Token names are shown exactly as they exist in the file. Names that are not lowercase are flagged in Known issues, not silently lowercased.

---

## Success Criteria

- Scope, outputs, destination, and whether code is needed were asked upfront, not inferred.
- The prerequisite check found zero unbound values in the documented components, or the designer explicitly chose to document them as untokenized.
- Every docs section is traceable to Evidence, Convention, or a designer-confirmed draft.
- Every checkable rule was tested against the file, and its violations were reported before publication.
- The variant matrix is built from live instances and matches the component set.
- The properties table matches the component's property definitions exactly.
- Every contrast figure was computed from resolved token values.
- Whether to generate code was asked, and code was produced only where the designer said engineering needs it. Where produced, it follows the three-layer format in the chosen stack (or in React with TypeScript, labeled as a default), references tokens for every bound property, flags untokenized literals, covers every variant and state (or lists what is missing), and was compile-checked or explicitly disclosed as not. Where not produced, the token-to-property mapping was delivered instead.
- The Tokens, Colors, and Typography pages exist under title banners, list every primitive and semantic variable and text style in scope with value and usage, use real bound fills and real text styles, and their flags were surfaced.
- Component docs sit on their category page under the Documentation banner, replaced their placeholders, stack without overlap, and show anatomy with leader lines that reach their parts, variants organized by size, type, and state, and no instance that blends into its background.
- The page list is organized into a Brand Guidelines group (title page and token library pages in alphabetical order), a divider, a Components title page, a divider, and all other pages below, with the final order reported.
- The docs furniture is bound to in-scope tokens, with gaps reported.
- The component set and tokens were unchanged by the run.
- Every question named its subject precisely, was multiple choice with a custom last option, and used no screenshots unless the designer asked what was being referred to.
- The skill ran correctly on a file with no brand-specific names, pages, or token names baked in.

---

## Reference Notes - Carried Forward From Tokenization and Componentization

These come from the earlier skills in the chain. Treat them as things worth checking for, not defaults to assume. This skill was revised after its first real run; the lessons from that run are at the end of this list.

- **Fallback fonts hide at segment level.** Preload fonts from `getStyledTextSegments(['fontName'])` for every text node, not only node-level `fontName`, or binding and editing can throw `unloaded font` errors. This also applies to the monospace font for code blocks.
- **Large operations may be blocked the first time.** Confirm explicitly and retry the same operation.
- **Retries must ship every step,** or duplicate docs frames and orphans pile up.
- **Token roles may be named by tone, not role** (for example `surface/cream`). Describe tokens by what the file shows, not by an idealized role.
- **Leftover unbound tokens are common** after tokenization (new levels exist but are not yet bound). Expect zero-usage tokens in the token library and report them neutrally.
- **Trust-style families may be semantic-only.** A role can exist as semantic tokens aliasing a general hue family with no standalone primitive. Detect this from the data rather than assuming every role has its own primitive.
- **Semantic splitting should earn its place.** Where one token serves two purposes with no reason to diverge, document the single token with both uses rather than flagging a missing second token.
- **Opacity variables use a 0-100 scale.** Convert to a 0-1 value when emitting code, and say so.
- **Stored paint colors matter.** A bound paint built from a placeholder may render the placeholder; set each bound paint's stored color to the token's resolved value for swatches.
- **Zero-size shapes are not zero.** Figma stores a zero-width bar as about 0.01px. Compare against a real threshold (> 1) when reading state from geometry.
- **Layout reads can be stale right after an edit,** especially inside auto-layout parents. Re-read after a brief wait and cross-check with a stable relationship or a screenshot before reporting a difference.
- **Hidden parts of an instance may not be returned by `findOne`.** Treat "not found" as "hidden" only where the design needs it hidden; otherwise it is a failure.
- **Assigning `componentPropertyReferences` replaces the whole object.** Not used in this skill, since it does not edit components, but read references carefully when documenting which properties control which parts.
- **An instance-swap property has one default for the whole set.** When documenting nested parts that differ per variant, say whether the part is swappable at the set level or by swapping the nested instance directly.
- **Find nested parts by what they are, not by their layer name.** The same part can carry different names on different pages. Identify by main component.
- **Plain-text look-alikes are easy to miss.** A part that is a component in one place may be a bare text layer elsewhere. List non-matching look-alikes under known issues instead of dropping them.
- **Cloning a variant does not add a variant** (not used here; the docs frames hold instances only, never new variants).
- **The code format comes from a reference example the team's engineer approved.** It has three layers: a CSS custom property token file, a React component with CSS Modules, and a usage example. Do not copy its shortcuts: the example hardcodes a disabled text color and a font size, adds a transition, applies inline styles with token values in its usage file, and replaces the focus outline with a border color change. This skill flags untokenized literals, adds no invented behavior, and requires a replacement focus indicator to pass the contrast check.
- **The stack is a choice the designer often can't make.** React is a library, TypeScript and JavaScript are languages, and WordPress is built on PHP, so the stack question is phrased in plain words and has a default. Code Connect mappings, where they exist, are better evidence than asking.
- **A WordPress block theme turns `theme.json` into CSS custom properties** (`--wp--preset--<category>--<slug>` for presets such as color, spacing, and font size, and `--wp--custom--...` for `settings.custom`), so its tokens layer is `theme.json`, not a hand-written `tokens.css`. A classic theme uses a stylesheet with PHP template parts. Which one a project uses is a developer decision, so ask.
- **Lessons from the first real run (the Fieldnote file).**
  - A first design that put everything on one 1584px page, listed a "tokens used" table per component, labeled variants Found or Authored, and numbered markers without leader lines read poorly and was rejected. The current spec comes from the designer's feedback and reference screens (a specimen frame, an anatomy example with leader lines, token tables, color swatch cards, typography specimens).
  - **Hidden parts shift child indexes.** An instance's children omit hidden parts, so matching a part by its index in the component picks the wrong layer. Match by name and type.
  - **`boundVariables.fontSize` can be an array.** Handle both an alias and an array of aliases. Ellipses and other shapes have no per-side stroke weights: read `strokeWeight` for them.
  - **Contrast against the set's own canvas is meaningless for variants with no fill of their own** (for example Inverse variants built for colored backgrounds). Mark them host-dependent and compute against the real hosts found in usage.
  - **Do not size the docs to the content.** A frame sized for the widest component (1440px) breaks the Documentation column. Fix the width to the banner and scale wide display instances.
  - **A helper-library text node can persist code between `use_figma` calls** (store the function source, rebuild it with the `AsyncFunction` constructor). Keep it on a temporary page, and re-ship the patch with every retry; a failed call is not committed.
  - **Placement shifted as the file evolved.** `componentize` now creates category pages with banners; read the page structure in Phase 0 instead of assuming one page.
  - **Fixed-height cells crop instances.** A matrix cell resized to a fixed height clips the top and bottom border of an outline button. Let cells hug their instance and set `clipsContent = false`.
  - **A leader line is a vector with its own bounding box.** Set the path relative to the line's top-left corner and position the vector there; positioning it at the stage origin draws a stray line at the stage edge.
  - **`createAutoLayout` puts the new frame on the current page.** Its `parent` is the page, not null, so adding it to a section with a "fill" sizing fails unless it is appended to the section first.
  - **A category page wraps each set.** Look up the component set by name and type on the page; the wrapper frame named like "Button: component set" is not the set and has no property definitions.
  - **A first matrix pass counts nested instances.** Count only instances whose main component belongs to the documented set, or nested icons look like duplicates.
  - **Run the build in two steps.** Install helper code in one call and build in another, so a failed build never loses the installed code; a call that errors is not committed.
  - **Wide components need a different anatomy layout.** Switching the Anatomy row from horizontal to vertical keeps a fixed 100px height unless it is set to hug; the stage and legend were clipped on Navigation, Footer, Section heading, Comparison row and Plan card until the row was set to `HUG` with clipping off. Verification now checks row height against stage and legend height.
  - **Showing only default-axis variants hides combinations.** A "by axis" grid left out Md with Green, Plus with Narrow and others; verification caught it because it checks that every variant of the set appears.
  - **Stored helper code is reformatted.** A function's source read back from a text node has its statement spacing normalized, so patches must use whitespace-tolerant patterns, not exact strings, and a patch that cannot find its anchor must stop the run.
  - **Verification fails on staging artifacts.** Frames built at the bottom of a page overlap each other until they are restacked; restack first, then verify overlaps.
  - **Size each page's column to its widest component.** The Navigation page needed a 1632px Documentation banner; widen the Title and Documentation banners and shift the Components column by the same amount so the gap between columns stays the same.
  - **Page order is an API call, not a creation order.** New pages append to the end. Reorder with `figma.root.insertChild(index, page)` and read `figma.root.children` back to confirm. Title and divider pages are plain empty pages named with an emoji and a title, or a row of dashes.

