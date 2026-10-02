# Layout Notation

An Emmet-style tree that states a screen's structure precisely enough to build
from, without naming any framework. The attributes map to primitives every UI
toolkit has under different names (CSS, React Native, SwiftUI, Compose).

## Tree syntax

- One node per line. The root has no prefix.
- Indent two spaces per level of nesting.
- `>` marks the first child of a node. `+` marks each later sibling.
- Name nodes in camelCase by their role: `orderTable`, not `table1` or `div`.
- Attributes go in brackets after the name: `name[attr, attr=value]`.

```emmet
screen[col]
  > header[row, justify=between, align=center, gap=md]
    > title[fill]
    + actions[row, gap=sm, hug]
      > exportButton
      + createButton
  + content[col, fill, gap=md, scroll=y]
    > summary
    + orderList
```

## Node markers

| Marker | Example | Meaning |
| --- | --- | --- |
| `*` | `orderRow*` | Repeated once per data item (list rows, cards, tabs) |
| `?` | `bulkBar?` | Conditional. State the condition in the Layout Breakdown |

Markers come before the brackets: `orderRow*[row, align=center]`.

## Container attributes

For any node with children. Direction is mandatory when a node has two or
more children; the rest are optional.

| Attribute | Values | Meaning |
| --- | --- | --- |
| direction | `row` \| `col` \| `grid` | How children are arranged. Never leave it implied: CSS and React Native default it in opposite ways |
| cols | `cols=N` \| `cols=auto` | Only with `grid`. A fixed column count, or as many as fit |
| justify | `start` \| `center` \| `end` \| `between` \| `around` | How children distribute along the main axis |
| align | `start` \| `center` \| `end` \| `stretch` | How children align on the cross axis |
| wrap | `wrap` | Children wrap onto new lines. Omit for no wrapping |
| gap | `xs` \| `sm` \| `md` \| `lg` \| `xl` | Relative spacing between children. A scale token, never a pixel value |
| scroll | `scroll=y` \| `scroll=x` | This region scrolls on its own, independent of the screen |

## Child attributes

How a node takes space within its parent.

| Attribute | Values | Meaning |
| --- | --- | --- |
| flex | `flex=N` | Proportional share of the parent's spare main-axis space. A ratio, not a size |
| fill | `fill` | Shorthand for `flex=1`: take all remaining space |
| hug | `hug` | Sized to its content, does not grow. State it only where filling would otherwise be assumed |
| w / h | `w=NN%` \| `h=NN%` | Fixed share of the parent. Use only when the proportion is truly fixed; prefer `flex`, which survives changes in text length and screen size |
| sticky | `sticky=top` \| `sticky=bottom` | Stays pinned while its scrolling ancestor scrolls |
| overlay | `overlay` | Rendered above the screen, outside normal flow: modal, drawer, popover, toast. List overlays last, as children of the root. When the overlay is the whole surface being specced, it is the root |

A node can carry both kinds: `actions[row, gap=sm, hug]` lays out its own
children in a row and hugs its content inside its parent.

## Worked example

A card header where the customer name takes all spare width and truncates,
and a metadata column hugs its own content:

```emmet
card[col, gap=sm]
  > header[row, justify=between, align=center, gap=sm]
    > customerName[fill]
    + metaRail[col, align=end, hug]
      > billNo
      + date
  + addressLine
```

Read without any prose: `card` stacks a header above an address line.
`header` lays its two children in a row, pushed apart, with a small gap.
`customerName` takes the remaining width. `metaRail` stacks two lines,
right-aligned, sized to its content.

## Responsive layouts

The tree describes the widest layout. Describe what changes at narrower sizes
in the Responsive Behavior section. If a narrower size restructures the screen
rather than adjusting it, add a second tree labelled for that size.

## What stays out

Color, font, border, shadow, radius, exact pixel or rem values, and any
framework class name. Those are visual styling, not structure, and belong to
visual design or to the implementation.
