---
id: view
title: "View (Base UI Object)"
sidebar_label: "View (Base UI Object)"
---
## Overview

The View module provides the foundational building block for constructing modern, reactive UI hierarchies within the DALi framework. As the primary component for UI development, the View class handles spatial positioning, layout measurement, [rendering](./rendering.md) configuration, and event propagation.

## Declarative UI and Fluent Chaining

The View framework supports a declarative programming style through a fluent API pattern, allowing developers to construct complex UI trees with high readability. Most setter methods are chainable, returning a reference to the View [object](./object.md) to allow successive configuration in a single statement.

The framework provides specific methods to manage hierarchical structure and inline configuration. Use the `Children(std::initializer_list<View>)` to define a nested tree of views within a single initialization block. The `As(View&)` allows you to capture a reference to a specific view instance that is being constructed declaratively, while the `With(F&&)` provides a flexible mechanism to execute custom operations on a view during the construction process.

```cpp
View container = View::New().Children({
  View::New().SetRequestedWidth(100.0f).SetRequestedHeight(100.0f),
  View::New().SetRequestedWidth(50.0f).SetRequestedHeight(50.0f)
});
```

## Layout and Sizing Mechanics

Views rely on a measurement and arrangement cycle to determine their spatial presence on the screen. You can control the layout behavior by specifying constraints such as width, height, and various margins.

The measurement process is triggered by the `Measure(float, float)`, which informs the view of available constraints, followed by the `Arrange(const LayoutRect&)` to finalize the layout position and dimensions. If you need to force a re-layout due to external property changes, use `InvalidateMeasure()` and `InvalidateArrange()`. For advanced use cases, custom logic can be injected via the `SetMeasureCallback(MeasureCallback)` and the `SetArrangeCallback(ArrangeCallback)`. You may also configure the behavior of a view within its parent using the `SetLayoutMode(LayoutMode)`.



## Visual Configuration and Styling

Visual attributes determine the aesthetic presentation of the View, including its background, borders, and corner geometry. The framework provides robust support for rounded corners, which can be configured using the `SetCornerRadius(float)` and the `SetCornerRadiusPolicy(CornerRadiusPolicy)`.

You can customize the borderline appearance by using the `SetBorderlineWidth(float)`, `SetBorderlineColor(const UiColor&)`, and `SetBorderlineOffset(float)`. Additionally, background colors are managed via the `SetBackgroundColor(const UiColor&)`, while the global visibility and transparency of the element are controlled by the `SetVisibility(bool)` and the `SetOpacity(float)`.

```cpp
View styledView = View::New()
  .SetCornerRadius(10.0f)
  .SetCornerRadiusPolicy(CornerRadiusPolicy::ABSOLUTE)
  .SetBorderlineWidth(2.0f)
  .SetBorderlineColor(UiColor(1.0f, 1.0f, 1.0f, 1.0f))
  .SetBorderlineOffset(1.0f);
```

## Adding Interactivity and Selection

Static views can be enhanced with reactive behaviors using traits. The `AsInteractive()` attaches an interaction trait to the view, which enables click and gesture handling. Similarly, the `AsSelectable()` attaches a selection trait, allowing the view to maintain a persistent state of being selected or deselected.

Once a trait is attached, you can manage the state directly, for instance by using the `SetSelected(bool)` for selectable components or `SetClickable(bool)` for interactive ones. The framework also supports a pseudo-disabled state, managed via the `SetPseudoDisabled(bool)`, which allows the view to remain interactive for guidance purposes while appearing visually inactive.

```cpp
View myView = View::New();

myView.AsInteractive([](InteractiveTrait interactive) {
  interactive.SetClickable(true);
});

myView.AsSelectable([](SelectableTrait selectable) {
  selectable.SetSelected(true);
  selectable.EnableToggleByClick(true);
});
```

## Event Handling and Signal Management

The View module uses a signal-based mechanism to communicate user input and state changes. You can respond to user interaction by connecting to specific signals, such as the `ClickedSignal()` for single taps or the `LongPressedSignal()` for extended interaction.

State changes are communicated through dedicated signals, such as the `StateChangedSignal()` and the `SelectionChangedSignal()`. Key navigation events are captured via the `KeyEventSignal()`, ensuring that the application can respond appropriately to non-touch input.

```cpp
void MyHandler(View view, const InputEvent& event) { /* ... */ }

View view = View::New();
view.AsInteractive().ConnectClickedSignal(&view, &MyHandler);
view.StateChangedSignal().Connect(&view, &MyHandler);
```

## Focus and Navigation Control

To support accessible and keyboard-driven interfaces, views participate in a structured focus management system. You can determine if a view is eligible for focus by using the `SetFocusable(bool)` or the `SetTouchFocusable(bool)`.

The movement of focus is defined by explicitly linking neighbors using methods such as `SetLeftFocusableView(View)`, `SetRightFocusableView(View)`, `SetUpFocusableView(View)`, and `SetDownFocusableView(View)`. This ensures that users can navigate through the UI hierarchy using standard directional keys or focus traversal paths.

```cpp
View buttonA = View::New();
View buttonB = View::New();

buttonA.SetRightFocusableView(buttonB);
buttonB.SetLeftFocusableView(buttonA);
```

## View Hierarchy and Z-Order

Managing the composition and stacking order of views is essential for complex UI design. The View class provides methods to add, remove, and query children, such as the `Insert(uint32_t, View)` and the `RemoveAllChildren()`.

The visual stacking order—often referred to as Z-order—is manipulated through methods that adjust the relative layering of siblings. You can use `Raise(LayoutOrderPolicy)` and `Lower(LayoutOrderPolicy)` to shift a view's position, or use `RaiseToTop(LayoutOrderPolicy)` and `LowerToBottom(LayoutOrderPolicy)` for extreme adjustments. Precise ordering relative to another component is achieved using the `RaiseAbove(View, LayoutOrderPolicy)` or the `LowerBelow(View, LayoutOrderPolicy)`.



## Related Sub-Components

- `[view-impl](./view-impl.md)`: Provides the internal implementation details required for extending View capabilities. → See: [view-impl]
- `[view-state](./view-state.md)`: Manages the current state transitions and representation for UI components. → See: [view-state]
- `[view-types](./view-types.md)`: Defines the common types and helper structures used throughout the View module. → See: [view-types]
- `[view-accessibility-enums](./view-accessibility-enums.md)`: Contains enumerations for defining accessibility roles and states. → See: [view-accessibility-enums]
- `[view-focus-enums](./view-focus-enums.md)`: Provides specific enumerations for controlling focus navigation behaviors. → See: [[view-focus-enums](./view-focus-enums.md)]

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/view)
