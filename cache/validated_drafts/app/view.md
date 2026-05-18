# Overview

`View` is the fundamental building block for UI construction, providing a declarative interface for managing visual elements and their properties in DALi applications. It serves as the primary base class for all UI components, handling life cycle, hierarchy management, and visual rendering orchestration.

# Declarative UI Construction

Dali applications leverage a fluent API style to build complex UI hierarchies concisely. By utilizing method chaining, developers can configure properties, add child views, and apply traits in a single, readable initialization statement.

The framework supports hierarchical construction using the `Children` method, which accepts an initializer list of child views. To maintain references to specific views within a nested structure, developers should use the `As` method, which assigns the current instance to a provided variable. Additional modifications can be applied via the `With` method, which executes a provided lambda on the instance.

```cpp
View root = View::New();
View child = View::New();

root.Children({
  child.With([](View& v) {
    v.SetName("MyChildView");
  }).As(child)
});
```

# Layout and Sizing

Spatial organization is managed through requested dimensions, layout modes, and spacing constraints. Views calculate their final size based on these parameters during the layout pass.

Developers can define boundaries using `SetRequestedWidth` and `SetRequestedHeight`. Constraints such as `SetMinimumWidth`, `SetMinimumHeight`, `SetMaximumWidth`, and `SetMaximumHeight` ensure that views remain within specific bounds during responsiveness adjustments. Inner and outer spacing are handled via the `SetPadding` and `SetMargin` methods, respectively.

```cpp
View view = View::New()
  .SetRequestedWidth(100.0f)
  .SetRequestedHeight(100.0f)
  .SetMinimumWidth(50.0f)
  .SetMinimumHeight(50.0f)
  .SetMaximumWidth(200.0f)
  .SetMaximumHeight(200.0f)
  .SetPadding(Extents(10, 10, 10, 10));
```

# Visual Styling and Configuration

The appearance of a view is defined by its background, corner geometry, and decorative properties. These configurations are applied to the visual surface of the component.

You can set the background color using `SetBackgroundColor` or provide complex visuals like gradients and images using `AddVisual`. Rounded corners are controlled by the `SetCornerRadius` method, with the policy defined by `SetCornerRadiusPolicy`. For border effects, use `SetBorderlineWidth` and `SetBorderlineColor`.

```cpp
View view = View::New()
  .SetBackgroundColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f))
  .SetCornerRadius(10.0f)
  .SetBorderlineWidth(2.0f)
  .SetBorderlineColor(UiColor::PRIMARY);
```

# Interaction and Input Handling

Standard views can be transformed into interactive elements to respond to user input. This process involves attaching specific traits and configuring the view's ability to receive focus.

The interaction capability is provided by the `AsInteractive` method, which enables click and press signals. To handle focus navigation for keyboard or controller inputs, configure neighbor links using methods like `SetLeftFocusableView`, `SetRightFocusableView`, `SetUpFocusableView`, and `SetDownFocusableView`.

```cpp
class MyHandler {
public:
  void OnClicked(View view, InputEvent event) {}
};

MyHandler* handler = new MyHandler();
View view = View::New().AsInteractive([&](View& v) {
  v.EnsureInteractiveTrait().ConnectClickedSignal(handler, &MyHandler::OnClicked);
});
```

# State and Trait Management

High-level component states are managed through trait objects, which provide specific logic for selection and engagement. These traits are typically accessed via dedicated getter and initialization methods.

The `InteractiveTrait` allows for managing press states and click policies. The `SelectableTrait` provides methods such as `SetSelected` and `EnableToggleByClick` to manage toggleable states. You can ensure these traits are active by calling `EnsureInteractiveTrait` or `EnsureSelectableTrait`.

```cpp
View view = View::New().AsSelectable([](View& v) {
  v.EnsureSelectableTrait().EnableToggleByClick(true);
});

view.EnsureSelectableTrait().SetSelected(true);
```

# Lifecycle and Scene Awareness

Managing the lifecycle of a view involves monitoring its attachment status and its position within the visual hierarchy. Views are automatically organized within a parent-child structure.

The `IsOnScene` method checks if the view is currently connected to the render tree. When constructing trees, developers can manipulate sibling order using `Raise`, `Lower`, `RaiseToTop`, and `LowerToBottom`. Resource-heavy views should be monitored using the `ResourceReadySignal` to ensure data is loaded before interaction is permitted.



# Related Sub-Components

- `view-impl`: Provides the internal implementation infrastructure for creating custom View subclasses. → See: [view-impl]
- `view-state`: Manages the collection of flags and states that represent the current status of a View. → See: [view-state]
- `view-types`: Defines the core structures and enumerations required for layout and rendering configurations. → See: [view-types]
- `view-accessibility-enums`: Lists the constants for defining the role and state of views for accessibility services. → See: [view-accessibility-enums]
- `view-focus-enums`: Specifies the constants used for defining focus navigation policies and types. → See: [view-focus-enums]