# Avalonia DataGrid

## About

The official `DataGrid` control for [Avalonia](https://github.com/AvaloniaUI/Avalonia).  

It displays repeating data in a customizable grid.  
See the [documentation](https://docs.avaloniaui.net/docs/reference/controls/datagrid/) for more information.

## Fork Features & Bug Fixes

This unofficial fork contains several enhancements and bug fixes not present in the official Avalonia repository:

### Added Features
- **Drag-to-select rows** (`DataGridSelectionMode.Extended` only): Holding the left mouse button and moving the pointer across rows extends the selection using anchor-based range logic.
- **Auto-scroll during drag-select**: Dragging rows near the top/bottom edge (30px margin) of the rows presenter automatically scrolls the grid vertically.

### Bug Fixes
- **StringFormat Binding Mode**: Fixed `StringFormat` bindings on bound columns forcing `TwoWay` mode (even on read-only grids), which caused cell content corruption during scroll recycling.
- **Vertical Scrollbar Track Clicking**: Fixed clicking the vertical scrollbar track scrolling only 1 row instead of 1 page.
- **Column Visibility Width Reset**: Fixed `Auto` and `SizeToHeader` columns appearing squished (~5px) when made visible after being hidden.
- **Range Selection Shrinking**: Fixed range selection (using `Shift`+arrows or drag-select) not deselecting rows when shrinking the range.
- **Drag-Select Row Detection**: Fixed drag-select not extending across rows due to implicit pointer capture.

## Status

The `DataGrid` control was initially ported from Silverlight, and was previously part of the [Avalonia main repository](https://github.com/AvaloniaUI/Avalonia).  
It now lives in its [own repository](https://github.com/AvaloniaUI/Avalonia.Controls.DataGrid): see [this discussion](https://github.com/AvaloniaUI/Avalonia/discussions/18388) for details.
