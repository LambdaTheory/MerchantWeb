# Machine Inventory Workbench Redesign

## Goal

Replace the over-compressed machine inventory list with a complete merchant inventory workbench. The page must make machine selection, quantity entry, validation, and batch submission easy to understand without adding unnecessary steps or duplicate actions.

The existing backend inventory APIs, snapshot semantics, CentralizedDataHub synchronization, preview mode, and product application flow remain unchanged.

## Product Decisions

- The page keeps the name `机器库存`.
- Inventory is a one-time or periodic snapshot, not real-time stock locking.
- A merchant selects one or more machines, enters only the current quantity, and submits the batch once.
- Machines can come from both the merchant's enabled products and the platform catalog.
- Reporting a platform catalog machine does not automatically enable that product for the merchant.
- Only one primary submit button is shown.
- Empty search does not render the whole machine catalog. Search results appear only after the merchant enters a brand or model.
- Selected machines remain visible in the inventory draft regardless of the active source tab or search keyword.

## Information Architecture

The desktop page uses one framed two-column workspace rather than several unrelated cards.

### Page Header

- Visible page title: `机器库存`
- Short supporting text: `更新当前可用数量`
- Latest successful inventory timestamp when one exists

### Left Pane: Machine Picker

- Section title: `选择机器`
- Segmented source control:
  - `我的商品`
  - `平台机器库`
- Search input for brand, model, category, and keywords
- A short source-specific note beneath the search field
- Search result rows containing:
  - machine name
  - source or availability state
  - latest submitted quantity when available
  - selection state
- The full catalog is never rendered before a search term is entered.
- If there is no match, the existing quick product application flow can be opened from the search area.
- The result row itself is the selection target, so a separate `加入` button is not required.

### Right Pane: Current Snapshot

- Section title: `本次库存`
- Live machine count and total quantity
- One row per selected machine containing:
  - machine name
  - source label
  - latest submitted quantity and timestamp when available
  - numeric quantity input with a visible `台` suffix
  - icon-only remove action
- An empty state is shown before any machine is selected.
- The footer contains validation or submission summary text and one `提交库存快照` button.
- After a successful request, the page confirms that the snapshot was submitted and synchronized to the data relay platform.

## Interaction Rules

- Clicking or keyboard-activating a search result adds it to the right pane and focuses its quantity input.
- Selecting an already-added result focuses the existing quantity input instead of creating a duplicate.
- Pressing Enter in search selects an exact match or the only result. If there are no matches, it opens the quick product application flow.
- Pressing Enter in a valid quantity field advances to the next incomplete row and submits after the final valid row.
- Pressing Escape in a quantity field removes that row.
- Quantity must be a non-negative integer. Blank, negative, and decimal values are invalid.
- The submit button is disabled while the draft is empty, invalid, or submitting.
- A successful submission clears the draft, refreshes inventory data, and returns focus to search.

## Visual Direction

- Continue the existing black, white, and green merchant workspace identity.
- Use white and soft neutral surfaces with green reserved for active, selected, and primary states.
- Keep card radii at 8px or less in the inventory workspace.
- Use restrained borders, shallow shadows, and clear section hierarchy instead of decorative gradients or oversized typography.
- Keep text concise but retain the operational context merchants need to avoid submitting the wrong machine or quantity.
- Use stable grid tracks for machine names, quantity fields, and icon actions so rows do not shift during editing.
- Avoid cards nested inside cards. The workspace is the frame; rows are structured list items.

## Responsive Behavior

- Desktop: picker and draft appear side by side.
- Tablet and mobile: panes stack vertically, with the picker first and the draft second.
- Search results have a bounded height on small screens.
- Draft rows preserve a stable quantity input and remove action without horizontal overflow.
- The primary submit button spans the available footer width on mobile.

## Accessibility

- Source controls expose tab semantics and selected state.
- Search, quantity, remove, and submit controls have explicit accessible labels.
- Search result rows support Enter and Space.
- Invalid quantity inputs expose `aria-invalid`.
- Focus styles remain visible for keyboard users.
- Loading, success, and error feedback use status or alert semantics where appropriate.

## Testing

- Contract tests verify the two-pane structure, both sources, search-gated results, selected draft rows, quantity validation, one primary submit action, and CentralizedDataHub confirmation copy.
- Unit tests continue to cover inventory payload validation.
- Run the full test suite and production build.
- Visually verify the preview page at desktop and 390px mobile widths, including source switching, search, selection, quantity editing, removal, invalid state, and submission.

## Out Of Scope

- Backend schema or endpoint changes
- Real-time inventory reservation
- Dispatch-driven inventory decrement
- Automatic product access enablement
- Merchant free-text inventory rows
- Inventory history management or editing past snapshots
