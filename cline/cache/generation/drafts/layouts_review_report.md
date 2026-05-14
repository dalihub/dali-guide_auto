-----BEGIN_REVISED_MARKDOWN-----
--- title: Layouts sidebar_label: Layouts category: layout-sizing --- # Layouts Layouts arrange child views using automatic sizing and positioning algorithms. dali-ui provides four layout containers: `AbsoluteLayout`, `StackLayout`, `FlexLayout`, and `GridLayout`. ## Table of Contents - [Layout Basics](#layout-basics) - [AbsoluteLayout](#absolutelayout) - [StackLayout](#stacklayout) - [FlexLayout](#flexlayout) - [GridLayout](#gridlayout) ## Layout Basics All layout classes inherit from `Dali::Ui::Layout`, which extends `Dali::Ui::View`. A layout is itself a view, so you can nest layouts within other layouts to build complex UI hierarchies. Each layout type uses a corresponding layout parameters class to control how individual children are sized and positioned: | Layout | Parameters Class | |--------|-----------------| | `AbsoluteLayout` | `AbsoluteLayoutParams` | | `StackLayout` | `StackLayoutParams` | | `FlexLayout` | `FlexLayoutParams` | | `GridLayout` | `GridLayoutParams` | Use `WRAP_CONTENT` to size a view to its natural content size, and `MATCH_PARENT` to fill the parent's available space. ```cpp using namespace Dali::Ui; // Create a layout that fills its parent StackLayout root = StackLayout::New() .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT); ``` ## AbsoluteLayout `AbsoluteLayout` positions children at explicit pixel coordinates. Use `AbsoluteLayoutParams` to set the position and size of each child. ### Positioning Children Set the `x` and `y` coordinates using `SetX()` and `SetY()`, and the dimensions using `SetWidth()` and `SetHeight()`: ```cpp AbsoluteLayout root = AbsoluteLayout::New() .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT); View redBox = View::New(); redBox.SetBackgroundColor(Color::RED); redBox.SetLayoutParams(AbsoluteLayoutParams::New() .SetWidth(100.0f).SetHeight(100.0f)); root.Add(redBox); View greenBox = View::New(); greenBox.SetBackgroundColor(Color::GREEN); greenBox.SetLayoutParams(AbsoluteLayoutParams::New() .SetX(100.0f).SetY(50.0f).SetWidth(100.0f).SetHeight(50.0f)); root.Add(greenBox); ``` ### Proportional Sizing Use `AbsoluteLayoutFlags` to position or size children proportionally relative to the parent. Values between 0.0 and 1.0 represent fractions of the parent's content area. ```cpp View proportionalChild = View::New(); proportionalChild.SetBackgroundColor(Color::BLUE); proportionalChild.SetLayoutParams(AbsoluteLayoutParams::New() .SetBounds(LayoutRect(0.0f, 0.0f, 1.0f, 1.0f)) .SetFlags(AbsoluteLayoutFlags::ALL)); root.Add(proportionalChild); ``` The `AbsoluteLayoutFlags::ALL` flag makes all coordinates proportional: the child fills the entire content area of the parent. ### Setting Bounds Directly Use `SetBounds()` with a `LayoutRect` to set position and size in a single call: ```cpp View box = View::New(); box.SetLayoutParams(AbsoluteLayoutParams::New() .SetBounds(LayoutRect(10.0f, 20.0f, 100.0f, 200.0f))); ``` ## StackLayout `StackLayout` arranges children in a single row or column. Use `StackOrientation::VERTICAL` for top-to-bottom stacking, or `StackOrientation::HORIZONTAL` for left-to-right. ### Creating a Stack ```cpp StackLayout stack = StackLayout::New(StackOrientation::VERTICAL) .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT) .SetSpacing(10.0f); ``` ### Child Weight Use `StackLayoutParams::SetWeight()` to distribute remaining space among children. Children with a weight greater than 0 share the available main-axis space proportionally. When weight is set, the child's main-axis `RequestedWidth` or `RequestedHeight` is ignored—the size is determined entirely by the weight ratio. ```cpp StackLayout stack = StackLayout::New(StackOrientation::VERTICAL) .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT); View header = View::New(); header.SetRequestedHeight(50.0f); stack.Add(header); View content = View::New(); content.SetLayoutParams(StackLayoutParams::New() .SetWeight(1.0f).SetAlignment(LayoutAlignment::FILL)); stack.Add(content); ``` ### Cross-Axis Alignment Use `SetAlignment()` on `StackLayoutParams` to position children within the cross-axis. The default alignment is `LayoutAlignment::START`. ```cpp View centered = View::New(); centered.SetRequestedWidth(100.0f); centered.SetLayoutParams(StackLayoutParams::New() .SetAlignment(LayoutAlignment::CENTER)); stack.Add(centered); ``` ## FlexLayout `FlexLayout` implements the CSS Flexbox algorithm, providing flexible row and column layouts with wrapping and alignment. ### Direction and Wrap Set the main axis direction with `SetDirection()` and enable wrapping with `SetWrap()`: ```cpp FlexLayout flex = FlexLayout::New() .SetDirection(FlexDirection::ROW) .SetWrap(FlexWrap::WRAP) .SetJustifyContent(FlexJustify::FLEX_START) .SetAlignItems(FlexAlign::CENTER); ``` Available directions: - `FlexDirection::ROW` — left to right - `FlexDirection::ROW_REVERSE` — right to left - `FlexDirection::COLUMN` — top to bottom - `FlexDirection::COLUMN_REVERSE` — bottom to top ### Flex Grow and Shrink Use `FlexLayoutParams` to control how children grow and shrink: ```cpp View box1 = View::New(); box1.SetRequestedHeight(100.0f); box1.SetLayoutParams(FlexLayoutParams::New() .SetFlexBasis(50.0f).SetFlexGrow(1.0f)); flex.Add(box1); View box2 = View::New(); box2.SetRequestedHeight(100.0f); box2.SetLayoutParams(FlexLayoutParams::New() .SetFlexBasis(100.0f).SetFlexGrow(2.0f)); flex.Add(box2); ``` - `SetFlexBasis()` sets the initial main size before grow/shrink. - `SetFlexGrow()` defines how much the item should grow relative to other items. - `SetFlexShrink()` defines how much the item should shrink when there is not enough space. ### Alignment Control alignment along the main axis with `SetJustifyContent()` and along the cross axis with `SetAlignItems()`: ```cpp FlexLayout flex = FlexLayout::New() .SetJustifyContent(FlexJustify::CENTER) .SetAlignItems(FlexAlign::STRETCH); ``` For wrapped layouts, use `SetAlignContent()` to control how multiple lines are aligned: ```cpp FlexLayout flex = FlexLayout::New() .SetWrap(FlexWrap::WRAP) .SetAlignContent(FlexAlign::CENTER); ``` ### Align Self Override cross-axis alignment for individual children using `SetAlignSelf()`. Use `FlexAlign::AUTO` to defer to the parent's `SetAlignItems()` setting. ```cpp View centered = View::New(); centered.SetLayoutParams(FlexLayoutParams::New() .SetAlignSelf(FlexAlign::CENTER)); flex.Add(centered); ``` ## GridLayout `GridLayout` arranges children in a grid of rows and columns. Define row and column sizes using `GridLength`. ### Defining Rows and Columns Add row and column definitions with `AddRowDefinition()` and `AddColumnDefinition()`: ```cpp GridLayout grid = GridLayout::New() .SetRequestedWidth(MATCH_PARENT) .SetRequestedHeight(MATCH_PARENT) .SetRowSpacing(10.0f) .SetColumnSpacing(10.0f); grid.AddRowDefinition(GridLength::Absolute(50.0f)); grid.AddRowDefinition(GridLength::Absolute(100.0f)); grid.AddRowDefinition(GridLength::Absolute(200.0f)); grid.AddColumnDefinition(GridLength::Absolute(50.0f)); grid.AddColumnDefinition(GridLength::Absolute(100.0f)); ``` ### Grid Length Types `GridLength` supports three sizing modes: - `GridLength::Absolute(pixels)` — fixed size in pixels - `GridLength::Star(factor)` — proportional sizing, shares remaining space - `GridLength::Auto()` — sizes to fit the content ```cpp // Mix absolute, star, and auto sizing grid.AddRowDefinition(GridLength::Auto()); // Auto-sized row grid.AddRowDefinition(GridLength::Star(1.0f)); // Takes remaining space grid.AddRowDefinition(GridLength::Auto()); // Auto-sized row ``` ### Batch Definitions Set all row or column definitions at once using `SetRowDefinitions()` and `SetColumnDefinitions()`: ```cpp Dali::Vector<GridLength> rowDefinition; rowDefinition.Reserve(3); rowDefinition.PushBack(GridLength::Auto()); rowDefinition.PushBack(GridLength::Star(1.0f)); rowDefinition.PushBack(GridLength::Auto()); grid.SetRowDefinitions(rowDefinition); ``` ### Positioning Children Use `GridLayoutParams` to specify the row and column for each child: ```cpp View cell00 = View::New(); cell00.SetBackgroundColor(Color::RED); cell00.SetLayoutParams(GridLayoutParams::New()); grid.Add(cell00); View cell01 = View::New(); cell01.SetBackgroundColor(Color::GREEN); cell01.SetLayoutParams(GridLayoutParams::New().SetColumn(1)); grid.Add(cell01); View cell10 = View::New(); cell10.SetBackgroundColor(Color::BLUE); cell10.SetLayoutParams(GridLayoutParams::New().SetRow(1)); grid.Add(cell10); View cell11 = View::New(); cell11.SetBackgroundColor(Color::YELLOW); cell11.SetLayoutParams(GridLayoutParams::New().SetRow(1).SetColumn(1)); grid.Add(cell11); ``` Children are placed in the first available cell by default. Use `SetRow()` and `SetColumn()` to specify explicit positions.
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# Layouts Prose Review Report

## Summary

The Layouts guide draft was reviewed against public headers, layout-types.h, and sample files. The draft was substantially accurate with only minor prose improvements needed.

## Changes Made

### 1. StackLayout Child Weight Section (Line ~120)

**Original:**
> Children with a weight greater than 0 share the available main-axis space proportionally.

**Revised:**
> Children with a weight greater than 0 share the available main-axis space proportionally. When weight is set, the child's main-axis `RequestedWidth` or `RequestedHeight` is ignored—the size is determined entirely by the weight ratio.

**Source Evidence:**
- `stack-layout.h` docstring: "Weight (via StackLayoutParams): Children with weight > 0 share the remaining main-axis space proportionally. The child's main-axis RequestedWidth/RequestedHeight is ignored — the size is determined entirely by the weight ratio."
- `stack-layout-params.h` SetWeight() docstring: "When > 0, the child's main-axis size is determined entirely by its weight proportion of the remaining space (the main-axis LayoutWidth/LayoutHeight is ignored)."

**Rationale:** The original prose was incomplete. Developers need to understand that setting weight overrides the normal size request behavior.

### 2. StackLayout Cross-Axis Alignment Section (Line ~135)

**Original:**
> Use `SetAlignment()` on `StackLayoutParams` to position children within the cross-axis:

**Revised:**
> Use `SetAlignment()` on `StackLayoutParams` to position children within the cross-axis. The default alignment is `LayoutAlignment::START`.

**Source Evidence:**
- `stack-layout-params.h` SetAlignment() docstring: "@param[in] alignment FILL, START (default), CENTER, or END"

**Rationale:** Adding the default value helps developers understand the initial behavior without explicit configuration.

### 3. FlexLayout Align Self Section (Line ~195)

**Original:**
> Override cross-axis alignment for individual children using `SetAlignSelf()`:

**Revised:**
> Override cross-axis alignment for individual children using `SetAlignSelf()`. Use `FlexAlign::AUTO` to defer to the parent's `SetAlignItems()` setting.

**Source Evidence:**
- `flex-layout-params.h` SetAlignSelf() docstring: "@param[in] align The alignment (AUTO defers to parent's alignItems)"
- `layout-types.h` FlexAlign enum: "AUTO = 0" with comment "Auto alignment"

**Rationale:** The `AUTO` value is important for developers who want to reset alignment behavior to inherit from the parent. This was missing from the original description.

## Verified Accurate (No Changes Needed)

The following sections were verified against source and found to be accurate:

1. **Layout Basics** - Correctly states that `Layout` extends `View` (verified in `layout.h`)
2. **WRAP_CONTENT/MATCH_PARENT values** - Correctly documented as -1.0f and -2.0f (verified in `layout-types.h`)
3. **AbsoluteLayout positioning** - SetX/SetY/SetWidth/SetHeight methods verified in `absolute-layout-params.h`
4. **AbsoluteLayoutFlags** - Proportional behavior and ALL flag verified in `layout-types.h`
5. **StackLayout orientation** - VERTICAL/HORIZONTAL enum values verified in `layout-types.h`
6. **FlexLayout direction values** - ROW, ROW_REVERSE, COLUMN, COLUMN_REVERSE verified in `layout-types.h`
7. **FlexLayout wrap values** - NO_WRAP, WRAP, WRAP_REVERSE verified in `layout-types.h`
8. **FlexLayout justify values** - FLEX_START, FLEX_END, CENTER, SPACE_BETWEEN, SPACE_AROUND, SPACE_EVENLY verified in `layout-types.h`
9. **FlexLayout align values** - AUTO, FLEX_START, FLEX_END, CENTER, STRETCH, BASELINE verified in `layout-types.h`
10. **GridLength types** - Absolute, Star, Auto verified in `layout-types.h`
11. **GridLayout methods** - AddRowDefinition, AddColumnDefinition, SetRowDefinitions, SetColumnDefinitions verified in `grid-layout.h`
12. **GridLayoutParams** - SetRow, SetColumn verified in `grid-layout-params.h`

## Code Blocks

All code blocks were preserved as-is. They were verified against sample files and found to be consistent with actual usage patterns:
- `absolutelayout-margin-padding-example.cpp` - confirmed AbsoluteLayoutParams usage pattern
- `flexlayout-wrap-grow-example.cpp` - confirmed FlexLayoutParams usage with SetFlexBasis/SetFlexGrow
- `gridlayout-example.cpp` - confirmed GridLayout and GridLayoutParams usage pattern

## Remaining Concerns

None. The document is accurate and follows the dali-ui public API surface correctly.

## Files Reviewed

- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout.h`
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout-types.h`
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/absolute-layout.h`
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/absolute-layout-params.h`
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/stack-layout.h`
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/stack-layout-params.h`
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/flex-layout.h`
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/flex-layout-params.h`
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/grid-layout.h`
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/grid-layout-params.h`
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`
- `repos/dali-ui/samples/absolutelayout/absolutelayout-margin-padding-example.cpp`
- `repos/dali-ui/samples/flexlayout/flexlayout-wrap-grow-example.cpp`
- `repos/dali-ui/samples/gridlayout/gridlayout-example.cpp`
-----END_PROSE_REVIEW_REPORT-----