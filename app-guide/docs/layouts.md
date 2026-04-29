---
id: layouts
title: "layouts"
sidebar_label: "layouts"
---
## Overview

The Layouts module provides a declarative system for positioning and sizing `View` elements within an application interface using automated layout managers. These managers abstract away the manual calculation of coordinates, allowing developers to define structural relationships that react gracefully to container resizing or device orientation changes.

## Applying Layouts to Views

The standard workflow for implementing automated positioning involves creating a layout manager instance and assigning it to the desired container using the `SetLayoutParams(const LayoutParams &)` method. This process informs the container how to organize its children, ensuring that the layout logic is decoupled from the individual child element definitions.

```cpp
View parentView = View::New();
AbsoluteLayout layout = AbsoluteLayout::New();
AbsoluteLayoutParams params = AbsoluteLayoutParams::New();
parentView.SetLayoutParams(params);
```

When a layout is applied to a parent `View`, the parent's size and internal structure dictate the final geometry of its children based on the rules defined by that specific layout manager. Developers should trigger layout recalculations when child properties change by interacting with the associated layout system.

## Stack-Based Arrangements

The `StackLayout` class organizes child elements in a strictly defined linear sequence, either vertically or horizontally. This manager is ideal for simple lists, toolbars, or any UI component that requires a uniform flow of items along a single axis.

By utilizing the `SetOrientation(StackOrientation)` method, developers can toggle between row-based and column-based arrangements. Spacing between child elements is handled globally for the stack via the `SetSpacing(float)` method.

```cpp
View container = View::New();
StackLayout stackLayout = StackLayout::New(StackOrientation::HORIZONTAL);
stackLayout.SetOrientation(StackOrientation::HORIZONTAL);
container.SetLayoutParams(stackLayout);
```

Within a stack, individual child behavior can be influenced using `StackLayoutParams`. This allows for fine-grained control over how much space a specific item consumes relative to its siblings. Using the `SetWeight(float)` method, developers can create flexible, proportional distributions where items grow to occupy remaining space based on their defined weight values.

## Flexible Responsive Design

For interfaces that require complex, reactive behavior across varying screen sizes, the `FlexLayout` class provides a robust engine inspired by the web-based CSS Flexbox model. This manager allows for sophisticated control over content flow, alignment, and wrapping without requiring explicit pixel coordinates.

The behavior of the layout is governed by properties such as `SetDirection(FlexDirection)`, `SetWrap(FlexWrap)`, and `SetJustifyContent(FlexJustify)`. These settings collectively determine how the container handles the distribution of its children across the main and cross axes.

```cpp
FlexLayout flexLayout = FlexLayout::New();
flexLayout.SetJustifyContent(FlexJustify::CENTER);
flexLayout.SetWrap(FlexWrap::WRAP);
View container = View::New();
container.SetLayoutParams(flexLayout);
```

Children within a flex container utilize `FlexLayoutParams` to define their growth and shrinkage characteristics. Properties like `SetFlexGrow(float)` and `SetFlexShrink(float)` determine if an element should expand or contract when the container size changes, providing a powerful mechanism for responsive UI scaling.

## Grid and Tabular Structures

The `GridLayout` enables the construction of precise, matrix-based interfaces where child components are placed into defined cells. This system is perfect for dashboards, form layouts, or complex grid-based content displays.

Rows and columns are defined by providing a vector of `GridLength` objects, which specify whether a dimension should be fixed, automatic, or proportional (star-sized). These are registered via `SetRowDefinitions(const Dali::Vector<GridLength> &)` and `SetColumnDefinitions(const Dali::Vector<GridLength> &)`.

```cpp
GridLayout gridLayout = GridLayout::New();
Dali::Vector<GridLength> columns;
columns.PushBack(GridLength::Star(1.0f));
columns.PushBack(GridLength::Star(2.0f));
gridLayout.SetColumnDefinitions(columns);
View child1 = View::New();
GridLayoutParams params1 = GridLayoutParams::New();
params1.SetRow(0);
params1.SetColumn(0);
child1.SetLayoutParams(params1);
```

Each child element within the grid is managed using `GridLayoutParams`. This class allows developers to pin an element to a specific coordinate using `SetRow(uint32_t)` and `SetColumn(uint32_t)`, while also supporting multi-cell occupancy through the `SetRowSpan(uint32_t)` and `SetColumnSpan(uint32_t)` methods.

## Absolute Positioning

When strict, pixel-perfect control is required that bypasses automated flow logic, the `AbsoluteLayout` provides a mechanism for direct coordinate placement. This is particularly useful for overlay elements, decorative graphics, or specific design requirements where content cannot be reflowed.

The layout uses the `AbsoluteLayoutParams` class to associate a specific rectangle with each child. This rectangle is defined by the `SetBounds(const LayoutRect &)` method, or individually via the `SetX(float)`, `SetY(float)`, `SetWidth(float)`, and `SetHeight(float)` methods.

```cpp
View container = View::New();
AbsoluteLayout layout = AbsoluteLayout::New();
container.SetLayoutParams(layout);
View child = View::New();
AbsoluteLayoutParams params = AbsoluteLayoutParams::New();
params.SetX(100.0f);
params.SetY(200.0f);
child.SetLayoutParams(params);
container.Add(child);
```

The `SetFlags(AbsoluteLayoutFlags)` method allows for advanced behavior, such as specifying that a component’s position or size should be calculated as a proportion of the parent's total size rather than as an absolute pixel value.

## Customizing Layout Constraints

Layout parameters act as the configuration bridge between a child `View` and its parent's layout manager. Every layout system provides a specialized class derived from `LayoutParams` to facilitate these settings.

Whether adjusting the alignment of a grid item using `SetHorizontalAlignment(LayoutAlignment)` or specifying the flex basis of an item in a flex container, these parameters are essential for fine-tuning UI composition. By using the chainable nature of these setter methods, developers can concisely define the layout behavior of a component during its creation.

## Managing Layout Complexity

The `LayoutController` serves as the central orchestration point for layout processing, ensuring that geometry updates are batched and executed efficiently. In most scenarios, the framework handles the registration of views automatically, but manual intervention is possible for custom integration scenarios.

If a view requires a manual layout refresh, the `RequestLayout(ViewImpl *)` method schedules that view for the next processing pass. For developers working with complex custom components, the `ProcessLayouts()` method can be invoked to force an immediate calculation of all pending layout requests within the context of a specific window.

```cpp
// Assuming view is a View* (ViewImpl*) and window is a Window
LayoutController& layoutController = LayoutController::Get(window);
layoutController.RequestLayout(view);
layoutController.ProcessLayouts();
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/layouts)
