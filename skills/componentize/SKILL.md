---
name: "componentize"
description: "Componentize a tokenized Figma element into a variant-complete, token-bound component set. Use for 'componentize this', 'make this a component set', or 'build variants from my selection'."
---

# Componentize Selection (variant discovery + token-bound build)

> Turns an already-tokenized element in a Figma file into a proper, variant-complete component set bound to the file's existing Variables, with variants derived from real evidence in the file plus standard interaction states authored from existing tokens. Second skill in the chain Tokenize Selection -> Componentize Selection -> Document Component.

> Designers draw an element once, then draw it again with small differences:
> a second size, a different emphasis, a pressed look. This skill is the
> formalization step after tokenization. It reads what the designer actually
> drew, works out which differences are real variants and which are drift,
> proposes a component API, and only then builds a component set bound to
> the file's existing Variables. It never decides silently, and it never
> hardcodes a value.

---

## Quick Start

**What to say:** "Componentize this" / "Turn this button into a component" / "Build a component set from my selection". You don't need an exact phrase. The skill asks what scope you mean before doing anything.

**How it asks:** Every question comes as tappable multiple-choice options with a custom-answer choice always available, so you rarely have to type. See "How This Skill Asks Questions".

**What you need:** A selected element whose fills, strokes, radius, spacing, and type are already bound to Figma Variables (the output of `tokenize-selection-v2`).

**What you get, in order:**
1. An upfront question on which element to componentize and where to look for look-alikes (Phase 0)
2. A prerequisite check: tokens exist, the element is bound, and any existing component convention is surfaced (Phase 0)
3. An anatomy read of the element and a direct-traversal search for look-alikes (Phase 1)
4. A variant discovery report: axes derived from real evidence, near-duplicates and one-offs flagged for your decision (Phase 2)
5. A confirmed component API: which axes become variant, boolean, text, or instance-swap properties, plus naming (Phase 3)
6. A proposal table of every variant, each marked **Found** (drawn in the file) or **Authored** (standard state built from existing tokens), with the token bound to each property (Phase 4)
7. The component set, built and bound, placed on a page for its component type; a new page is created if none exists (Phase 5, see "Component Pages")
8. A verification report: zero hardcoded values, screenshots, and an optional swap of originals for instances (Phase 6)
9. A component page with a main title banner and **Documentation** and **Components** subheaders, with an overview card beside each component (see "Component Pages")

---

## What This Skill Does NOT Do

- **Does not create tokens as part of the build.** If a component needs a value no Variable provides, stop and say so. Creating tokens is `tokenize-selection-v2`'s job for colors, type, spacing, and radius. For property types that skill doesn't cover (stroke weight, effects, opacity), the designer decides whether to pause for a separate token step; the build never proceeds with a hardcoded value in its place unless the designer explicitly chooses "leave unbound and flag it".
- **Does not write full documentation.** It generates a short overview card per component from data read out of the file (properties, variants, tokens, usage counts). Anatomy, usage guidance, and rules are `Document Component`'s job (not yet built).
- **Does not rename, delete, reorder, or edit existing pages.** It only creates new component pages and places components on them.
- **Does not componentize untokenized work.** If the element's fills, strokes, type, or spacing are mostly raw values, stop and route the designer to `tokenize-selection-v2`.
- **Does not audit drift across a whole file.** That is separate audit work.

---

## How This Skill Asks Questions

**Every question to the designer is multiple choice, and every multiple-choice question always includes a custom-answer option.** The designer should almost never have to type an answer from scratch, and should never be boxed in by the options offered.

Rules:

- **Use the structured-choice tool** (`ask_user_input_v0`) whenever it is available. If it isn't, write the options as a lettered list in chat and tell the designer they can reply with just a letter.
- **Always include a custom-answer option, and always make it the last one.** Label it plainly, for example "Something else (I'll type it)". It is never omitted, even when the other options look exhaustive. If the designer picks it, wait for their typed answer, then restate it back to confirm before acting on it.
- **Option limit.** The tool allows 2-4 options per question, so offer **at most 3 real choices plus the custom option**. If a decision has more than 3 real choices, either group them into 3 sensible buckets and refine in a follow-up, or split the decision across questions. Never silently drop a real choice to fit.
- **Put the recommended option first** and mark it "(recommended)" when there is a clearly better default. Offer a recommendation only when there is a real reason for it.
- **One decision per question, and at most 3 questions per round.** Don't bundle unrelated decisions into one question.
- **Questions about specific elements** (near-duplicate differences, look-alike groups): the choice buttons can't carry images, so the question text must identify the thing precisely by its cluster ID, layer name, page, and node ids. Do not attach a screenshot by default. Show one only when the designer asks (see below).
- **Batch approvals are still multiple choice**: "Approve all", "Review individually", plus the custom option for "approve all except these".
- **Name what you are asking about precisely; don't show it by default.** Identify every element in the question text exactly as it appears in the file (layer name, page, how many instances, and node ids where useful). Do not attach screenshots or annotations to questions unprompted: the designer has said constant visuals are not needed. Show the element only when they ask (next rule).
- **When the designer asks "what are you referring to?" (or "what do you mean?", "which one?", "not sure what you're asking"), do not reword or simplify the question.** That is the signal to show the element. The designer is telling you they cannot see which component or design you mean. Show it: take a screenshot of the exact element (number or annotate each one when several are involved), or add native Figma annotations to the exact nodes, and give the layer name, page, and node ids. Then ask the **same question again, unchanged**. Plain-language rewording and analogies hide the thing they asked about, and are not a substitute for showing it.
- **Temporary markers are removed.** Any annotation, outline, or callout added only to answer a question is cleaned up once the question is answered, unless the designer asks to keep it, and it never changes the design itself.
- **Stop after asking.** After presenting a question, end the turn and wait for the answer. Don't continue writing or acting as though it were answered.
- **Never turn a statement of fact into a question.** If the answer is determinable by reading the file, read the file. Ask only about real decisions or genuine ambiguity.

---

## Phase 0 - Confirm Scope, Prerequisites, and Convention

### Step 1 - Ask scope before anything else

Always ask, regardless of how the skill was invoked. Use two multiple-choice questions:

1. **Which element?** Offer the current selection (named by layer name and node id) as the first option, up to two other plausible candidates found on the page, and the custom option. If nothing is selected, say so and offer the most repeated structures found on the page as options.
2. **Where should I look for look-alikes?** Options: "Just my selection", "This page" (recommended when the selection is on a single page), "Whole file", plus the custom option.

Wait for the answers. If nothing is selected, ask the designer to select the element and re-check. Confirm the look-alike search scope back explicitly ("searching all N pages") when it is broader than the selection.

### Step 2 - Prerequisite check (read-only)

Walk the element directly and confirm:

- **Tokens exist.** The file has Variable collections. If none, stop and route to `tokenize-selection-v2`.
- **The element is bound.** List every fill, stroke color, stroke weight, corner radius, spacing value, font size, and **effect** (shadow color, blur, offset, spread) on the element that is **not** bound to a Variable. Check effects and stroke weight explicitly: they are easy to miss, and a scan limited to fills, radius, spacing, and type will wrongly report "fully bound". Scan **every descendant** of every match (icons, vectors, ellipses, nested frames), not only the top-level frame and its text children: a raw stroke on a small icon is easy to miss and has been missed. Also note whether the file has any Variables for stroke weight or effects at all. If it has none, those properties cannot be bound by this skill, and the designer must decide in Phase 4 how to handle them. Report the count and the nodes.
  - A few leftovers: ask whether to bind them first (to an existing token) or exclude them from the build. Never carry a raw value into a component.
  - Mostly unbound: stop and route to `tokenize-selection-v2`.
- **Existing convention.** Check existing components, component sets, and Styles for naming and grouping patterns. Surface what you find and ask whether to match it. If nothing exists, say you'll default to `Category/Name` for the set and `Property=Value` for variants, and give the designer the chance to change that.

### Step 3 - Surface the token layering found

State which collections exist (for example a primitive layer and a semantic layer). Binding rules in Phase 5 depend on this. Never assume a semantic layer exists.

### Step 4 - Find or choose the component page

List the file's existing pages by name. Do not edit them. Open a page only to read what is on it (for example to record child counts or find existing frames), and visit every page before recording counts (see Reference Notes). Decide which category the element belongs to using the table in "Component Pages", then check whether a page for that category already exists, matching by normalized name and synonyms (`Button`, `Buttons`, and `Actions` are the same page).

Ask one multiple-choice question per category that needs a decision, naming the page precisely:

- **A page exists:** "Place Button on the existing page `Buttons`" (recommended) / "Create a new page instead" / custom.
- **No page exists:** "Create a new page named `Navigation`" (recommended; it will be placed in alphabetical order among the component pages) / "Use the existing page `<closest match>`" / custom.
- **No category fits:** offer the two closest categories, plus the custom option for a new page name.

Do nothing to the file yet; the page is created in Phase 5 only after this answer.

---

## Phase 1 - Read the Element and Find Look-Alikes

Read-only. Use **direct node traversal** (`findAll` / `findAllWithCriteria`, reading real node properties). Never use a design-context or code-generation tool as a substitute: such tools sample and summarize and will miss look-alikes.

### 1a - Anatomy of the source element
For each layer: type, name, auto-layout direction, padding, item spacing, sizing mode (hug/fill/fixed), constraints, corner radius, fills, strokes, effects, and for text nodes the font family, style, size, and line height read per styled segment (see Reference Notes). Record the bound Variable for each property, or flag it as unbound.

### 1b - Look-alike search
Within the confirmed scope, search two ways and merge the results:

- **By structure.** Nodes that share the source's structural fingerprint: same auto-layout direction, similar size range, same kinds of children. Treat an optional child (icon, arrow, badge) as optional, not as a different structure, or you will split one component into several false families.
- **By name.** Nodes whose names match or resemble the source's (for example `Button`, `Primary action`, `Start button`). Structure alone misses look-alikes with an extra or missing child.

Show the designer any node that matched only one way, and ask in Phase 0/2 whether it belongs. Then, for each match, record: page, top-level frame, node id, and which properties differ from the source (size, fills, strokes, radius, spacing, text style, child presence, text content). Also record two pieces of context for every match:

- **Background context.** The nearest ancestor fill (token name if bound). A difference that tracks the background (light fill on a colored section, dark fill on a light one) is evidence for a theme or surface axis, not for separate styles.
- **Resolved token values.** When two matches use different tokens for what looks like the same role, resolve both through their alias chains. **Same resolved value, different token** means token-role drift to normalize, not a visual variant. **Different resolved value** means a real variant candidate.

Group matches by their set of differences. These groups are the raw material for Phase 2.

---

## Phase 2 - Discover Variants From Evidence

**Core guarantee: no variant is invented from nothing, and no difference is classified silently.**

Each distinct, repeated combination of differences becomes a **candidate variant**. Assign each a cluster ID (`V1`, `V2`, ...). Then sort the differences into candidate axes:

| Evidence | Candidate axis |
|---|---|
| Differing height, padding, or font size | Size |
| Differing fill/stroke/text-color tokens | Emphasis / Type / Theme |
| Child layer present in some, absent in others | Boolean (e.g. Icon) |
| Differing text content only | Text property, not a variant |
| Differing icon child only | Instance-swap property, not a variant |

### Explain before triaging

Before counting how often anything appears, try to **explain every difference with an axis**. Work through the table above and the Phase 1 context (background, resolved values, optional children). A difference that is fully explained by an axis (a size, a theme, a boolean child, content-driven height) is **evidence for that axis**, never a candidate for drift, no matter how few nodes show it. Check whether the evidence fills a grid: if every combination of two candidate axes appears in the file, that is strong evidence for two axes rather than a single list of named styles.

Only the **unexplained residue** goes to triage. Frequency alone is not a reason to call something drift: in a small population (a dozen buttons) every cluster is "rarely used".

### Triage before asking

- **Rarely-used, unexplained differences** (the divergent form appears in 2 or fewer places, and no axis explains it): batch into a single approval list, presented once in chat, each item named by layer name and node id (screenshots only if the designer asks). They are almost always one-off drift. The designer explicitly approves the batch or pulls items out for individual review.
- **Repeated differences** (3 or more places): get their own question, one at a time.

For each flagged difference ask a direct multiple-choice question, never a silent merge or split. Name the affected nodes in the question (layer name, page, node ids); show them only if the designer asks:

> **V3 - ambiguous.** 6 nodes use radius `Radius/md`, 2 use `Radius/lg`, otherwise identical. How should I treat the difference?
> - Treat as drift and use `Radius/md` everywhere (recommended)
> - Treat as drift and use `Radius/lg` everywhere
> - Keep as a distinct variant (e.g. Shape)
> - Something else (I'll type it)

The rarely-used batch uses the same pattern: "Approve all as drift", "Review each individually", "Approve all except some (I'll type which)". Wait for answers before Phase 3. Keep each resolved decision in a running record, since Phase 4 depends on it.

### Interaction states

Decide whether the element is **interactive** from evidence: it carries CTA-role tokens, is named like a button/link/input/tab/toggle/chip, or contains interactive-looking children. If the evidence is mixed, ask. Non-interactive elements (cards, badges, dividers) get no interaction states.

For interactive elements, record which standard states are **already drawn** in the file (hover, pressed, focus, disabled) and which are **missing**. Missing states are authored in Phase 4/5 (see below).

---

## Phase 3 - Confirm the Component API

Propose and confirm with the designer, using multiple-choice questions (one decision per question, custom option always included):

1. **Variant properties.** Axes with a small, closed set of values (Size, Emphasis, State). Use `Property=Value` naming.
2. **Boolean properties.** Presence toggles (Leading icon, Trailing icon, Badge).
3. **Text properties.** One per user-editable string. Default text taken from the source.
4. **Instance-swap properties.** For icon slots, with a sensible default.
5. **Naming.** Set name, property names, and value names, following the Phase 0 convention. Propose names from the actual evidence, never from a fixed template, and get confirmation on each.
6. **Prune combinations.** Say explicitly if some combinations are impossible or meaningless (e.g. Disabled + Focus) and should be omitted rather than built.

For naming, offer the proposed name plus one alternative plus the custom option. For each pruning decision, offer "Omit the combination", "Build it anyway", plus the custom option. Do not proceed until the designer confirms the API.

---

## Phase 4 - Propose the Build

Output a proposal table before writing anything.

| Variant (`Size=Md, Emphasis=Primary, State=Hover`) | Origin | Properties changed from base | Token bound |
|---|---|---|---|
| `...State=Default` | Found (node 58:12) | none | `cta/fill`, `text/on-cta` |
| `...State=Hover` | Authored | fill | `cta/fill-hover` (existing) |
| `...State=Disabled` | Authored | fill, text | **Blocked:** no existing token for a disabled value |

### Authoring standard states

The designer has chosen to have missing standard states authored automatically, rather than asked about one by one. That is a mandate to build them, not permission to invent values. Authored states are always shown in this table, marked **Authored**, so the designer can veto before anything is built.

Authored states must obey these rules:

- **Derive from existing Variables only.** Prefer an existing semantic token for that state. If none exists, the next or previous step in the same primitive family is acceptable, but only with the primitive-confirmation step below.
- **Never hardcode.** If a state needs a value no Variable provides (a disabled opacity, a focus-ring color), mark the row **Blocked** and tell the designer which token is missing. Offer to route to `tokenize-selection-v2`; do not create the token here.
- **Keep geometry stable.** Authored states change color, stroke, effect, or opacity-through-token, not layout, so sibling variants stay aligned.
- **Match the drawn language.** If the file already drew one state for another variant (a hover for Primary), authored states for the other variants should follow that pattern.

### Binding rules (confirmed with the designer)

- **Semantic first.** Bind to a semantic token wherever one fits the property's role.
- **Primitive only with confirmation.** If no semantic token fits, name the primitive you'd bind and ask before using it. Batch these questions per variant set.
- **Respect token roles and scopes.** Fills use fill-scoped tokens (surfaces, CTA fills), strokes use stroke-scoped tokens (borders), text uses text-scoped tokens. A token's name and scopes say its role. Do not borrow a token from another role because its color happens to fit.
- **No duplicate semantic tokens.** Reuse one semantic token across purposes when the use cases have no reason to diverge. Only split a role into two names when the values might reasonably diverge later.

Ask each primitive-binding question as multiple choice: "Bind to `<primitive>`", "Leave this property unbound and report it", and the custom option last ("Use a different token (I'll type it)"). Then ask once for the whole table: "Build as proposed", "Remove some Authored states", plus the custom option. Wait for the answer before Phase 5.

---

## Phase 5 - Build

Only after Phase 0, Phase 2, Phase 3, and Phase 4 are all resolved.

Before the first `use_figma` call, load the `figma-use` skill (and `figma-generate-library` when building a full set) as those skills require.

1. **Preload fonts per styled text segment** (`getStyledTextSegments(['fontName'])`), not only the node-level `fontName`.
2. **Build the base component** from a duplicate of the source element. Never convert the original in place until swap is confirmed in Phase 6.
3. **Set auto-layout, sizing, constraints, and layer names** per the confirmed API. Name every layer meaningfully (no `Frame 123`).
4. **Create variants** by combining components into a component set. Apply each variant's property differences and bind every fill, stroke, radius, spacing, and font-size to its confirmed Variable.
5. **Define component properties** (boolean, text, instance-swap) and connect them to the layers they control.
6. **Write the description** on the component set and on each variant: what it is for, and for Authored variants, state plainly that it was authored from existing tokens.
7. **Arrange the set** in a clean labeled frame (title, headers, row labels, context bands), and place it on the component page confirmed in Phase 0, following "Component Pages". Create the page first if it does not exist, and add the overview card beside the frame. Never leave component frames scattered on a design page.
8. **If any step fails**, report it. Never silently skip a variant, a property, or a binding.

Do not touch nodes outside the confirmed scope. Do not create tokens.

The confirmed scope includes the component page, its title and subheader banners, and the overview cards confirmed in Phase 0 Step 4 (see "Component Pages"). Creating those is part of the build, and nothing else on existing pages is touched.

---

## Phase 6 - Verify and Offer the Swap

### Verification

Walk every node of every variant by direct traversal and assert:

- No fill, stroke, corner radius, padding, item spacing, or font size is an unbound raw value.
- **Every binding is inside its token's scopes**: fills against fill scopes, text fills against text scopes, strokes against stroke scopes, effect colors against effect scopes, stroke weights and opacity against their float scopes. Report any mismatch as a violation, even if it renders correctly. This also applies to the layout furniture you add around the set (dividers, bands, labels).
- Every text node's fonts are loaded and match the source.
- Every variant in the proposal table exists, or is listed as blocked with a reason.

Output a final table:

| Variant | Origin | Bindings | Status |
|---|---|---|---|
| `Size=Md, Emphasis=Primary, State=Default` | Found | 6/6 bound | Created |
| `Size=Md, Emphasis=Primary, State=Disabled` | Authored | - | Blocked: missing token |

Share a screenshot of the component set. Call out anything that could not be written.

### Offer to swap originals for instances

After building, **offer** to replace the original look-alike elements with instances, and wait for an explicit, unambiguous yes, asked as multiple choice ("Swap all N", "Swap only the confirmed ones", "Don't swap", plus the custom option). State the exact count and location ("I'd replace 14 elements across 3 frames on this page"). The swap preserves each element's position, size, text content, and the properties it matches. Anything that doesn't map cleanly (an unmapped difference, an unconfirmed one-off) is excluded and listed.

A large swap may be blocked by the environment's permission check as modifying shared resources. If so, ask for explicit confirmation and retry the **same** operation. Do not retry a smaller, quieter version of the same action. Never swap without that explicit yes.

---

## Component Pages

Every component lives on a page for its **type**, so the file has one place to find buttons, one for cards, and so on. The pages are created by this skill when they do not already exist. The designer's design frames are never edited. A component frame may be moved off a design page only when the designer asks (see "Moving components already in the file").

### Page types

| Page | What goes on it |
|---|---|
| **Buttons** | buttons and button-like actions, with their states |
| **Cards** | testimonial, plan or pricing, feature, product, and content cards |
| **Logos & Icons** | logos, brand marks, avatars, icons, illustration placeholders |
| **Navigation** | header and its links, footer and its link groups, tabs, breadcrumbs, pagination |
| **Forms & Inputs** | inputs, selects, checkboxes, radios, toggles, form rows |
| **Content & Layout** | eyebrows, section headings, list items, FAQs and accordions |
| **Data display** | tables and rows, badges and tags, stats |

The table is for reading; it is not the page order (new pages are placed alphabetically, see "Creating and finding pages"). For anything else, propose a new page name in Phase 0 Step 4 and let the designer choose. A small part used inside a bigger one lives with its own type (an arrow icon is on Logos & Icons even though Button nests it). Cross-page nested instances keep working.

### Creating and finding pages

- Check for an existing page first, by normalized name and synonyms. Reuse it. Never create a second page for the same type.
- Create a missing page with `figma.createPage()` and set its `name` to exactly the page type. Then put it in **alphabetical order** (case-insensitive) among the component pages:
  - the component pages are the pages whose names match a page type in the table, or one of its synonyms;
  - insert the new page immediately before the first component page whose name sorts after it (`figma.root.insertChild(index, page)`);
  - if no component page sorts after it, put it after the last component page; if there are no component pages yet, put it after all the file's other pages.
- Alphabetical order applies only to pages this skill creates. Never rename, delete, or reorder existing pages, and never edit their content. Inserting a new page shifts positions but leaves the order of every existing page unchanged. If the existing component pages are not already alphabetical, do not fix that: place the new page by the rule above and tell the designer.
- After creating pages, read the page order back and confirm each new page sits where the rule says. If `insertChild` is unavailable or fails, leave the page where it is, say so, and do not move other pages to make it fit.
- Work on a page by making it current (`await figma.setCurrentPageAsync(page)`) before creating or finding nodes on it.
- If a page already has the template below, add the new component as a new row beneath the last row. If it has other content, do not touch it; ask the designer where the component should go.

### Page template

Same layout on every page. The sizes are defaults the designer can change; the structure is not.

1. **Main title banner** at the top: a wide frame (full content width, about 150px tall) holding the page type as its title, large and bold, in the on-dark text token. Use a dark brand fill token the file already has (for example `accent/green` or `surface/dark`); if several fit, ask once.
2. **Two subheader banners** side by side beneath it: **Documentation** (left column, about 960px wide) and **Components** (right area, the rest of the width). Same fill and the **same corner radius token** as the title banner (all three banners share one radius), with smaller text.
3. **One row per component** beneath the subheaders, parts before wholes (for example Navigation link before Navigation). Left: that component's **overview card**. Right: the component's labeled frame from Phase 5. Rows are about 96px apart. If a frame is wider than the Components area, widen both subheader banners and the title banner so nothing sticks out.

Every banner fill and every text size and color is bound to a Variable, and every binding is within its token's scopes. Do not draw anything that needs a raw value.

### Overview card

A frame at the left of each row (about 960px wide, a card surface token, padding and gap bound to spacing tokens) holding:

- the **component name** and its description text;
- a **Properties** list: each property's name, its type, and its values or default, read from the component set's property definitions;
- a **Variants** line: the number of variants, and how many are Found (drawn in the file) and Authored (built from existing tokens), counted from the variant descriptions;
- the **Tokens used**, listed by Variable name, collected by traversing the component's real bindings;
- **Used on**: the number of instances on design pages (count them; do not estimate);
- **Open items**, if any: blocked or omitted states recorded in the description;
- a closing line: "Overview only. Full documentation: Document Component."

Every number and name comes from the file through the API, never from memory. Never write a count you did not just read.

### Moving components already in the file

Only when the designer asks. Moving a top-level frame to another page with `targetPage.appendChild(frame)` keeps its instances linked to it. Count the instances before and after the move and require them to match. Verify that the move worked in this environment on one frame before moving the rest, and say so if it did not.

### Verifying a page

Screenshot the page and check: the three banners are present and bound; every component has its overview card beside it; no overlaps; no existing page was changed (compare page names and child counts before and after, visiting every page first); the pages this skill created sit in alphabetical order among the component pages and no existing page moved relative to the others; instance counts are unchanged.

---

## Hard Rules - Non-Negotiable

- **Never hardcode a value.** Every fill, stroke, radius, spacing, and font size in the component is bound to a Variable.
- **Never create tokens in this skill.** Missing tokens are reported and routed to `tokenize-selection-v2`.
- **Never invent a variant the file doesn't evidence, except standard interaction states.** Authored states are limited to standard interaction states on interactive elements, are always shown in the Phase 4 proposal marked Authored, and are built only from existing Variables.
- **Never silently classify a difference as drift or as a variant.** Every flagged difference gets a direct question or an explicit batch approval.
- **Never skip the batch-approval step for rarely-used differences.** Batching removes individual questions, not the designer's approval.
- **Never bind to a primitive without confirmation** when a semantic layer exists. If no semantic token fits, ask.
- **Never bind a token outside its scopes.** The API will bind any token to any property, so the scopes are your responsibility. A `border/*` token (stroke scope) is never a fill. A text-scoped token (`text/*`, `cta/*-text`) is never a stroke or a shape fill. Check `variable.scopes` against the property before binding. When a role token has the right value but the wrong scope, bind a token with the right scope and the same resolved value (a stroke token for a stroke), and tell the designer.
- **Never create a duplicate semantic token** for a use case with no reason to diverge.
- **Never run Phase 1 with a design-context/code-generation tool as a substitute for direct node traversal.**
- **Never convert or delete the original elements in place.** Build from a duplicate, and swap only after an explicit yes.
- **Never assume scope.** Ask element and look-alike scope explicitly in Phase 0, and never touch nodes outside the confirmed scope.
- **The confirmed scope includes the component page**, its banners, and its overview cards once the page is confirmed in Phase 0 Step 4. It never includes editing, renaming, deleting, or reordering existing pages, or editing design frames.
- **Never route the designer to a skill that can't do the job.** Before saying "route to `tokenize-selection-v2`", check that it covers the missing property type. It covers fill/stroke colors, type, spacing, and corner radius. It does **not** cover stroke weight, effects (shadows), or opacity, and it stops on scopes that are already tokenized. For those, say plainly that the chain has a gap, and offer to add the token in-session with the same confirm-before-write protocol (propose names and values grounded in what the file shows, get explicit approval, then write).
- **Never create a page for a type that already has one.** Match existing pages by normalized name and synonyms first.
- **Never rename, delete, reorder, or edit existing pages.** Only add new component pages.
- **Place every page this skill creates in alphabetical order among the component pages**, without moving any existing page to do it.
- **Never create a page before the designer confirms the page mapping** in Phase 0 Step 4.
- **Every component page uses the template**: a main title banner, a Documentation subheader, and a Components subheader, all bound to tokens and within scope.
- **Never put a count or name in an overview card that was not read from the file** in the same step.
- **Never write anything before Phases 0, 2, 3, and 4 are all resolved.**
- **Never operate on untokenized work.** Route to `tokenize-selection-v2`.
- **Never ask about an element without naming it precisely** (layer name, page, count, and node ids where useful). Never attach screenshots to questions by default; show the element when the designer asks what you are referring to.
- **Never answer "what are you referring to?" by simplifying or rewording.** Show the exact element, then repeat the same question.
- **Never ask an open-ended question when options can be offered.** Every question is multiple choice.
- **Never omit the custom-answer option**, and never place it anywhere but last.
- **Never exceed 3 real choices plus the custom option in one question.** Group or split instead of dropping a real choice.
- **Never act on a question before it is answered**, and never treat a custom answer as confirmed until it is restated back.
- **Never silently skip a failed step.** Report every variant, property, or binding that could not be written.

---

## Success Criteria

- Scope (element and look-alike range) was asked upfront, not inferred.
- Every property of every variant is bound to a Variable; the Phase 6 traversal found zero raw values and zero scope violations.
- Every variant in the build is traceable to either a drawn node (Found) or an existing-token derivation (Authored), and is labeled as such in the proposal and in its description.
- No Authored state contains a value that did not come from an existing Variable; any state that needed one was reported Blocked.
- Every near-duplicate or one-off difference was resolved by the designer in Phase 2 before being built or discarded.
- The component API (properties, names, pruned combinations) was confirmed by the designer before any build.
- Primitive bindings, where used, were individually confirmed.
- Every question about a specific element named it precisely (layer name, page, count, node ids). Screenshots were not attached by default; when the designer asked what a question referred to, the answer was the exact element shown (screenshot or native annotation) and the same question repeated, never a simplified paraphrase.
- Every question was multiple choice with a custom option last, and the designer was never required to type an answer unless they chose the custom option.
- The Phase 1 search used direct node traversal.
- The original elements were left untouched unless the designer explicitly approved a swap, and the swap report lists what was and wasn't replaced.
- The component is on a page for its type. If no such page existed, one was created after the designer confirmed it, placed in alphabetical order among the component pages, and no existing page was renamed, deleted, reordered, or edited.
- The page has the main title banner and the Documentation and Components subheaders, each component has an overview card beside its frame, and every number on those cards matches what the file reports.
- The skill ran correctly on a file with no brand-specific names, pages, or token names baked into it.

---

## Reference Notes - Lessons Carried From Tokenization

Treat these as things worth checking for, not defaults to assume:

- **Fallback fonts hide at segment level.** Figma may substitute a fallback font for glyphs the real font doesn't cover (a symbol or stamp character, for instance). The node-level `fontName` can miss this, and binding a variable then throws an `unloaded font` error. Preload fonts from `getStyledTextSegments(['fontName'])` for every text node before binding or editing text.
- **Large operations may be blocked the first time.** A single call touching thousands of nodes can be stopped by the environment's permission check as modifying shared resources. The fix is explicit, unambiguous designer confirmation, then retry. Do not shrink the operation to evade the check.
- **Retries must ship every step.** When re-running a build script after a failure, make sure cleanup and teardown steps are present in each retry. A retry that drops a step can leave duplicate components or orphaned sets behind.
- **Token roles may be named by tone, not role.** A file may have tokens like `surface/cream` rather than `surface/page`, because usage doesn't split cleanly. Bind by the token that matches the property's actual use, and don't force a role-based mapping that isn't in the file.
- **Leftover unbound tokens are common.** After a tokenization pass, newly added token levels may exist in Variables but not yet be bound to nodes. Expect to find some unbound values in Phase 0 and handle them as described there.
- **Cloning a variant does not add a variant.** `clone()` on a variant component places the copy loose on the page, not in the set. Move it in with the set's `appendChild`, then set its position, and confirm the set's child count afterwards.
- **Opacity variables use a 0-100 scale.** A value of `0.4` renders as 0.4%, not 40%. Store opacity tokens as percentages (for example `40`) and read the bound node's `opacity` back to check.
- **Stored paint colors matter for text.** A bound paint built from a placeholder color can render the placeholder. Set each bound paint's stored color to the token's resolved value before binding.
- **Swap one node at a time, and verify against relationships, not just coordinates.** For each original: create the instance, insert it at the same index in the same parent, copy position or layout sizing, set the label and boolean properties from the original, compare, and only then remove the original. Compare each parent frame's size before and after. A position read taken right after inserting can be stale inside auto-layout parents (this run reported two false 185-189px shifts); confirm suspect results with a stable relationship (a gap to a sibling, a distance to the parent's edge) and a screenshot before treating them as real.
- **An instance-swap property has one default for the whole set.** If each variant needs a different nested component (an arrow sized and toned per button variant), do not add a set-level swap property: it forces every variant to the same default. Nest the right instance in each variant and leave it swappable by swapping the nested instance directly. State this to the designer if it differs from what was proposed.
- **Build atoms before molecules, and diff after changing a shared part.** When changing a nested part inside an existing component (for example replacing a text arrow with an icon instance), snapshot every variant's size and the nested part's visibility, plus every placed instance on the page, then diff after the change. Zero differences is the pass condition.
- **The existing file may have role mismatches too.** Surfaces bound to text-scoped tokens (a footer filled with `text/primary`) show up when you look for a fill token for a new component. Propose the missing semantic token (here `surface/dark` aliasing `Black/300`) with a value taken from the file, and get approval before creating it.
- **Wrap caption text in labeled frames.** Fixed-length captions overflow narrow frames. Set `textAutoResize = 'HEIGHT'` and a width equal to the frame minus its padding.
- **Assigning `componentPropertyReferences` replaces the whole object.** If a node needs more than one link (a text node that is both editable and toggleable), set all of them in one assignment, or merge with the existing object. Assigning `{ visible }` after `{ characters }` silently drops the text link. Check every linked node after building by reading its references back.
- **Hidden parts of an instance may not be returned by `findOne`.** When verifying a swap, treat "not found" as "hidden" only where the design needs that part hidden, and as a failure everywhere else. Verify content part by part against the original and remove the original only when every part matches; a failed check rolls the new instance back and leaves the original untouched.
- **A set-wide property default applies to every variant.** Variants that don't use an optional part (a label, a fine print line, a fifth row) still need that part present so the property exists everywhere. Default every optional part to on, and have each swapped instance turn off what its card doesn't use.
- **Build a variant from the drawn card it represents** (clone it, convert the clone, then fix tokens) so typography, nested instances, and spacing carry over exactly. Rename layers to one convention across variants (`Name` and `Plan` to `Plan name`) so text properties merge by name.
- **Zero-size shapes are not zero.** Figma stores a "zero-width" bar or line as about 0.01px, so `width > 0` is true for it. When reading a state from geometry (an active-link bar, a hidden indicator), compare against a real threshold (`> 1`) and cross-check against another signal (font weight, a name). A verification that reuses the same reading as the swap cannot catch the misreading, so verify state against an independent signal or a screenshot.
- **Find nested parts by what they are, not by their layer name.** The same button was named `Button`, `Primary action`, and `Start button` across pages, and a statement was `Statement` on one page and `Tagline` on another. Look up instances by their main component and text by a list of known names, and when a lookup returns null, roll back and inspect instead of guessing.
- **Compare against the original for every property you can still read before removing it.** Record anything you cannot re-derive later (active states, sizing modes, text) before the original is deleted, because there is no undo from the API. If something was misread after removal, say so plainly and name what was inferred.
- **A component's fixed width can clash with a drawn variant that hugs.** When a swapped instance changes the parent's size, override the nested part's sizing on the instance to match the original, report the inconsistency, and offer to normalize it as a separate decision.
- **Parent heights read straight after a swap can be stale too.** A parent section appeared to grow by 207px right after inserting an instance, then read its original height a moment later. Before concluding that a swap changed layout, wait briefly and re-read the parent and the page frame, and compare against the original values you recorded. Report only differences that survive the second reading.
- **Plain-text look-alikes are easy to miss.** A part that looks like a component (an eyebrow) may be a bare text layer on some pages and a framed part on others. Survey by name and type, list the ones that don't match, and decide with the designer whether to normalize them to the component or add an option for them; don't silently skip or force them.
- **Read page child counts only after visiting the page.** A page that has not been opened can report a smaller child count than it really has (a page showed 5 top-level frames, then 15 once it was loaded). Visit every page first, then record counts, or you will report a change you did not make. `loadAllPagesAsync` is not available in this environment; use `figma.setCurrentPageAsync` on each page.
- **Prove a page move with instance counts across the whole file.** Count the instances of every moved component on every page before and after (including instances nested inside other components) and require the totals to match per component. Compare the totals, not a per-page split, since the move shifts instances between pages.
- **Look for frames you did not create before moving things.** New top-level frames (for example `Docs/...` frames) may belong to the designer's own documentation. Report them and ask; do not move, rename, or delete them.
- **Component pages replace a single catch-all section.** Group frames by component type on their own pages. If an earlier run left every component frame in one section on a design page, offer to move them onto type pages as a separate, confirmed step, and check instance counts before and after.
- **Semantic splitting should earn its place.** If an accent and a surface share the same shade with no reason to diverge, reuse one token for both. Apply the same judgment when deciding whether a state needs its own token or can reuse an existing one.
