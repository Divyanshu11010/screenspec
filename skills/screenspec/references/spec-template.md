# Spec Template

Two depths: the full screen spec (default) and the light spec. Pick one as
described in SKILL.md under Depth. Fill every section of the one you pick. If a section does not apply, write `N/A`
and the reason. Replace everything in angle brackets and delete any guidance
text. The layout tree follows [layout-notation.md](layout-notation.md).

## Full screen spec

````markdown
# <Screen name>

**Primary user:** <role> | **Platform:** <web, mobile, desktop> | **Depth:** Full | **Status:** Draft

## Purpose

<Two or three sentences: the task this screen lets the user complete, and what
goes wrong without it.>

**Out of scope:** <what this screen deliberately does not do>

## Users and Goals

| Role | Can do here | Cannot do here |
| --- | --- | --- |
| <role> | <actions> | <restrictions> |

Goals:

- <Goal, written as an outcome the user wants>

## Assumptions and Open Questions

| # | Assumption or question | Impact if wrong | Status |
| --- | --- | --- | --- |
| A1 | <what you assumed> | <what would change in this design> | Assumed |
| Q1 | <what is still unknown> | <what it blocks> | Open |

## Entry and Exit Points

| Direction | Screen | Trigger | State carried |
| --- | --- | --- | --- |
| In | <where the user arrives from> | <action> | <IDs, filters, selection> |
| Out | <where the user goes next> | <action> | <what is passed on> |

## Data Requirements

| Data | Read / write | Volume | Freshness | Notes |
| --- | --- | --- | --- | --- |
| <entity and the fields used> | <Read, Write> | <how many> | <when it updates> | <sorting, paging, limits> |

## Layout Structure

```emmet
screen[col]
  > header[row, justify=between, align=center, gap=md]
    > title[fill]
    + actions[row, gap=sm, hug]
      > secondaryAction
      + primaryAction
  + main[row, fill, gap=lg]
    > sidebar[col, gap=md, w=25%]
    + content[col, fill, gap=md, scroll=y]
      > sectionA
      + sectionB
  + footer
```

## Layout Breakdown

### <Region name>

Purpose: <why this region exists>

Contains:

- <child and what it is for>

Shown when: <condition, for conditional regions only>

## Component Details

### <Component name>

Type: <Table, Form, Card, Modal, Drawer, Dropdown, ...>

Why it exists: <the user task or data need it serves>

Data: <what it reads and writes>

Fields:

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| <field> | <type> | Yes / No | <format, default, limits> |

Behavior:

- <what it does in response to the user or to data>

Validation:

- <field-level rule and when it is checked>

States:

| State | What the user sees | What the user can do |
| --- | --- | --- |
| Empty | <message and call to action> | <actions> |
| Loading | <placeholder, what stays usable> | <actions> |
| Populated | <normal content> | <actions> |
| Error | <message, cause if known> | <retry or recovery> |

## User Flow

Primary path:

1. <User arrives and sees ...>
2. <User does ...>
3. <System responds ...>

Alternate paths:

- <Condition>: <what happens instead>

## Interaction Rules

| Trigger | Result | Feedback | Reversible |
| --- | --- | --- | --- |
| <user action> | <what changes> | <what the user is shown> | <Undo, Confirm first, No> |

## Validation Rules

<Cross-field and submit-level rules only. Field-level rules live with their
component.>

- <rule, when it is checked, and the message shown>

## Screen States

| State | When | What the user sees | Available actions |
| --- | --- | --- | --- |
| First use | No data exists yet | <message and call to action> | <actions> |
| No results | Filters or search match nothing | <message> | <clear or change filters> |
| Loading | First load | <placeholder> | <what stays usable> |
| Error | The screen cannot load | <message> | <retry> |
| No permission | The role cannot view this screen | <message> | <where to go> |

## Responsive Behavior

| Size | What changes | Why |
| --- | --- | --- |
| Wide | <baseline: as in the tree> | |
| Medium | <what collapses, stacks, or hides> | <reason> |
| Narrow | <what collapses, stacks, or hides> | <reason> |

## Accessibility

- Keyboard: <tab order, shortcuts, how every action is reachable without a pointer>
- Focus: <where focus lands on load, when an overlay opens and closes, after a destructive action>
- Screen reader: <names for controls without visible text, announcements for changes that happen without a page load>
- Non-color cues: <how status and errors are conveyed beyond color>
- Targets and motion: <which controls must meet the platform's minimum target size, stated without pixel values; reduced-motion behavior>

## Future Extensibility

- <Likely future feature, and what in this design leaves room for it>
````

## Light spec

For a quick pass or a small, self-contained surface. Same headings as the full
spec, so it can be expanded later without rewriting. The tree is exactly as
precise as in a full spec; only the prose is shorter.

````markdown
# <Screen name>

**Primary user:** <role> | **Platform:** <web, mobile, desktop> | **Depth:** Light | **Status:** Draft

## Purpose

<Two sentences: the task this screen lets the user complete, and what goes
wrong without it.>

**Out of scope:** <what this screen deliberately does not do>

## Assumptions and Open Questions

| # | Assumption or question | Impact if wrong | Status |
| --- | --- | --- | --- |
| A1 | <what you assumed> | <what would change in this design> | Assumed |

## Layout Structure

```emmet
dialog[overlay, col, gap=md]
  > title
  + message
  + actions[row, justify=end, gap=sm]
    > cancelButton
    + confirmButton
```

## Component Details

| Component | Type | Why it exists | Behavior |
| --- | --- | --- | --- |
| <name from the tree> | <type> | <the task or data need it serves> | <what it does, including its show condition if conditional> |

## Interaction Rules

| Trigger | Result | Feedback | Reversible |
| --- | --- | --- | --- |
| <user action> | <what changes> | <what the user is shown> | <Undo, Confirm first, No> |

## Screen States

| State | When | What the user sees | Available actions |
| --- | --- | --- | --- |
| Empty | <no data yet> | <message and call to action> | <actions> |
| No results | <filters match nothing> | <message> | <clear filters> |
| Loading | <first load or action in flight> | <placeholder, what stays usable> | <actions> |
| Error | <load or action failed> | <message> | <retry or recovery> |

## Responsive Behavior

- <What collapses, stacks, or hides when narrower, and why>

## Accessibility

- Focus: <where it lands on open and on close>
- Keyboard: <how every action is reached and dismissed>
- <Anything else that is not obvious: announcements, non-color cues>

Light spec. Not covered: Users and Goals, Entry and Exit Points, Data
Requirements, Layout Breakdown, User Flow, Validation Rules, Future
Extensibility. Ask for the full spec to add them.
````

Mark a Screen States row `N/A` with the reason when it cannot occur, for
example No results on a screen with no filters.

## Flow overview

For a flow of several screens, write this first, then one screen spec per row,
each at the depth chosen for the flow.

````markdown
# <Flow name>

## Screens

| # | Screen | Purpose | Type |
| --- | --- | --- | --- |
| 1 | <name> | <one line> | <Page, Modal, Drawer> |

## Navigation

1. <Screen A> to <Screen B>: <trigger>, carrying <state>
2. <Screen B> back to <Screen A>: <trigger>, restoring <state>

## Shared State

- <Data or selection that persists across screens, and where it is lost>
````

## What each section answers

Every section pre-answers a question an engineer would otherwise have to ask.

| Section | Question it answers |
| --- | --- |
| Purpose | Why does this screen exist, and what breaks if it is cut? |
| Users and Goals | Who is it for, and who is allowed to do what? |
| Assumptions and Open Questions | What did the designer not know? |
| Entry and Exit Points | How does the user get here, and where next? |
| Data Requirements | What does the backend need to provide? |
| Layout Structure and Breakdown | What is on the screen and how is it nested? |
| Component Details | What does each control read, write, and do at its edges? |
| User Flow and Interaction Rules | What happens, in order, when the user acts? |
| Screen States | What does the screen show when the happy path is not true? |
| Responsive Behavior | What survives, collapses, or hides at each size? |
| Accessibility | What does a keyboard-only or screen-reader user experience? |
| Future Extensibility | What must this design not block later? |
