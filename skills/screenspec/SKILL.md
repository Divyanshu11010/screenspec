---
name: screenspec
description: Produce an implementation-agnostic UI spec for a screen, page, dashboard, modal, or multi-step flow - layout as an Emmet-style tree, plus data, behavior, states, responsiveness, and accessibility in Markdown. Use when asked to design, wireframe, or spec a screen or flow, to describe a UI's layout or information architecture in text, or when a feature needs a layout and interaction spec before implementation starts. Writes a full spec by default, or a light one for small surfaces and quick passes. Not for writing UI code (HTML, CSS, React, Vue, SwiftUI) or producing visual mockups.
---

# ScreenSpec

Act as a senior product designer and UX architect. Turn a feature idea into a
complete screen spec that engineers and other agents can build from without
asking follow-up questions: an Emmet-style layout tree, plus Markdown covering
data, behavior, states, responsiveness, and accessibility.

The spec is implementation-agnostic. It describes structure and behavior,
never HTML, CSS, framework code, colors, or typography.

**Core principle:** every element must have a stated reason to exist. "Table"
is a name. "Table, because agents bulk-triage 200+ orders by status" is a
design decision.

## Scope

Use this skill to:

- Design a screen, page, admin panel, dashboard, modal, drawer, or multi-step flow
- Give a feature description the layout and interaction spec it lacks
- Write a wireframe, information architecture, or UX spec in text form

Hand off instead when the user wants:

- **Working UI code.** That is implementation. If a spec is also wanted, finish
  it first, then build from it as a separate task.
- **A visual mockup, or color, type, and brand direction.** That is visual design.
- **The design of a chart itself.** Spec the screen around the chart here, and
  leave chart type, scales, and encoding to data-visualization guidance.

## Depth

Every spec is written at one of two depths. State which in the header line.

| Depth | Use when | Contains |
| --- | --- | --- |
| **Full** (default) | The screen will be built from this spec, or nothing says otherwise | All 15 sections |
| **Light** | The user asks for a quick, light, brief, or rough spec; or the surface is small and self-contained, such as a confirmation dialog, a toast, or a simple modal | 8 sections, each compact |

A light spec is a shorter document, not a looser one. Every rule below still
applies: direction tags, a reason per component, no styling, no code, recorded
assumptions. It drops sections; it does not drop precision. End a light spec
with one line naming the sections it left out and offering the full spec.

If the user asks to expand a light spec, keep what is written and add the
missing sections around it; the headings are the same in both.

## Workflow

1. **Ground in what exists.** Look for prior art before inventing anything.
   - In a codebase: feature or product docs (`docs/`, `specs/`, `design/`, a
     README, or wherever this repo keeps them), the routes and screens this one
     connects to, and the component library it must fit.
   - In a conversation with no codebase: the user's description, attached
     files, and screenshots.

   Reuse existing names, patterns, and components. If there is nothing to
   find, move on without comment.

2. **Close the gaps that block the design.** Ask only what the context did not
   answer, one question at a time, most blocking first:
   - What is the user trying to get done on this screen?
   - Who is the primary user, and do other roles see it differently?
   - What data does it read and write, and roughly how much of it?
   - Where does the user arrive from, and where do they go next?
   - What constraints apply (platform, existing component library, compliance)?

   Stop asking once you can design. If the user is unavailable or tells you to
   proceed, pick the most reasonable answer and record it under Assumptions
   and Open Questions. Never guess silently.

3. **Draft the spec.** Read [references/spec-template.md](references/spec-template.md)
   and [references/layout-notation.md](references/layout-notation.md), then
   fill the template for the chosen depth. For a flow of several screens, write the flow overview
   from the template first, then spec one screen at a time. Read
   [references/example-spec.md](references/example-spec.md) when unsure how
   much detail a section needs. Always read the template and the notation when they
   are readable, at either depth. Only if a read fails, build the spec from
   the two summaries below.

4. **Run the self-check** below and fix whatever fails.

5. **Deliver.** Put the spec in the conversation. Save it as a Markdown file
   when the user asks for one, or when the repo already keeps specs somewhere;
   follow that folder's naming.

6. **Offer adjacent screens.** Name the screens this one implies (detail view,
   create or edit form, confirmation step) and ask which to spec next. Do not
   assume they are in scope.

## Notation at a glance

Full reference: `references/layout-notation.md`.

```emmet
screen[col]
  > header[row, justify=between, align=center, gap=md]
    > title[fill]
    + actions[row, gap=sm, hug]
      > exportButton?
      + createButton
  + orderList[col, fill, scroll=y]
    > orderRow*
  + orderDrawer?[overlay, col]
```

- One node per line in a fenced `emmet` block, two spaces of indent per level,
  camelCase names by role. `>` marks a first child, `+` each later sibling.
- Attributes go in brackets: `name[attr, attr=value]`.
- Container: `row` | `col` | `grid` (with `cols=N`), `justify=start|center|end|between|around`,
  `align=start|center|end|stretch`, `wrap`, `gap=xs|sm|md|lg|xl`, `scroll=x|y`.
- Child: `flex=N`, `fill`, `hug`, `w=NN%`, `h=NN%`, `sticky=top|bottom`, `overlay`.
- Markers before the brackets: `name*` repeats per data item, `name?` is conditional.

## Spec sections

Full template: `references/spec-template.md`. Every spec has these sections,
in this order. Use each name as a level-two heading exactly as written, with
no number in the heading.

Open every spec, at either depth, with the screen name as the title and then
this header line:

`**Primary user:** <role> | **Platform:** <platform> | **Depth:** Full or Light | **Status:** Draft`

A full spec has all fifteen:

1. **Purpose**: the task the screen enables, plus what is out of scope
2. **Users and Goals**: table of role, can do, cannot do; then goals
3. **Assumptions and Open Questions**: table of # (A1, Q1), assumption or
   question, impact if wrong, status
4. **Entry and Exit Points**: table of direction, screen, trigger, state carried
5. **Data Requirements**: table of data, read or write, volume, freshness
6. **Layout Structure**: the tree
7. **Layout Breakdown**: per region, its purpose, contents, and show condition
8. **Component Details**: per component, a level-three heading, then Type,
   Why it exists, Data, a Fields table, Behavior, Validation, and a States
   table with rows Empty, Loading, Populated, Error
9. **User Flow**: primary path as numbered steps, then alternate paths
10. **Interaction Rules**: table of trigger, result, feedback, reversible
11. **Validation Rules**: cross-field and submit-level rules
12. **Screen States**: table covering first use, no results, loading, error,
    no permission
13. **Responsive Behavior**: table of wide, medium, narrow, with reasons
14. **Accessibility**: keyboard, focus, screen reader, non-color cues,
    targets and motion
15. **Future Extensibility**: what the design must not block

A **light** spec has these eight, in this order, under the same headings:

1. **Purpose**: two sentences, plus what is out of scope
2. **Assumptions and Open Questions**: the same table
3. **Layout Structure**: the tree, as precise as in a full spec
4. **Component Details**: one table for all components, with columns
   Component, Type, Why it exists, Behavior
5. **Interaction Rules**: the same table
6. **Screen States**: one table covering empty, no results, loading, and
   error, naming the component wherever its behavior differs from the screen's
7. **Responsive Behavior**: bullets for what changes when narrower
8. **Accessibility**: bullets for focus, keyboard, and anything not obvious

Close a light spec with this line, listing what was left out:

`Light spec. Not covered: Users and Goals, Entry and Exit Points, Data Requirements, Layout Breakdown, User Flow, Validation Rules, Future Extensibility. Ask for the full spec to add them.`

## Rules

- **Layout is a tree, behavior is prose.** Write the hierarchy in the notation
  from `references/layout-notation.md`. Write everything else as Markdown.
- **Tag direction on every node with two or more children**: `row`, `col`, or
  `grid`. Platforms default this differently, so an untagged container reads
  as two different layouts to two different readers.
- **No styling.** No color, font, border, shadow, radius, pixel or rem value,
  or framework class name anywhere in the spec, including in accessibility
  notes: say "meets the platform's minimum target size", not a number. Layout attributes (direction,
  justify, align, gap, flex) are structure and are allowed.
- **No code in the spec**, not even a quick snippet. It pre-empts engineering
  decisions the spec exists to leave open.
- **Every component states why it exists**: the user task or data need it serves.
- **Every data-bearing component covers four states**: empty, loading,
  populated, error. A state you did not design is one the user will hit anyway.
  In a light spec these live in the Screen States table instead of a table
  per component.
- **Design for the data volume you were told.** If it is unknown and you
  cannot ask, assume it grows large (pagination, filtering, bulk actions) and
  record that as an assumption.
- **Fill every section of the chosen depth.** If a section does not apply,
  write `N/A` and the reason. A section you cannot fill means information is missing.
- **Do not invent requirements.** Anything the user or the codebase did not
  supply is an assumption and belongs in the assumptions table.
- **Say each thing once**, in the section that owns it. Other sections refer
  to it by name instead of restating it.
- **Use the product's own vocabulary.** Take names from the codebase and the
  user's domain, not generic placeholders.

## Self-check

Before delivering, confirm each of these:

- [ ] Every node with two or more children is tagged `row`, `col`, or `grid`
- [ ] The first child of each node is marked `>`, later siblings `+`, and
      indentation matches nesting
- [ ] Every region and component in the tree is described in the prose, and
      nothing in the prose is missing from the tree
- [ ] Every component has a reason that names a user task or data need
- [ ] Every data-bearing component has all four states, each saying what the
      user sees and what they can do
- [ ] Every conditional (`?`) node states its condition
- [ ] Every action has a result and feedback; destructive actions have a
      confirmation or an undo
- [ ] No styling vocabulary and no code anywhere
- [ ] Every assumption is in the assumptions table
- [ ] No template placeholder or empty section remains
- [ ] The header line states the depth, and a light spec ends by naming the
      sections it left out

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| Describing pixels, colors, or fonts | Stay at layout and behavior altitude; visual design is a separate job |
| Writing CSS or Tailwind class names in the tree | Use the layout attributes instead; same precision, no framework |
| Leaving a multi-child node's direction untagged | Tag it `row`, `col`, or `grid` explicitly |
| Naming a component without a reason | Add the task or data need it serves |
| Designing only the populated state | Give every data-bearing component all four states |
| Treating "no data yet" and "no results for this filter" as one state | They need different messages and different actions |
| Inventing who the user is or what data flows | Ask, or record it as an assumption |
| Adding example code to be helpful | Leave it out; the spec is the deliverable |
| Speccing only the main screen of a flow | Ask which adjacent screens are in scope |
