# Example Spec

A complete full-depth spec for one screen, showing how much detail each section needs.
Read it for depth and tone, not to copy its content.

---

# Order Triage

**Primary user:** Support agent | **Platform:** Web, desktop first | **Depth:** Full | **Status:** Draft

## Purpose

Lets a support agent find orders that need attention and move them forward in
bulk. Without it, agents open orders one at a time from customer emails, and
stuck orders go unnoticed until a customer complains.

**Out of scope:** editing order contents and issuing refunds. Both happen on
Order Detail.

## Users and Goals

| Role | Can do here | Cannot do here |
| --- | --- | --- |
| Support agent | View, filter, assign orders to self, change status | Assign to others, export |
| Support lead | Everything an agent can, plus assign to anyone and export | None |

Goals:

- Find every order in a given status within seconds
- Move many orders to the next status in one action
- Check one order's history without losing their place in the list

## Assumptions and Open Questions

| # | Assumption or question | Impact if wrong | Status |
| --- | --- | --- | --- |
| A1 | About 5,000 orders are open at any time, and the number grows | Server-side paging and filtering would be unnecessary | Assumed |
| A2 | A status change can be reversed by setting the previous status | Bulk changes would need a confirmation step instead of undo | Assumed |
| Q1 | Should the list update live as other agents work? | Adds conflict handling to bulk actions | Open |

## Entry and Exit Points

| Direction | Screen | Trigger | State carried |
| --- | --- | --- | --- |
| In | Main navigation | Select Orders | None. Opens with the "Needs attention" status filter |
| In | Notification | Follow the link | The filter preset named in the link |
| Out | Order Detail | Open full order from the drawer | Order ID. Filters, page, and scroll position are restored on return |
| Out | File download | Export | Current filters |

## Data Requirements

| Data | Read / write | Volume | Freshness | Notes |
| --- | --- | --- | --- | --- |
| Orders: number, customer name, status, total, placed date, assignee | Read | About 5,000 open, 50 per page | On load, on manual refresh, after any change | Filtering, sorting, and paging happen on the server |
| Order status and assignee | Write | 1 to 50 orders per action | Immediate | Some orders in a batch can fail while others succeed |
| Order timeline | Read | Up to about 100 events per order | Loaded when the drawer opens | Newest first |

## Layout Structure

```emmet
screen[col]
  > header[row, justify=between, align=center, gap=md]
    > titleBlock[col, fill]
      > breadcrumb
      + title
    + headerActions[row, gap=sm, hug]
      > refreshButton
      + exportButton?
  + filterBar[row, align=center, gap=sm, wrap]
    > searchField[fill]
    + statusFilter[hug]
    + dateRangeFilter[hug]
    + clearFilters?[hug]
  + bulkBar?[row, justify=between, align=center, gap=md]
    > selectionCount
    + bulkActions[row, gap=sm, hug]
      > assignButton
      + changeStatusButton
  + orderTable[col, fill, scroll=y]
    > tableHeader[sticky=top]
    + orderRow*[row, align=center, gap=md]
      > selectCheckbox[hug]
      + orderNumber[hug]
      + customerName[fill]
      + statusBadge[hug]
      + total[hug]
      + placedAt[hug]
  + pagination[row, justify=between, align=center]
    > rangeSummary
    + pageControls[hug]
  + orderDrawer?[overlay, col, gap=md]
    > drawerHeader[row, justify=between, align=center]
      > drawerTitle[fill]
      + closeButton[hug]
    + orderSummary
    + timeline[fill, scroll=y]
    + openFullOrder
```

## Layout Breakdown

### header

Purpose: tells the agent where they are and holds actions that apply to the
whole list.

Contains:

- breadcrumb and title: location within the back office
- refreshButton: reloads the list with the current filters, because the list
  does not update live (see Q1)
- exportButton: downloads the filtered list. Shown when: the user is a support lead

### filterBar

Purpose: narrows 5,000 orders to the ones the agent can act on now.

Contains:

- searchField: order number or customer name, the two things a customer quotes
- statusFilter and dateRangeFilter: the two dimensions agents triage by
- clearFilters. Shown when: any filter differs from the default

### bulkBar

Purpose: makes bulk actions visible only when they can apply, so the screen
stays quiet while browsing.

Contains:

- selectionCount: how many orders the next action will touch
- assignButton and changeStatusButton: the two changes agents make in bulk

Shown when: one or more rows are selected. The table moves down to make room.

### orderTable

Purpose: the working surface. One row per order, dense enough to compare many
at once.

### pagination

Purpose: tells the agent how much is left and moves between pages.

### orderDrawer

Purpose: shows one order's history beside the list, so the agent keeps their
filters and scroll position.

Shown when: the agent opens a row.

## Component Details

### filterBar

Type: Filter group

Why it exists: agents work one status at a time, and no agent can scan 5,000
rows by eye.

Data: reads the list of statuses. Writes nothing; it changes the query for
orderTable.

Fields:

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| searchField | Text | No | Matches order number exactly or customer name by prefix |
| statusFilter | Multi-select | No | Defaults to "Needs attention" |
| dateRangeFilter | Date range | No | Defaults to the last 30 days |

Behavior:

- Changing any filter reloads the table from page 1 and clears the selection
- Search runs after the agent pauses typing, not on every keystroke
- Active filters are kept in the address so a filtered view can be shared

Validation:

- The end date cannot be before the start date; checked when the range is applied

States:

| State | What the user sees | What the user can do |
| --- | --- | --- |
| Empty | Default filters applied | Change any filter |
| Loading | Status options show a placeholder | Type in search |
| Populated | Chosen filters shown as set | Change or clear filters |
| Error | Status options failed to load, with a retry link | Search and date still work |

### orderTable

Type: Table

Why it exists: agents compare many orders at once and act on several together,
which a card list or one-at-a-time view cannot support.

Data: reads orders for the current filters, sort, and page.

Fields:

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| orderNumber | Text | Yes | Opens the drawer |
| customerName | Text | Yes | Truncates when long; full name available on focus and hover |
| statusBadge | Status | Yes | Always includes the status name as text |
| total | Currency | Yes | Sortable |
| placedAt | Date and time | Yes | Sortable. Default sort, oldest first, so the longest-waiting order is on top |

Behavior:

- Selecting a row adds it to the selection; the header checkbox selects every row on the page
- Selection is limited to the current page and cleared when the page or filters change
- Column headers toggle the sort direction

Validation:

- N/A. The table has no input fields.

States:

| State | What the user sees | What the user can do |
| --- | --- | --- |
| Empty | See Screen States: first use and no results differ | Clear filters |
| Loading | Placeholder rows in place of data; header stays | Change filters |
| Populated | Up to 50 rows | Select, sort, open a row |
| Error | A message in place of the rows, with the cause if known | Retry; filters stay as set |

### bulkBar

Type: Action bar

Why it exists: moving orders one at a time is the slow path this screen replaces.

Data: writes status and assignee for the selected orders.

Fields:

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| selectionCount | Text | Yes | "12 selected" |
| assignButton | Action | No | Agents assign to self; leads choose any agent |
| changeStatusButton | Action | No | Offers only statuses valid for every selected order |

Behavior:

- Applies the change to every selected order in one request
- On partial failure, the orders that failed stay selected and the rest are deselected

Validation:

- If the selected orders share no valid next status, changeStatusButton is disabled and says why

States:

| State | What the user sees | What the user can do |
| --- | --- | --- |
| Empty | Not shown; nothing is selected | N/A |
| Loading | Actions disabled, progress shown on the one in flight | Wait |
| Populated | Count and both actions | Assign, change status, clear selection |
| Error | How many succeeded and how many failed, and why | Retry the failed ones |

### orderDrawer

Type: Drawer

Why it exists: agents need an order's history to decide what to do with it,
and leaving the list would lose their place.

Data: reads the order summary and timeline for one order.

Fields:

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| orderSummary | Read-only group | Yes | Customer, items count, total, current status, assignee |
| timeline | List | Yes | Status changes, notes, and messages, newest first |
| openFullOrder | Link | Yes | Goes to Order Detail |

Behavior:

- Opens over the right side of the list; the list stays visible and scrollable
- Opening another row replaces the drawer's content without closing it
- Closes on closeButton, on Escape, or when filters change

Validation:

- N/A. The drawer is read-only.

States:

| State | What the user sees | What the user can do |
| --- | --- | --- |
| Empty | Summary, and "No activity yet" in place of the timeline | Open the full order |
| Loading | Order number in the title, placeholders below | Close |
| Populated | Summary and timeline | Scroll the timeline, open the full order |
| Error | A message in place of the content | Retry, close |

## User Flow

Primary path:

1. The agent arrives and sees orders needing attention, oldest first.
2. The agent narrows by status or searches for a customer.
3. The agent opens a row to read its timeline in the drawer.
4. The agent selects the orders ready to move on and changes their status.
5. The list reloads, the moved orders leave the filtered view, and a
   confirmation offers undo.

Alternate paths:

- No orders match: the agent sees the no-results state and clears filters.
- Some orders fail to update: the failed ones stay selected with the reason,
  and the agent retries or opens them individually.
- The agent arrives from a notification: the preset filter is applied and
  shown as active in filterBar.

## Interaction Rules

| Trigger | Result | Feedback | Reversible |
| --- | --- | --- | --- |
| Change a filter | Table reloads from page 1; selection cleared | Table shows loading rows | Yes, clear filters |
| Select a row | Row joins the selection; bulkBar appears | Count updates | Yes, deselect |
| Open a row | Drawer opens with that order | Row is marked as open | Yes, close |
| Change status in bulk | Selected orders updated | Confirmation with the count and an undo action | Undo |
| Assign in bulk | Selected orders assigned | Confirmation with the count and assignee | Undo |
| Export | File download starts | Progress, then a completion message | No |
| Refresh | Table reloads with the same filters and page | Table shows loading rows | N/A |

## Validation Rules

- A bulk status change is allowed only to a status valid for every selected
  order. Checked when the selection changes; the action explains which orders
  block it.
- A bulk action covers at most the 50 orders on the current page.

## Screen States

| State | When | What the user sees | Available actions |
| --- | --- | --- | --- |
| First use | The shop has no orders at all | "No orders yet" and where orders come from | None |
| No results | Filters or search match nothing | "No orders match these filters" with the active filters named | Clear filters |
| Loading | First load | Header and filterBar in place, placeholder rows | Change filters |
| Error | The order list cannot load | What went wrong, in plain words | Retry |
| No permission | The role has no access to orders | "You do not have access to orders" | Return to the home screen |

## Responsive Behavior

| Size | What changes | Why |
| --- | --- | --- |
| Wide | As in the tree; the drawer sits beside the table | Agents compare the list and one order together |
| Medium | total and placedAt columns hide; the drawer covers the table | Number, customer, and status are what triage needs |
| Narrow | Rows become stacked cards; filters collapse behind one control; bulk actions move to a bar pinned to the bottom | A table cannot be scanned at this width, and actions must stay in thumb reach |

## Accessibility

- Keyboard: tab order is filterBar, bulkBar when shown, table, pagination.
  Arrow keys move between rows, Space selects, Enter opens the drawer.
- Focus: on load, focus goes to searchField. When the drawer opens, focus
  moves to its title; when it closes, focus returns to the row that opened it.
  After a bulk action, focus goes to the confirmation.
- Screen reader: refreshButton, closeButton, and each row checkbox have names
  that include their target ("Select order 10482"). The result count and the
  outcome of a bulk action are announced when they change.
- Non-color cues: status is always written as text, never conveyed by color alone.
- Targets and motion: row actions stay large enough to tap on touch screens.
  The drawer appears without animation when reduced motion is requested.

## Future Extensibility

- Saved filter sets: filterBar keeps filters in the address, so a saved view
  is a stored address.
- Live updates (Q1): rows are keyed by order ID, so a row can update in place
  without reloading the table.
- More bulk actions: bulkActions is a row of independent actions and can grow
  into an overflow menu.
