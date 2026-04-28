---
id: view
title: "View (Base UI Object)"
sidebar_label: "View (Base UI Object)"
---
## Overview

Dali::Ui::View is the fundamental building block for constructing modern, declarative UIs in DALi, providing a structured way to manage visual composition, layout constraints, and component interactions. By serving as a robust wrapper for UI logic, it enables developers to create complex interfaces with a clean, readable, and highly maintainable hierarchy.

## Fluent API and Declarative Construction

The framework utilizes a fluent interface to simplify the creation and configuration of UI trees, allowing developers to chain setters and structure components in a single expression. This declarative style replaces traditional, imperative multi-line setup code with a concise syntax.

### Hierarchical Tree Construction

To define the parent-child relationships within an interface, use the `Children(std::initializer_list<View>)` to define an initializer list of child components. The `As(View&)` allows you to capture a reference to a specific view created deep within a declarative tree, which is useful when you need to access that component later for dynamic updates.

```cpp
View parent = View::New();
View child1 = View::New();
View child2 = View::New();

parent.Children({child1, child2});
child1.As(child1);
```

### Custom Actions and Traits

The `With(F&&)` provides an extension point to execute custom logic or complex configuration blocks on a view during its initialization. Additionally, traits like `AsInteractive(F&&)` and `AsSelectable(F&&)` can be applied to extend the functionality of a standard view, enabling interaction and selection capabilities through simple method calls.

```cpp
View view = View::New().With([](View& v) {
  v.SetBackgroundColor(UiColor(1.0f, 1.0f, 1.0f, 1.0f));
}).AsInteractive([](InteractiveTrait& t) {
  t.SetClickable(true);
});
```

## Layout and Sizing Mechanics

Views calculate their spatial footprint through a multi-pass process involving measurement and arrangement, ensuring that content remains responsive to dynamic changes. You can control this behavior by defining size constraints and alignment properties.

### Size and Layout Configuration

To influence the size of a view, use methods like `SetRequestedWidth(float)` and `SetRequestedHeight(float)`, which inform the parent of the preferred dimensions. More specific constraints are handled through `SetMinimumWidth(float)`, `SetMinimumHeight(float)`, `SetMaximumWidth(float)`, and `SetMaximumHeight(float)`. For fine-grained control over spacing within and around a component, use `SetMargin(Extents)` and `SetPadding(Extents)`.

```cpp
View myView = View::New()
  .SetRequestedWidth(100.0f)
  .SetRequestedHeight(100.0f)
  .SetMinimumWidth(50.0f)
  .SetMinimumHeight(50.0f)
  .SetMaximumWidth(200.0f)
  .SetMaximumHeight(200.0f)
  .SetMargin(Extents(10, 10, 10, 10))
  .SetPadding(Extents(5, 5, 5, 5));
```

### Layout Control

The `SetLayoutMode(LayoutMode)` dictates how a view participates in its parent's layout, while `SetLayoutDirection(Dali::LayoutDirection::Type)` allows you to explicitly define whether content flows in a standard or reverse direction. You can trigger re-layout cycles manually if necessary using `InvalidateMeasure()` and `InvalidateArrange()`.

## Visual Styling and Effects

Visual customization in DALi is handled through a variety of property setters that modify the rendering of the view. You can create rounded corners, apply borders, or specify unique background appearances to achieve the desired aesthetic.

### Corner and Border Styling

The corner geometry is controlled via `SetCornerRadius(float)` and `SetCornerSquareness(float)`. You can toggle between absolute and relative corner scaling using `SetCornerRadiusPolicy(CornerRadiusPolicy)` or the convenience helper `SetCornerRadiusPolicyRelative()`. Border aesthetics, including width, color, and positioning, are configured using `SetBorderlineWidth(float)`, `SetBorderlineColor(UiColor)`, and `SetBorderlineOffset(float)`.

```cpp
View myView = View::New()
  .SetCornerRadius(15.0f)
  .SetCornerRadiusPolicyRelative()
  .SetBorderlineWidth(2.0f)
  .SetBorderlineColor(UiColor(1.0f, 1.0f, 1.0f, 1.0f))
  .SetBorderlineOffset(1.0f);
```

## Interactive and Selectable Traits

Interactive and selectable traits empower views to participate in the application's user experience. By attaching these traits, views become capable of responding to clicks, long-press gestures, and maintaining selection states.

### Interaction and State

Views with an interaction trait can have their click behavior managed via `SetClickable(bool)` and `SetKeyClickPolicy(KeyClickPolicy)`. The selectable trait provides state management, where `SetSelected(bool)` and `EnableToggleByClick(bool)` allow for rich UI states like radio buttons or checkboxes.

```cpp
View view = View::New();
InteractiveTrait interactive = view.EnsureInteractiveTrait();
interactive.SetClickable(true);

SelectableTrait selectable = view.EnsureSelectableTrait();
selectable.EnableToggleByClick(true);
```

## Event Handling and Signal Management

Signal-slot connections are the primary mechanism for responding to user events and internal view transitions. This architecture ensures that your application logic remains decoupled from the specific visual implementation of the views.

### Monitoring Changes

You can connect to events like `ClickedSignal()`, `PressedChangedSignal()`, or `StateChangedSignal()` to perform actions when a user engages with the UI. For input handling, the `KeyEventSignal()` provides a robust way to intercept hardware key presses.

```cpp
View myView = View::New();
myView.EnsureInteractiveTrait().ConnectClickedSignal(this, &MyHandler::OnClicked);
myView.StateChangedSignal().Connect(this, &MyHandler::OnStateChanged);
```

## Focus and Navigation Control

Keyboard and directional navigation are managed by assigning focus targets and defining the focus group behavior. This ensures that users can traverse the interface seamlessly without mouse or touch input.

### Navigation Configuration

To control the focus order, use directional methods such as `SetLeftFocusableView(View)`, `SetRightFocusableView(View)`, `SetUpFocusableView(View)`, and `SetDownFocusableView(View)`. Additionally, `SetClockwiseFocusableView(View)` and `SetCounterClockwiseFocusableView(View)` offer circular navigation paths. Enable focus support on any view by using `SetFocusable(bool)` or `SetKeyNavigationSupport(bool)`.

```cpp
View parent = View::New();
View left = View::New();
View right = View::New();

parent.SetFocusable(true);
parent.SetKeyNavigationSupport(true);
parent.SetLeftFocusableView(left);
parent.SetRightFocusableView(right);
```

## Lifecycle and Scene Awareness

Views provide signals to track their progress through the scene graph, from resource loading to final render completion. Understanding these states is crucial for performance optimization, such as delaying animations until assets are fully loaded.

### Resource and Scene Status

The `IsResourceReady()` and the associated `ResourceReadySignal()` allow you to check or react to the loading status of required assets. The `IsOnScene()` identifies if the view is currently live in the display hierarchy. For more advanced use cases, the `OffScreenRenderingFinishedSignal()` indicates when offscreen operations for a view have successfully completed.

## Related Sub-Components

- **[view-impl](./view-impl.md)**: Provides the internal implementation details and custom actor bridge logic for complex, custom view components. → See: [[view-impl](./view-impl.md)]
- **[view-state](./view-state.md)**: Manages the state machine and conditional property sets for dynamic view behaviors. → See: [[view-state](./view-state.md)]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/view)
