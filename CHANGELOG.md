# Changelog
All notable changes to `Avalonia.Controls.DataGrid` are documented here.
---
## 2026-05-21
### Fixed
- **Columns appear squished (~5px) when made visible after being hidden**  
  When toggling a column from `IsVisible=false` to `IsVisible=true`, Auto and
  SizeToHeader columns rendered at near-zero width until the window was resized.  
  Root cause: `OnColumnVisibleStateChanged` did not reset the column's width
  measurement state. The DataGrid reused stale `DesiredValue`/`DisplayValue` from
  before the column was hidden (or from initial construction when the column started
  hidden), so `AutoSizeColumn` skipped remeasuring it entirely.  
  Fix: When a column becomes visible, `IsInitialDesiredWidthDetermined` is reset to
  `false` and the column's `DesiredValue`/`DisplayValue` are set to `double.NaN` via
  `SetWidthInternalNoCallback`, forcing `CoerceWidth` to recalculate them from
  scratch. `OnColumnWidthChanged` is then called to trigger the layout pass.  
  Affects: any column using `Auto`, `SizeToHeader`, or `SizeToCells` width modes that
  starts hidden or is toggled hidden/visible at runtime.

---
## 2026-05-08
### Added
- **Drag-to-select rows** (`DataGridSelectionMode.Extended` only)  
  Holding the left mouse button and moving the pointer across rows now extends the
  selection using the same anchor-based range logic as `Shift`+arrow-key navigation.
  - Works on both cell (`DataGridCell`) and row-header (`DataGridRowHeader`) areas.
  - Moving back over already-selected rows correctly shrinks the selection (see bug fix
    below) so the live range always reflects anchor → current pointer position.
- **Auto-scroll during drag-select**  
  When dragging to select rows, moving the pointer into a 30 px margin zone near the
  top or bottom edge of the rows presenter now automatically scrolls the grid at
  50 ms intervals (one row per tick via `ProcessVerticalScroll`) while continuously
  extending the selection to the nearest visible boundary row.  Scroll and selection
  are kept separate: the `DispatcherTimer` only scrolls; selection is re-evaluated
  from the stored pointer position after each tick.  The timer fires once immediately
  on start (no initial delay) and is stopped as soon as the pointer moves back into
  the safe zone or the mouse button is released.  
  Inspired by the auto-scroll pattern used in `Avalonia.Controls.TreeDataGrid`.
### Fixed
- **Drag-select did not extend selection across rows** (regression fix)  
  Because Avalonia gives implicit pointer capture to the initially-pressed element,
  `OnPointerMoved` always fired on the same `DataGridCell` / `DataGridRowHeader`,
  so the slot never changed and selection was never extended.  
  Fix: `UpdateStateOnDragSelectMove` now accepts `PointerEventArgs` instead of a
  pre-calculated slot, calls `e.GetPosition(_rowsPresenter)` to obtain the pointer
  Y coordinate, and walks `DisplayData.GetScrollingElements()` to find the actual
  `DataGridRow` under the pointer.
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
