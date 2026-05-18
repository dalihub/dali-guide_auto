---
id: layouts
title: "layouts"
sidebar_label: "layouts"
---
## Overview

The DALi Layouts framework provides a declarative system for arranging UI components within a View hierarchy, enabling fluid, responsive, and platform-adaptive interfaces. By offloading coordinate calculations to dedicated layout engines, it ensures consistent UI behavior across varying display resolutions and aspect ratios.

## Applying Layouts to Views

Learn the fundamental pattern of assigning layout logic to a parent container using the `SetLayoutParams(LayoutParams)` to control child positioning. A parent container organizes its contents by evaluating the specific parameters assigned to each child, which must match the layout type currently active on the parent.

```cpp
View parent = View::New();
View child = View::New();
parent.Add(child);

FlexLayoutParams params = FlexLayoutParams::New();
params.SetFlexGrow(1.0f);
child.SetLayoutParams(params);
```

> Note: Layout parameters are strictly bound to their corresponding layout type; using parameters of an incompatible type will prevent proper arrangement of the child.

## Flexible Alignment and Sizing

Configure responsive interfaces using `FlexLayout` and `FlexLayoutParams` to handle content distribution, wrapping, and cross-axis alignment. These tools implement a robust system based on the CSS Flexbox algorithm, allowing developers to define how space is distributed among children through growth, shrinkage, and basis properties.

### Controlling Flex Behavior

The layout container itself determines the primary axis and wrapping behavior. You can specify the direction of flow using `SetDirection(FlexDirection)` or toggle wrapping capability with `SetWrap(FlexWrap)`. The overall alignment of content along the main axis is managed by `SetJustifyContent(FlexJustify)`, while cross-axis distribution is controlled by `SetAlignItems(FlexAlign)` and `SetAlignContent(FlexAlign)`.

### Defining Child Properties

Individual children can influence their own size and alignment relative to the flex container. The `SetFlexGrow(float)` allows a child to occupy extra space, whereas `SetFlexShrink(float)` defines how a child reduces in size when space is restricted. The `SetFlexBasis(float)` sets the initial size of the child before any flex factors are applied, and `SetAlignSelf(FlexAlign)` provides an override for cross-axis alignment specific to that individual child.

```cpp
FlexLayout layout = FlexLayout::New();
layout.SetDirection(FlexDirection::ROW);
layout.SetWrap(FlexWrap::WRAP);

View item = View::New();
FlexLayoutParams params = FlexLayoutParams::New();
params.SetFlexGrow(1.0f);
params.SetFlexShrink(0.0f);
item.SetLayoutParams(params);
```

## Grid-Based Arrangements

Organize complex user interfaces into rows and columns using `GridLayout`, with specific control over cell spanning and spacing via `GridLayoutParams`. The grid system uses row and column definitions, which support both fixed pixel sizes and proportional star-based distribution.

### Configuring Grid Structure

You can explicitly add structural definitions using `AddRowDefinition(GridLength)` and `AddColumnDefinition(GridLength)`, providing a `GridLength` to specify the sizing strategy. For complete updates, use `SetRowDefinitions(Vector<GridLength>)` or `SetColumnDefinitions(Vector<GridLength>)`. The space between grid cells is defined globally for the container using `SetRowSpacing(float)` and `SetColumnSpacing(float)`.

### Managing Child Placement

Each child in the grid must be assigned a specific location and extent. Use `SetRow(uint32_t)` and `SetColumn(uint32_t)` to designate the starting cell, and adjust the span across adjacent cells using `SetRowSpan(uint32_t)` and `SetColumnSpan(uint32_t)`. Alignment within a specific grid cell is achieved through `SetHorizontalAlignment(LayoutAlignment)` and `SetVerticalAlignment(LayoutAlignment)`.

```cpp
GridLayout layout = GridLayout::New();
layout.AddRowDefinition(GridLength::Star(1.0f));
layout.AddColumnDefinition(GridLength::Absolute(100.0f));

View cell = View::New();
GridLayoutParams params = GridLayoutParams::New();
params.SetRow(0);
params.SetColumn(0);
params.SetColumnSpan(2);
cell.SetLayoutParams(params);
```

## Stacking and Layering

Use `StackLayout` and `StackLayoutParams` to overlay UI elements or organize them in simple linear stacks with specific gravity alignment. This layout is ideal for straightforward vertical or horizontal successions of UI components.

### Orienting the Stack

The orientation of the stack is determined by the `SetOrientation(StackOrientation)`, which supports either horizontal or vertical arrangements. You can adjust the gap between items with `SetSpacing(float)`.

### Individual Item Control

Each child's proportional weight within the stack is determined by `SetWeight(float)`, allowing items to claim varying amounts of available main-axis space. Cross-axis alignment for individual items is managed using the `SetAlignment(LayoutAlignment)`.

```cpp
StackLayout layout = StackLayout::New(StackOrientation::VERTICAL);
layout.SetSpacing(10.0f);

View item = View::New();
StackLayoutParams params = StackLayoutParams::New();
params.SetWeight(1.0f);
params.SetAlignment(LayoutAlignment::CENTER);
item.SetLayoutParams(params);
```

## Precise Coordinate Positioning

Implement static UI designs using `AbsoluteLayout` to define exact bounds and dimensions for children when fixed placement is required. This system provides the highest level of control over pixel-perfect layouts, though it does not automatically adapt to container resizing unless flags are used.

### Absolute Parameters

Positions are defined via `SetX(float)` and `SetY(float)`, while dimensions are managed through `SetWidth(float)` and `SetHeight(float)`. For convenience, you can update all positional and dimensional data simultaneously using the `SetBounds(LayoutRect)` with a `LayoutRect`. 

### Proportional Scaling

When relative sizing is necessary, you can enable proportional behavior using the `SetFlags(AbsoluteLayoutFlags)`. The `AbsoluteLayoutFlags` provides granular control over which axes remain proportional to the parent’s dimensions.

```cpp
AbsoluteLayout layout = AbsoluteLayout::New();
View child = View::New();
AbsoluteLayoutParams params = AbsoluteLayoutParams::New();
params.SetBounds(LayoutRect(10.0f, 10.0f, 100.0f, 50.0f));
child.SetLayoutParams(params);
```

## Dynamic Sizing and Measurement

Understand how `MeasuredSize` and `LayoutRect` interact to calculate the final rendering dimensions of your components during the layout pass. These classes serve as the bridge between logical layout requirements and the final screen state.

The system processes layout requests via the `LayoutController`. For custom UI requirements, you can invoke `RequestLayout(ViewImpl*)` to ensure that pending changes are calculated, or call `ProcessLayouts()` to force an immediate update of the UI hierarchy. 

```cpp
// Assuming MyView inherits from View and implements ViewImpl
LayoutController controller = LayoutController::Get(window);
controller.RequestLayout(GetImpl(myViewInstance));
controller.ProcessLayouts();
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/layouts)
