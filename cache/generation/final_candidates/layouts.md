---
title: Layouts
sidebar_label: Layouts
category: uncategorized
---

# Layouts

Layouts in dali-ui provide container classes that automatically measure and arrange child views. The layout system supports absolute positioning, flexible box layouts, grid arrangements, and simple stacking.

## Table of Contents

- [AbsoluteLayout](#absolutelayout)
- [FlexLayout](#flexlayout)
- [GridLayout](#gridlayout)
- [StackLayout](#stacklayout)
- [Layout Parameters](#layout-parameters)

## AbsoluteLayout

`AbsoluteLayout` positions children at explicit coordinates. Use this layout when you need precise control over child positions, such as overlaying elements or building custom UI patterns.

### Creating an AbsoluteLayout

Create an `AbsoluteLayout` using `AbsoluteLayout::New()`:

```cpp
using namespace Dali;
using namespace Dali::Ui;

AbsoluteLayout root = AbsoluteLayout::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT)
  .SetPadding(Extents(50, 50, 50, 50));
```

### Positioning Children

Children are positioned using `AbsoluteLayoutParams`. Set the x, y, width, and height values to define the child's bounds:

```cpp
View redBox = View::New();
redBox.SetBackgroundColor(Color::RED);
redBox.SetLayoutParams(AbsoluteLayoutParams::New()
  .SetWidth(100.0f)
  .SetHeight(100.0f));
root.Add(redBox);

View greenBox = View::New();
greenBox.SetBackgroundColor(Color::GREEN);
greenBox.SetLayoutParams(AbsoluteLayoutParams::New()
  .SetX(100.0f)
  .SetY(50.0f)
  .SetWidth(100.0f)
  .SetHeight(50.0f));
root.Add(greenBox);
```

### Proportional Positioning

Use `AbsoluteLayoutFlags` to position children proportionally within the parent. Values between 0.0 and 1.0 represent percentages of the parent's size:

```cpp
View overlay = View::New();
overlay.SetBackgroundColor(Color::MAGENTA);
overlay.SetLayoutParams(AbsoluteLayoutParams::New()
  .SetBounds(LayoutRect(0.0f, 0.0f, 1.0f, 1.0f))
  .SetFlags(AbsoluteLayoutFlags::ALL));
root.Add(overlay);
```

The `AbsoluteLayoutFlags` enumeration provides options for proportional positioning:

- `X_PROPORTIONAL` - X value is a proportion of parent width
- `Y_PROPORTIONAL` - Y value is a proportion of parent height
- `WIDTH_PROPORTIONAL` - Width is a proportion of parent width
- `HEIGHT_PROPORTIONAL` - Height is a proportion of parent height
- `POSITION_PROPORTIONAL` - Both X and Y are proportional
- `SIZE_PROPORTIONAL` - Both width and height are proportional
- `ALL` - All dimensions are proportional

### Setting Bounds Directly

Use `SetBounds()` with a `LayoutRect` to set all position and size values at once:

```cpp
View box = View::New();
box.SetLayoutParams(AbsoluteLayoutParams::New()
  .SetBounds(LayoutRect(10.0f, 20.0f, 100.0f, 50.0f)));
```

## FlexLayout

`FlexLayout` arranges children using the CSS Flexbox algorithm. It provides flexible sizing, wrapping, and alignment options.

### Creating a FlexLayout

Create a `FlexLayout` using `FlexLayout::New()`:

```cpp
FlexLayout flex = FlexLayout::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT)
  .SetDirection(FlexDirection::ROW)
  .SetWrap(FlexWrap::WRAP)
  .SetJustifyContent(FlexJustify::FLEX_START)
  .SetAlignItems(FlexAlign::STRETCH);
```

### Flex Direction

Control the main axis direction with `SetDirection()`:

```cpp
flex.SetDirection(FlexDirection::ROW);            // Left to right
flex.SetDirection(FlexDirection::ROW_REVERSE);    // Right to left
flex.SetDirection(FlexDirection::COLUMN);         // Top to bottom
flex.SetDirection(FlexDirection::COLUMN_REVERSE);  // Bottom to top
```

### Wrapping Behavior

Enable items to wrap to the next line when there is insufficient space:

```cpp
flex.SetWrap(FlexWrap::NO_WRAP);      // Default, items shrink to fit
flex.SetWrap(FlexWrap::WRAP);         // Items wrap to next line
flex.SetWrap(FlexWrap::WRAP_REVERSE); // Items wrap in reverse order
```

### Justify Content

Control how items are distributed along the main axis:

```cpp
flex.SetJustifyContent(FlexJustify::FLEX_START);   // Items at start
flex.SetJustifyContent(FlexJustify::FLEX_END);     // Items at end
flex.SetJustifyContent(FlexJustify::CENTER);       // Items centered
flex.SetJustifyContent(FlexJustify::SPACE_BETWEEN); // Equal space between
flex.SetJustifyContent(FlexJustify::SPACE_AROUND);  // Space around items
flex.SetJustifyContent(FlexJustify::SPACE_EVENLY);  // Equal spacing
```

### Alignment

Control cross-axis alignment with `SetAlignItems()` and multi-line alignment with `SetAlignContent()`:

```cpp
flex.SetAlignItems(FlexAlign::FLEX_START);  // Cross-axis start
flex.SetAlignItems(FlexAlign::CENTER);     // Cross-axis center
flex.SetAlignItems(FlexAlign::STRETCH);    // Stretch to fill

flex.SetAlignContent(FlexAlign::CENTER);    // Center wrapped lines
```

### Flex Item Parameters

Use `FlexLayoutParams` to control how individual children participate in flex layout:

```cpp
View box1 = View::New();
box1.SetLayoutParams(FlexLayoutParams::New()
  .SetFlexBasis(100.0f)   // Initial size before grow/shrink
  .SetFlexGrow(1.0f));    // Grow to fill remaining space
flex.Add(box1);

View box2 = View::New();
box2.SetLayoutParams(FlexLayoutParams::New()
  .SetFlexBasis(100.0f)
  .SetFlexGrow(2.0f));    // Grow twice as much as box1
flex.Add(box2);
```

### Flex Grow and Shrink

- `SetFlexGrow()` - Controls how much an item grows relative to others when there is extra space
- `SetFlexShrink()` - Controls how much an item shrinks relative to others when space is insufficient

```cpp
// Item that will not shrink
View fixedItem = View::New();
fixedItem.SetLayoutParams(FlexLayoutParams::New()
  .SetFlexBasis(250.0f)
  .SetFlexShrink(0.0f));

// Item that shrinks 3x more than normal
View shrinkableItem = View::New();
shrinkableItem.SetLayoutParams(FlexLayoutParams::New()
  .SetFlexBasis(250.0f)
  .SetFlexShrink(3.0f));
```

### Align Self

Override the container's `AlignItems` for individual children:

```cpp
View centeredItem = View::New();
centeredItem.SetLayoutParams(FlexLayoutParams::New()
  .SetAlignSelf(FlexAlign::CENTER));
```

## GridLayout

`GridLayout` arranges children in a grid of rows and columns with support for absolute, star (proportional), and auto sizing.

### Creating a GridLayout

Create a `GridLayout` using `GridLayout::New()`:

```cpp
GridLayout grid = GridLayout::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT)
  .SetRowSpacing(10.0f)
  .SetColumnSpacing(10.0f);
```

### Defining Rows and Columns

Add row and column definitions using `GridLength`:

```cpp
// Fixed-size rows
grid.AddRowDefinition(GridLength::Absolute(50.0f));
grid.AddRowDefinition(GridLength::Absolute(100.0f));
grid.AddRowDefinition(GridLength::Absolute(200.0f));

// Fixed-size columns
grid.AddColumnDefinition(GridLength::Absolute(50.0f));
grid.AddColumnDefinition(GridLength::Absolute(100.0f));
```

### GridLength Types

`GridLength` supports three sizing modes:

- `GridLength::Absolute(pixels)` - Fixed size in pixels
- `GridLength::Star(factor)` - Proportional sizing, shares available space
- `GridLength::Auto()` - Sizes to fit content

```cpp
// Auto-sized header row, content takes remaining space, auto footer
grid.AddRowDefinition(GridLength::Auto());
grid.AddRowDefinition(GridLength::Star(1.0f));
grid.AddRowDefinition(GridLength::Auto());
```

### Batch Row and Column Definitions

Set all definitions at once using vectors:

```cpp
Dali::Vector<GridLength> rowDefinition;
rowDefinition.Reserve(3);
rowDefinition.PushBack(GridLength::Auto());
rowDefinition.PushBack(GridLength::Star(1.0f));
rowDefinition.PushBack(GridLength::Auto());
grid.SetRowDefinitions(rowDefinition);

Dali::Vector<GridLength> columnDefinition;
columnDefinition.Reserve(2);
columnDefinition.PushBack(GridLength::Auto());
columnDefinition.PushBack(GridLength::Star(1.0f));
grid.SetColumnDefinitions(columnDefinition);
```

### Positioning Children in Cells

Use `GridLayoutParams` to specify row and column positions:

```cpp
View cell00 = View::New();
cell00.SetBackgroundColor(Color::RED);
cell00.SetLayoutParams(GridLayoutParams::New());  // Default: row 0, column 0
grid.Add(cell00);

View cell01 = View::New();
cell01.SetBackgroundColor(Color::GREEN);
cell01.SetLayoutParams(GridLayoutParams::New().SetColumn(1));
grid.Add(cell01);

View cell10 = View::New();
cell10.SetBackgroundColor(Color::BLUE);
cell10.SetLayoutParams(GridLayoutParams::New().SetRow(1));
grid.Add(cell10);

View cell11 = View::New();
cell11.SetBackgroundColor(Color::YELLOW);
cell11.SetLayoutParams(GridLayoutParams::New().SetRow(1).SetColumn(1));
grid.Add(cell11);
```

### Cell Span

Children can span multiple rows or columns:

```cpp
View spanningCell = View::New();
spanningCell.SetLayoutParams(GridLayoutParams::New()
  .SetRow(0)
  .SetColumn(0)
  .SetRowSpan(2)     // Span 2 rows
  .SetColumnSpan(3)); // Span 3 columns
grid.Add(spanningCell);
```

### Cell Alignment

Control how children are positioned within their cells:

```cpp
View centeredCell = View::New();
centeredCell.SetLayoutParams(GridLayoutParams::New()
  .SetHorizontalAlignment(LayoutAlignment::CENTER)
  .SetVerticalAlignment(LayoutAlignment::CENTER));
```

Alignment options include:
- `LayoutAlignment::FILL` - Stretch to fill the cell (default)
- `LayoutAlignment::START` - Align to cell start
- `LayoutAlignment::CENTER` - Center within the cell
- `LayoutAlignment::END` - Align to cell end

## StackLayout

`StackLayout` arranges children in a single row or column with optional spacing. It is the simplest layout container.

### Creating a StackLayout

Create a `StackLayout` with a specified orientation:

```cpp
StackLayout verticalStack = StackLayout::New(StackOrientation::VERTICAL)
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT)
  .SetSpacing(16.0f);

StackLayout horizontalStack = StackLayout::New(StackOrientation::HORIZONTAL)
  .SetSpacing(8.0f);
```

### Orientation

Control the stacking direction:

```cpp
stack.SetOrientation(StackOrientation::VERTICAL);   // Top to bottom
stack.SetOrientation(StackOrientation::HORIZONTAL); // Left to right
```

### Spacing

Set uniform spacing between children:

```cpp
stack.SetSpacing(10.0f);
```

### Stack Layout Parameters

Use `StackLayoutParams` to control weight and cross-axis alignment:

```cpp
View expandingChild = View::New();
expandingChild.SetLayoutParams(StackLayoutParams::New()
  .SetWeight(1.0f)                      // Fill remaining space
  .SetAlignment(LayoutAlignment::FILL)); // Fill cross-axis
stack.Add(expandingChild);
```

### Weight Distribution

Children with weight > 0 share remaining space proportionally:

```cpp
// Two children that equally share remaining space
View child1 = View::New();
child1.SetLayoutParams(StackLayoutParams::New().SetWeight(1.0f));

View child2 = View::New();
child2.SetLayoutParams(StackLayoutParams::New().SetWeight(1.0f));

// Child that takes 2/3 of remaining space
View child3 = View::New();
child3.SetLayoutParams(StackLayoutParams::New().SetWeight(2.0f));
```

## Layout Parameters

Each layout type has a corresponding params class for per-child configuration:

| Layout | Params Class | Purpose |
|--------|--------------|---------|
| `AbsoluteLayout` | `AbsoluteLayoutParams` | Position (x, y) and size (width, height) |
| `FlexLayout` | `FlexLayoutParams` | Flex grow, shrink, basis, align-self |
| `GridLayout` | `GridLayoutParams` | Row, column, row span, column span, alignment |
| `StackLayout` | `StackLayoutParams` | Weight, cross-axis alignment |

### Attaching Layout Parameters

Use `View::SetLayoutParams()` to attach parameters to a child:

```cpp
view.SetLayoutParams(FlexLayoutParams::New()
  .SetFlexGrow(1.0f)
  .SetFlexShrink(0.0f));
```

### Common Size Constants

Use these constants for `RequestedWidth` and `RequestedHeight`:

- `WRAP_CONTENT` (-1.0f) - Size to fit content
- `MATCH_PARENT` (-2.0f) - Fill parent's available space

```cpp
View child = View::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(WRAP_CONTENT);
