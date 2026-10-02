# ScreenSpec

Turns a feature idea into a complete, implementation-agnostic screen spec that
engineers and other agents can build from. The output is an Emmet-style layout
tree plus Markdown covering data, behavior, states, responsiveness, and
accessibility. It never contains HTML, CSS, or framework code.

## Why

A feature description says what to build. A mockup says how it looks. Neither
says what the screen does when the data is empty, slow, or broken, who is
allowed to do what, or where focus goes when a dialog closes. ScreenSpec
writes that down before any code exists, in a form that is precise enough to
build from and tied to no framework.

## Use it

Ask Claude to design, wireframe, or spec a screen, page, dashboard, modal, or
multi-step flow:

- "Design the order triage screen for our support team"
- "Spec the checkout flow before we start building"
- "Write a UX spec for this feature doc"

Claude then:

1. Looks at what already exists: feature docs, connected screens, and the
   component library in your repo, or the files you attached
2. Asks only what it could not find out, one question at a time
3. Fills a fixed template, so every spec has the same sections
4. Checks the spec against its own rules before handing it over
5. Offers the adjacent screens the design implies

## Two depths

- **Full** (default): all 15 sections. Use it for anything that will be built
  from the spec.
- **Light**: 8 compact sections for a quick pass or a small surface such as a
  confirmation dialog. Ask for a "light" or "quick" spec. The layout tree is
  just as precise; only the prose is shorter, and it can be expanded to a full
  spec later.

## What a full spec contains

| Section | What it pins down |
| --- | --- |
| Purpose, Users and Goals | Why the screen exists and who can do what |
| Assumptions and Open Questions | What was not known, and what changes if it is wrong |
| Entry and Exit Points | How the user arrives and where they go next |
| Data Requirements | What the screen reads and writes, how much, how fresh |
| Layout Structure | The nesting of regions, as a tree |
| Component Details | Fields, behavior, validation, and four states per component |
| User Flow, Interaction Rules | What happens, in order, when the user acts |
| Screen States | First use, no results, loading, error, no permission |
| Responsive Behavior | What changes at each size, and why |
| Accessibility | Keyboard, focus, screen reader, non-color cues |
| Future Extensibility | What the design must not block later |

## The layout notation

Structure is written as a tree with layout attributes, never as markup:

```emmet
card[col, gap=sm]
  > header[row, justify=between, align=center, gap=sm]
    > customerName[fill]
    + metaRail[col, align=end, hug]
      > billNo
      + date
  + addressLine
```

`>` is a first child, `+` a later sibling. The attributes (`row`, `col`,
`grid`, `justify`, `align`, `gap`, `fill`, `hug`) are primitives that CSS,
React Native, SwiftUI, and Compose all share, so the same spec builds on any
of them. Color, fonts, and pixel values are deliberately left out.

## What it is not for

- Writing UI code. Build from the spec as a separate step.
- Visual mockups, color, or typography.
- Choosing chart types and encodings. It specs the screen around a chart.

## Contents

```
skills/screenspec/
  SKILL.md                      Workflow, rules, and self-check
  references/spec-template.md   The full and light templates
  references/layout-notation.md The tree syntax and attribute vocabulary
  references/example-spec.md    A complete worked example
```

## Data

ScreenSpec is Markdown instructions only. It runs no scripts, bundles no
connectors, and sends no data to any service. It may read feature docs and
component files in the repository you are working in, to ground the spec in
what already exists.

## License

MIT
