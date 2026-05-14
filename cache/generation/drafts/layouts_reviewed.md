-----BEGIN_REVISED_MARKDOWN-----
--- title: Layouts sidebar_label: Layouts category: layout-sizing --- # Layouts Layouts arrange child views within a container using different algorithms. dali-ui provides four layout types: `AbsoluteLayout`, `StackLayout`, `FlexLayout`, and `GridLayout`. ## Table of Contents - [Creating and Applying Layouts](#creating-and-applying-layouts) - [AbsoluteLayout](#absolutelayout) - [StackLayout](#stacklayout) - [FlexLayout](#flexlayout) - [GridLayout](#gridlayout) - [Common Size Constants](#common-size-constants) ## Creating and Applying Layouts All layout types inherit from `Dali::Ui::Layout` and follow the same pattern: create the layout, configure its properties, and add child views. ```cpp #include <dali-ui-foundation/dali-ui-foundation.h> using namespace Dali; using namespace Dali::Ui; // Create a layout StackLayout root = StackLayout::New(StackOrientation::VERTICAL) .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT) .SetPadding(Extents(50, 50, 50, 50)); // Add children View child = View::New(); child.SetBackgroundColor(Color::RED); child.SetRequestedWidth(100.0f); child.SetRequestedHeight(50.0f); root.Add(child); // Add layout to window window.Add(root); ``` Each child view can have layout-specific parameters attached via `View::SetLayoutParams()`. The parameters control how the child is positioned and sized within its parent layout. ## AbsoluteLayout `AbsoluteLayout` positions children at explicit coordinates. Use `AbsoluteLayoutParams` to specify the position and size of each child. ### Creating an AbsoluteLayout ```cpp AbsoluteLayout layout = AbsoluteLayout::New() .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT); ``` ### Positioning Children with AbsoluteLayoutParams `AbsoluteLayoutParams` provides methods to set the position and size of a child view. ```cpp // Position a child at (10, 20) with size 100x50 View child = View::New(); child.SetBackgroundColor(Color::RED); child.SetLayoutParams(AbsoluteLayoutParams::New() .SetX(10.0f) .SetY(20.0f) .SetWidth(100.0f) .SetHeight(50.0f)); layout.Add(child); ``` You can also set all bounds at once using `SetBounds()` with a `LayoutRect`: ```cpp child.SetLayoutParams(AbsoluteLayoutParams::New() .SetBounds(LayoutRect(10.0f, 20.0f, 100.0f, 50.0f))); ``` ### Proportional Positioning and Sizing `AbsoluteLayoutFlags` enables proportional positioning and sizing, where values are interpreted as fractions (0.0 to 1.0) of the parent's content area. ```cpp // Position at 50% of parent width, 25% of parent height // Size at 50% of parent width, 50% of parent height View proportional = View::New(); proportional.SetBackgroundColor(Color::BLUE); proportional.SetLayoutParams(AbsoluteLayoutParams::New() .SetBounds(LayoutRect(0.5f, 0.25f, 0.5f, 0.5f)) .SetFlags(AbsoluteLayoutFlags::ALL)); layout.Add(proportional); ``` Available flags: | Flag | Description | |------|-------------| | `AbsoluteLayoutFlags::NONE` | All values are absolute pixels | | `AbsoluteLayoutFlags::X_PROPORTIONAL` | X is proportional to parent width | | `AbsoluteLayoutFlags::Y_PROPORTIONAL` | Y is proportional to parent height | | `AbsoluteLayoutFlags::WIDTH_PROPORTIONAL` | Width is proportional to parent width | | `AbsoluteLayoutFlags::HEIGHT_PROPORTIONAL` | Height is proportional to parent height | | `AbsoluteLayoutFlags::POSITION_PROPORTIONAL` | Both X and Y are proportional | | `AbsoluteLayoutFlags::SIZE_PROPORTIONAL` | Both width and height are proportional | | `AbsoluteLayoutFlags::ALL` | All values are proportional | ### Nested AbsoluteLayouts AbsoluteLayout can be nested to create complex layouts with independent padding regions. ```cpp // Outer layout with padding AbsoluteLayout root = AbsoluteLayout::New() .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT) .SetPadding(Extents(50, 50, 50, 50)); // Nested layout with its own padding AbsoluteLayout nested = AbsoluteLayout::New() .SetBackgroundColor(Color::GRAY) .SetPadding(Extents(50, 50, 50, 50)) .SetLayoutParams(AbsoluteLayoutParams::New() .SetY(300.0f) .SetWidth(200.0f) .SetHeight(200.0f)); // Child inside nested layout fills it proportionally View inner = View::New(); inner.SetBackgroundColor(Color::MAGENTA); inner.SetLayoutParams(AbsoluteLayoutParams::New() .SetBounds(LayoutRect(0.0f, 0.0f, 1.0f, 1.0f)) .SetFlags(AbsoluteLayoutFlags::ALL)); nested.Add(inner); root.Add(nested); ``` ## StackLayout `StackLayout` arranges children in a single row or column with optional spacing between them. ### Creating a StackLayout ```cpp // Vertical stack StackLayout vertical = StackLayout::New(StackOrientation::VERTICAL) .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT) .SetSpacing(10.0f); // Horizontal stack StackLayout horizontal = StackLayout::New(StackOrientation::HORIZONTAL) .SetSpacing(20.0f); ``` ### Configuring Spacing Use `SetSpacing()` to add space between children: ```cpp StackLayout stack = StackLayout::New(StackOrientation::VERTICAL) .SetSpacing(50.0f) .SetPadding(Extents(50, 50, 50, 50)); ``` ### Child Weight and Alignment `StackLayoutParams` controls child weight (for distributing remaining space) and alignment within the cross-axis. ```cpp // Child with weight fills remaining space View expanding = View::New(); expanding.SetBackgroundColor(Color::GREEN); expanding.SetLayoutParams(StackLayoutParams::New() .SetWeight(1.0f) .SetAlignment(LayoutAlignment::FILL)); stack.Add(expanding); ``` ## FlexLayout `FlexLayout` implements the CSS Flexbox algorithm, providing flexible arrangement of children in rows or columns with support for wrapping, alignment, and flexible sizing. ### Creating a FlexLayout ```cpp FlexLayout flex = FlexLayout::New() .SetDirection(FlexDirection::ROW) .SetJustifyContent(FlexJustify::FLEX_START) .SetAlignItems(FlexAlign::CENTER); ``` ### Direction and Wrap Control the main axis direction and whether items wrap to the next line: ```cpp // Row with wrapping FlexLayout row = FlexLayout::New() .SetDirection(FlexDirection::ROW) .SetWrap(FlexWrap::WRAP); // Column in reverse order (bottom to top) FlexLayout columnReverse = FlexLayout::New() .SetDirection(FlexDirection::COLUMN_REVERSE); ``` Available directions: | Value | Description | |-------|-------------| | `FlexDirection::ROW` | Left to right | | `FlexDirection::ROW_REVERSE` | Right to left | | `FlexDirection::COLUMN` | Top to bottom | | `FlexDirection::COLUMN_REVERSE` | Bottom to top | Available wrap modes: | Value | Description | |-------|-------------| | `FlexWrap::NO_WRAP` | Items stay on one line | | `FlexWrap::WRAP` | Items wrap to next line | | `FlexWrap::WRAP_REVERSE` | Items wrap in reverse order | ### Alignment Control how items are aligned along the main axis (`SetJustifyContent`) and cross axis (`SetAlignItems`): ```cpp FlexLayout flex = FlexLayout::New() .SetDirection(FlexDirection::ROW) .SetJustifyContent(FlexJustify::CENTER) // Center on main axis .SetAlignItems(FlexAlign::CENTER) // Center on cross axis .SetAlignContent(FlexAlign::SPACE_AROUND); // Spacing between wrapped lines ``` Justify content options: | Value | Description | |-------|-------------| | `FlexJustify::FLEX_START` | Pack to start | | `FlexJustify::FLEX_END` | Pack to end | | `FlexJustify::CENTER` | Center items | | `FlexJustify::SPACE_BETWEEN` | Even space between items | | `FlexJustify::SPACE_AROUND` | Space around items | | `FlexJustify::SPACE_EVENLY` | Equal space around all items | Align items options: | Value | Description | |-------|-------------| | `FlexAlign::AUTO` | Inherit from parent | | `FlexAlign::FLEX_START` | Align to start | | `FlexAlign::FLEX_END` | Align to end | | `FlexAlign::CENTER` | Center alignment | | `FlexAlign::STRETCH` | Stretch to fill | | `FlexAlign::BASELINE` | Baseline alignment | ### Flex Item Properties Use `FlexLayoutParams` to control individual child behavior: ```cpp // Item that grows to fill available space View growing = View::New(); growing.SetBackgroundColor(Color::RED); growing.SetRequestedHeight(100.0f); growing.SetLayoutParams(FlexLayoutParams::New() .SetFlexBasis(50.0f) .SetFlexGrow(1.0f)); flex.Add(growing); // Item that grows twice as much View growingMore = View::New(); growingMore.SetBackgroundColor(Color::BLUE); growingMore.SetRequestedHeight(100.0f); growingMore.SetLayoutParams(FlexLayoutParams::New() .SetFlexBasis(100.0f) .SetFlexGrow(2.0f)); flex.Add(growingMore); ``` - `SetFlexBasis()`: Initial size before grow/shrink - `SetFlexGrow()`: How much the item should grow (0 = no growing) - `SetFlexShrink()`: How much the item should shrink if needed - `SetAlignSelf()`: Override parent's `AlignItems` for this child ### FlexLayout with Wrapping When wrapping is enabled, items flow to the next line when there is not enough space: ```cpp FlexLayout flex = FlexLayout::New() .SetDirection(FlexDirection::ROW) .SetWrap(FlexWrap::WRAP) .SetJustifyContent(FlexJustify::FLEX_START) .SetAlignContent(FlexAlign::CENTER); // Add multiple items that will wrap for (int i = 0; i < 6; ++i) { View item = View::New(); item.SetBackgroundColor(colors[i]); item.SetRequestedHeight(100.0f); item.SetLayoutParams(FlexLayoutParams::New() .SetFlexBasis(100.0f) .SetFlexGrow(1.0f)); flex.Add(item); } ``` ## GridLayout `GridLayout` arranges children in a grid of rows and columns with support for absolute, proportional (star), and auto sizing. ### Creating a GridLayout ```cpp GridLayout grid = GridLayout::New() .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT) .SetRowSpacing(10.0f) .SetColumnSpacing(10.0f); ``` ### Defining Rows and Columns Use `AddRowDefinition()` and `AddColumnDefinition()` with `GridLength` values: ```cpp // 3 rows with fixed heights grid.AddRowDefinition(GridLength::Absolute(50.0f)); grid.AddRowDefinition(GridLength::Absolute(100.0f)); grid.AddRowDefinition(GridLength::Absolute(200.0f)); // 2 columns with fixed widths grid.AddColumnDefinition(GridLength::Absolute(50.0f)); grid.AddColumnDefinition(GridLength::Absolute(100.0f)); ``` Alternatively, set all definitions at once: ```cpp Dali::Vector<GridLength> rowDefs; rowDefs.Reserve(3); rowDefs.PushBack(GridLength::Auto()); rowDefs.PushBack(GridLength::Star(1.0f)); rowDefs.PushBack(GridLength::Auto()); grid.SetRowDefinitions(rowDefs); Dali::Vector<GridLength> colDefs; colDefs.Reserve(2); colDefs.PushBack(GridLength::Auto()); colDefs.PushBack(GridLength::Star(1.0f)); grid.SetColumnDefinitions(colDefs); ``` ### GridLength Types | Type | Description | |------|-------------| | `GridLength::Absolute(pixels)` | Fixed size in pixels | | `GridLength::Star(factor)` | Proportional size, shares remaining space | | `GridLength::Auto()` | Sizes to fit content | ### Positioning Children Use `GridLayoutParams` to specify the row and column for each child: ```cpp // Cell (0, 0) View cell00 = View::New(); cell00.SetBackgroundColor(Color::RED); cell00.SetLayoutParams(GridLayoutParams::New()); grid.Add(cell00); // Cell (1, 1) View cell11 = View::New(); cell11.SetBackgroundColor(Color::GREEN); cell11.SetLayoutParams(GridLayoutParams::New() .SetRow(1) .SetColumn(1)); grid.Add(cell11); // Cell (2, 0) View cell20 = View::New(); cell20.SetBackgroundColor(Color::BLUE); cell20.SetLayoutParams(GridLayoutParams::New() .SetRow(2)); grid.Add(cell20); ``` ### Cell Alignment Control how children are aligned within their cells: ```cpp View centered = View::New(); centered.SetLayoutParams(GridLayoutParams::New() .SetRow(1) .SetColumn(1) .SetHorizontalAlignment(LayoutAlignment::CENTER) .SetVerticalAlignment(LayoutAlignment::CENTER)); grid.Add(centered); ``` Alignment options: | Value | Description | |-------|-------------| | `LayoutAlignment::FILL` | Stretch to fill cell (default) | | `LayoutAlignment::START` | Align to start of cell | | `LayoutAlignment::CENTER` | Center within cell | | `LayoutAlignment::END` | Align to end of cell | ### Auto-Sizing Grid Example Mix `Auto`, `Star`, and `Absolute` sizing for responsive layouts: ```cpp GridLayout grid = GridLayout::New() .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT) .SetRowSpacing(10.0f) .SetColumnSpacing(10.0f); // Row 0: Auto-sized header // Row 1: Fills remaining space // Row 2: Auto-sized footer grid.AddRowDefinition(GridLength::Auto()); grid.AddRowDefinition(GridLength::Star(1.0f)); grid.AddRowDefinition(GridLength::Auto()); // Column 0: Auto-sized label column // Column 1: Fills remaining space grid.AddColumnDefinition(GridLength::Auto()); grid.AddColumnDefinition(GridLength::Star(1.0f)); // Header in the content column View header = View::New(); header.SetBackgroundColor(Color::RED); header.SetRequestedHeight(50.0f); header.SetLayoutParams(GridLayoutParams::New() .SetColumn(1)); grid.Add(header); // Main content area View content = View::New(); content.SetBackgroundColor(Color::BLUE); content.SetLayoutParams(GridLayoutParams::New() .SetRow(1) .SetColumn(1)); grid.Add(content); ``` ## Common Size Constants All layouts support these size constants for `RequestedWidth` and `RequestedHeight`: | Constant | Value | Description | |----------|-------|-------------| | `WRAP_CONTENT` | -1.0f | Size to fit content | | `MATCH_PARENT` | -2.0f | Fill parent's available space | ```cpp View child = View::New(); child.SetRequestedWidth(WRAP_CONTENT); // Size to content width child.SetRequestedHeight(MATCH_PARENT); // Fill parent height ```
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# Layouts Prose Review

## Summary

The Layouts guide draft is well-written and accurate. The API usage matches the public headers, and the code examples follow proper dali-ui idioms using `Dali::Ui::View`, typed setters, and fluent chaining.

## Changes Made

### 1. GridLayout Auto-Sizing Example Comment Correction

**Location:** GridLayout > Auto-Sizing Grid Example

**Original:**
```cpp
// Header spans both columns
View header = View::New();
header.SetBackgroundColor(Color::RED);
header.SetRequestedHeight(50.0f);
header.SetLayoutParams(GridLayoutParams::New()
  .SetColumn(1));
grid.Add(header);
```

**Revised:**
```cpp
// Header in the content column
View header = View::New();
header.SetBackgroundColor(Color::RED);
header.SetRequestedHeight(50.0f);
header.SetLayoutParams(GridLayoutParams::New()
  .SetColumn(1));
grid.Add(header);
```

**Reason:** The original comment "Header spans both columns" was incorrect. The code places the header in column 1 only (the content column), not spanning both columns. To span columns, the code would need `.SetColumnSpan(2)`. The comment was corrected to match the actual code behavior.

**Source Evidence:**
- `GridLayoutParams::SetColumn()` places a child in a specific column (grid-layout-params.h:62-68)
- `GridLayoutParams::SetColumnSpan()` is required to span multiple columns (grid-layout-params.h:86-93)

## Verification Summary

All API references were verified against public headers:

| API | Header | Status |
|-----|--------|--------|
| `Layout` base class | layout.h | ✓ Accurate |
| `AbsoluteLayout::New()` | absolute-layout.h | ✓ Accurate |
| `AbsoluteLayoutParams::New()` | absolute-layout-params.h | ✓ Accurate |
| `AbsoluteLayoutParams::SetBounds()` | absolute-layout-params.h | ✓ Accurate |
| `AbsoluteLayoutParams::SetFlags()` | absolute-layout-params.h | ✓ Accurate |
| `AbsoluteLayoutFlags` enum | layout-types.h | ✓ Accurate |
| `StackLayout::New()` | stack-layout.h | ✓ Accurate |
| `StackLayout::SetSpacing()` | stack-layout.h | ✓ Accurate |
| `StackLayoutParams::SetWeight()` | stack-layout-params.h | ✓ Accurate |
| `StackLayoutParams::SetAlignment()` | stack-layout-params.h | ✓ Accurate |
| `FlexLayout::New()` | flex-layout.h | ✓ Accurate |
| `FlexLayout::SetDirection()` | flex-layout.h | ✓ Accurate |
| `FlexLayout::SetWrap()` | flex-layout.h | ✓ Accurate |
| `FlexLayout::SetJustifyContent()` | flex-layout.h | ✓ Accurate |
| `FlexLayout::SetAlignItems()` | flex-layout.h | ✓ Accurate |
| `FlexLayout::SetAlignContent()` | flex-layout.h | ✓ Accurate |
| `FlexLayoutParams::SetFlexGrow()` | flex-layout-params.h | ✓ Accurate |
| `FlexLayoutParams::SetFlexShrink()` | flex-layout-params.h | ✓ Accurate |
| `FlexLayoutParams::SetFlexBasis()` | flex-layout-params.h | ✓ Accurate |
| `FlexLayoutParams::SetAlignSelf()` | flex-layout-params.h | ✓ Accurate |
| `GridLayout::New()` | grid-layout.h | ✓ Accurate |
| `GridLayout::AddRowDefinition()` | grid-layout.h | ✓ Accurate |
| `GridLayout::AddColumnDefinition()` | grid-layout.h | ✓ Accurate |
| `GridLayout::SetRowDefinitions()` | grid-layout.h | ✓ Accurate |
| `GridLayout::SetColumnDefinitions()` | grid-layout.h | ✓ Accurate |
| `GridLayoutParams::SetRow()` | grid-layout-params.h | ✓ Accurate |
| `GridLayoutParams::SetColumn()` | grid-layout-params.h | ✓ Accurate |
| `GridLayoutParams::SetHorizontalAlignment()` | grid-layout-params.h | ✓ Accurate |
| `GridLayoutParams::SetVerticalAlignment()` | grid-layout-params.h | ✓ Accurate |
| `GridLength::Absolute()` | layout-types.h | ✓ Accurate |
| `GridLength::Star()` | layout-types.h | ✓ Accurate |
| `GridLength::Auto()` | layout-types.h | ✓ Accurate |
| `WRAP_CONTENT` | layout-types.h | ✓ Accurate (-1.0f) |
| `MATCH_PARENT` | layout-types.h | ✓ Accurate (-2.0f) |
| `LayoutAlignment` enum | layout-types.h | ✓ Accurate |
| `View::SetLayoutParams()` | view.h | ✓ Accurate |

## Remaining Concerns

None. The guide accurately describes the dali-ui Layouts API and follows the app-facing guidance requirements.
-----END_PROSE_REVIEW_REPORT-----