# Changelog

All notable changes to `Avalonia.Controls.DataGrid` are documented here.

---

## [Unreleased] – 2026-05-08

### Added

- **Drag-to-select rows** (`DataGridSelectionMode.Extended` only)  
  Holding the left mouse button and moving the pointer across rows now extends the
  selection using the same anchor-based range logic as `Shift`+arrow-key navigation.
  - Works on both cell (`DataGridCell`) and row-header (`DataGridRowHeader`) areas.
  - Moving back over already-selected rows correctly shrinks the selection (see bug fix
    below) so the live range always reflects anchor → current pointer position.

### Fixed

- **Range selection did not deselect rows when shrinking the range**  
  When using `Shift`+arrow keys (or the new drag-select feature) to extend a range
  selection, moving the cursor *back* toward the anchor left previously-selected rows
  highlighted even though they were no longer within the anchor-to-current range.  
  Root cause: `SetRowsSelection` only called `SelectSlots(..., true)` and never
  removed slots that fell outside the new `[startSlot, endSlot]` bounds.  
  Fix: `SetRowsSelection` now iterates all currently-selected slots before selecting
  the new range and calls `SelectSlots(slot, slot, false)` for any slot that lies
  outside `[startSlot, endSlot]`, correctly shrinking the highlighted region and
  raising `SelectionChanged` for the removed items.  
  Affects: `DataGrid.SelectAll()`, `Shift`+arrow-key range selection, and the new
  mouse drag-select feature.
