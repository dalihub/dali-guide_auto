---
id: layouts
title: "layouts"
sidebar_label: "layouts"
---
## Overview

The Layouts module provides a declarative system for positioning and sizing `View` components using constraint-based containers that automate complex UI orchestration. By separating layout logic from individual component properties, this module enables responsive design and simplifies the maintenance of dynamic user interfaces.

## Applying Layouts to Views

The foundation of the layout system involves assigning specific layout strategies to container views. Once a layout instance is created, it is applied to a view, which then governs the geometric arrangement of all its child elements.

To apply a layout strategy to a specific view, developers use the `SetLayoutParams(const LayoutParams &)` method. This mechanism allows for fine-grained control over how individual children behave within the parent's coordinate space.

```cpp
View container = View::New();
FlexLayout layout = FlexLayout::New();
container.SetLayoutParams(layout);
```

## Flex Layout Configuration

The `FlexLayout` class facilitates complex, responsive designs by arranging children based on the industry-standard CSS Flexbox algorithm. This container automatically distributes available space and handles alignment adjustments according to defined directions and growth factors.

To configure the layout's global behavior, developers can adjust the direction of the flow using `SetDirection(FlexDirection)`, specify wrapping behavior with `SetWrap(FlexWrap)`, and control how content is distributed through `SetJustifyContent(FlexJustify)`. Alignment of items along the cross-axis is handled by `SetAlignItems(FlexAlign)` and `SetAlignContent(FlexAlign)`.

Individual children within a flex container can be configured using `FlexLayoutParams`. This allows specific items to define their own growth behavior via `SetFlexGrow(float)`, their shrinkage priority with `SetFlexShrink(float)`, and their initial size basis using `SetFlexBasis(float)`. An individual item may also override the parent's alignment policy by calling `SetAlignSelf(FlexAlign)`.

```cpp
View child = View::New();
FlexLayoutParams params = FlexLayoutParams::New();
params.SetFlexGrow(1.0f);
params.SetAlignSelf(FlexAlign::CENTER);
child.SetLayoutParams(params);
```

## Grid Layout Strategies

The `GridLayout` container is designed for structured, 2D tabular arrangements of user interface elements. It relies on row and column definitions to divide the parent area into manageable cells.

Row and column definitions are established using `AddRowDefinition(GridLength)` and `AddColumnDefinition(GridLength)`, or via bulk assignment with `SetRowDefinitions(const Vector< GridLength > &)` and `SetColumnDefinitions(const Vector< GridLength > &)`. These definitions accept `GridLength` objects, which can represent absolute pixel values, star-based proportional ratios, or auto-sizing logic. For precise control, row and column spacing can be adjusted using `SetRowSpacing(float)` and `SetColumnSpacing(float)`.

Children are positioned within the grid using `GridLayoutParams`. Developers specify the target cell location using `SetRow(uint32_t)` and `SetColumn(uint32_t)`, and can extend a child across multiple cells with `SetRowSpan(uint32_t)` and `SetColumnSpan(uint32_t)`. Internal alignment within a designated grid cell is managed by `SetHorizontalAlignment(LayoutAlignment)` and `SetVerticalAlignment(LayoutAlignment)`.

```cpp
GridLayout grid = GridLayout::New();
grid.AddRowDefinition(GridLength::Star(1.0f));
grid.AddRowDefinition(GridLength::Absolute(100.0f));

View child = View::New();
GridLayoutParams params = GridLayoutParams::New();
params.SetRow(0);
params.SetColumn(0);
params.SetHorizontalAlignment(LayoutAlignment::CENTER);
child.SetLayoutParams(params);
```

## Stack Layout Patterns

The `StackLayout` provides a straightforward approach to arranging UI elements in a single dimension, either linearly horizontal or vertical. This layout is ideal for lists, button bars, or simple vertical forms.

The orientation of the stack is determined by the `StackOrientation` enumeration passed to the `New` factory method or the `SetOrientation(StackOrientation)` method. The space between items can be managed by `SetSpacing(float)`.

When using `StackLayoutParams` for individual children, developers can influence the distribution of available space using `SetWeight(float)`. The alignment of a child relative to the cross-axis of the stack is configured with `SetAlignment(LayoutAlignment)`.

```cpp
StackLayout layout = StackLayout::New(StackOrientation::HORIZONTAL);
layout.SetSpacing(10.0f);

View child = View::New();
StackLayoutParams params = StackLayoutParams::New();
params.SetWeight(1.0f);
child.SetLayoutParams(params);
```

## Absolute Positioning

When precise, coordinate-based placement is required, the `AbsoluteLayout` offers an alternative to flow-based containers. It ignores automatic distribution rules in favor of fixed locations and dimensions.

Children within an absolute container are positioned using `AbsoluteLayoutParams`. Developers set the geometry by applying a `LayoutRect` using `SetBounds(const LayoutRect &)`, or by individual coordinate methods like `SetX(float)`, `SetY(float)`, `SetWidth(float)`, and `SetHeight(float)`. Proportional sizing and positioning behaviors can be enabled via `SetFlags(AbsoluteLayoutFlags)`, allowing elements to scale relative to the parent's dimensions using the `AbsoluteLayoutFlags` bitmask.

```cpp
View child = View::New();
AbsoluteLayoutParams params = AbsoluteLayoutParams::New();
params.SetX(0.5f);
params.SetY(0.5f);
params.SetFlags(AbsoluteLayoutFlags::POSITION_PROPORTIONAL);
child.SetLayoutParams(params);
```

## Measuring and Sizing

Effective layout requires that the framework can calculate the size of every view. The `MeasuredSize` class acts as the standardized container for conveying width and height results during the layout pass.

While container layouts handle the bulk of this logic, custom layout implementations may need to interact with sizing metrics directly. Developers can convert these metrics to a standard `Vector2` using `ToVector2()` for use in underlying engine operations.

## Managing Layout Lifecycle

The lifecycle of a layout is orchestrated by the `LayoutController`. This singleton-like service manages the invalidation and processing of all view hierarchies that require re-calculation.

When a view's layout properties change in a way that affects its geometric size or position, the system triggers a request. Developers can manually ensure a view is updated by calling `RequestLayout(ViewImpl *)` on the instance obtained via `Get(Window)`. For complex scenarios, calling `ProcessLayouts()` will force the synchronization of pending layout states across the active window. In scenarios where a view is removed or the window state changes, the controller handles the necessary cleanup and updates via `UnregisterView(ViewImpl *)` and `OnWindowResize(int32_t, int32_t)` respectively.

```cpp
// Assuming containerView is an existing View instance
ViewImpl& impl = GetImpl(containerView);
LayoutController::Get(window).RequestLayout(&impl);
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/layouts)
