---
title: View (Base UI Object)
sidebar_label: View
category: views-components
---

# View (Base UI Object)

`View` is the foundational UI component in dali-ui, providing the essential building block for all visual elements. It extends `CustomActor` and implements a complete layout system with measure/arrange passes, visual styling, interaction traits, and animation support.

## Table of Contents

- [Creating and Identifying Views](#creating-and-identifying-views)
- [Layout and Sizing](#layout-and-sizing)
- [Visual Styling](#visual-styling)
- [Interaction with Traits](#interaction-with-traits)
- [View Hierarchy](#view-hierarchy)
- [Animation](#animation)
- [State and Focus](#state-and-focus)
- [Accessibility](#accessibility)

## Creating and Identifying Views

Create a `View` using the static `View::New()` method. The returned handle supports fluent chaining for configuration.

```cpp
using namespace Dali;
using namespace Dali::Ui;

// Create a basic View
View view = View::New();

// Create and configure in a chain
View styledView = View::New()
  .SetName("MyView")
  .SetBackgroundColor(UiColor(0.5f, 0.7f, 1.0f, 1.0f))
  .SetRequestedWidth(200.0f)
  .SetRequestedHeight(100.0f);
```

Use `View::DownCast()` to safely convert a `BaseHandle` to a `View`:

```cpp
View view = View::DownCast(someBaseHandle);
if (view)
{
  // Successfully downcast
}
```

## Layout and Sizing

`View` participates in the dali-ui layout system through measure and arrange passes. Set size requests using `SetRequestedWidth()` and `SetRequestedHeight()`.

### Size Values

- **Positive value**: Fixed size in pixels
- **WRAP_CONTENT** (-1): Size to content
- **MATCH_PARENT** (-2): Fill parent container

```cpp
View fixedView = View::New()
  .SetRequestedWidth(150.0f)
  .SetRequestedHeight(80.0f);

View flexView = View::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(WRAP_CONTENT);
```

### Size Constraints

Apply minimum and maximum size constraints:

```cpp
View constrainedView = View::New()
  .SetMinimumWidth(100.0f)
  .SetMaximumWidth(300.0f)
  .SetMinimumHeight(50.0f)
  .SetMaximumHeight(200.0f);
```

Retrieve size constraints using `GetMinimumWidth()`, `GetMaximumWidth()`, `GetMinimumHeight()`, and `GetMaximumHeight()`.

### Margin and Padding

Set margin (outer space) and padding (inner space) using `Extents`:

```cpp
View spacedView = View::New()
  .SetMargin(Extents(10.0f, 10.0f, 5.0f, 5.0f))  // start, end, top, bottom
  .SetPadding(Extents(20.0f, 20.0f, 15.0f, 15.0f));
```

Retrieve with `GetMargin()` and `GetPadding()`.

### Layout Mode

Control how a View participates in its parent's layout using `SetLayoutMode()`:

- **LayoutMode::DEFAULT**: Normal layout participation
- **LayoutMode::STANDALONE**: Excluded from parent accumulation; position set via `SetRequestedPositionX()` and `SetRequestedPositionY()`

```cpp
// Standalone view for overlay or absolute positioning
View overlay = View::New()
  .SetLayoutMode(LayoutMode::STANDALONE)
  .SetRequestedPositionX(50.0f)
  .SetRequestedPositionY(100.0f)
  .SetRequestedWidth(200.0f)
  .SetRequestedHeight(50.0f);
```

### Layout Direction

Set the layout direction for RTL/LTR support:

```cpp
view.SetLayoutDirection(Dali::LayoutDirection::RIGHT_TO_LEFT);
```

Clear an explicit direction to inherit from parent:

```cpp
view.ClearLayoutDirection();
```

Query the effective direction:

```cpp
Dali::LayoutDirection::Type dir = view.GetEffectiveLayoutDirection();
```

### Layout Parameters

Attach layout-specific parameters using `SetLayoutParams()`:

```cpp
View child = View::New();
child.SetLayoutParams(
  AbsoluteLayoutParams::New()
    .SetBounds(LayoutRect(50.0f, 50.0f, 100.0f, 100.0f)));
```

Retrieve typed layout parameters:

```cpp
auto params = view.GetLayoutParams<AbsoluteLayoutParams>();
if (params)
{
  params.SetWidth(200.0f);
  view.InvalidateMeasure();
}
```

### Measure and Arrange

The layout system uses `Measure()` and `Arrange()` passes:

```cpp
MeasuredSize measured = view.Measure(widthConstraint, heightConstraint);
MeasuredSize arranged = view.Arrange(LayoutRect(x, y, width, height));
```

Invalidate layout when dimensions change:

```cpp
view.InvalidateMeasure();  // Recalculate size
view.InvalidateArrange();   // Recalculate position
```

## Visual Styling

### Background Color

Set a background color using `UiColor`:

```cpp
view.SetBackgroundColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));  // Red

// Or use theme color ID
view.SetBackgroundColor(UiColor::PRIMARY);
```

Retrieve with `GetBackgroundColor()`.

### Opacity

Control transparency:

```cpp
view.SetOpacity(0.5f);  // 50% transparent

float opacity = view.GetOpacity();
```

### Corner Radius

Apply rounded corners:

```cpp
// Uniform radius for all corners
view.SetCornerRadius(20.0f);

// Individual corner radii (topLeft, topRight, bottomRight, bottomLeft)
view.SetCornerRadius(10.0f, 20.0f, 15.0f, 25.0f);

// From Vector4
view.SetCornerRadius(Vector4(10.0f, 20.0f, 15.0f, 25.0f));
```

Query the radius policy:

```cpp
CornerRadiusPolicy policy = view.GetCornerRadiusPolicy();
```

### Borderline

Add a borderline (inset border):

```cpp
view.SetBorderlineWidth(2.0f);
view.SetBorderlineColor(UiColor(0.0f, 0.0f, 0.0f, 1.0f));
view.SetBorderlineOffset(0.0f);  // Centered on edge
```

Retrieve values with `GetBorderlineWidth()`, `GetBorderlineColor()`, and `GetBorderlineOffset()`.

### Color

Set the view's color (tint):

```cpp
view.SetColor(UiColor(1.0f, 1.0f, 1.0f, 1.0f));
UiColor color = view.GetColor();
UiColor currentColor = view.GetCurrentColor();
```

## Interaction with Traits

`View` supports interaction through traits. Use `AsInteractive()` to attach an `InteractiveTrait` for click and press handling.

### Making a View Interactive

```cpp
View button = View::New()
  .SetBackgroundColor(UiColor::PRIMARY)
  .AsInteractive();  // Attaches InteractiveTrait
```

Configure the trait with a callback:

```cpp
View button = View::New()
  .SetBackgroundColor(UiColor::PRIMARY)
  .AsInteractive([](InteractiveTrait trait) {
    trait.SetClickable(true);
  });
```

### Click Handling

Connect to the clicked signal using `ConnectClickedSignal()`:

```cpp
class MyController : public ConnectionTracker
{
  void Setup()
  {
    View button = View::New().AsInteractive();

    InteractiveTrait trait = button.EnsureInteractiveTrait();
    trait.ConnectClickedSignal(this, &MyController::OnClicked);
  }

  void OnClicked(View view, InputEvent event)
  {
    // Handle click
  }
};
```

### Press State

Track pressed state changes:

```cpp
InteractiveTrait trait = view.EnsureInteractiveTrait();
trait.ConnectPressedChangedSignal(this, [](View v, bool pressed, InputEvent e) {
  if (pressed)
  {
    // Press started
  }
  else
  {
    // Press ended
  }
});
```

Query the pressed state:

```cpp
bool isPressed = trait.IsPressed();
```

### Long Press

Handle long-press gestures:

```cpp
InteractiveTrait trait = view.EnsureInteractiveTrait();
trait.ConnectLongPressedSignal(this, [](View v, InputEvent e) -> bool {
  // Return true to consume the event and suppress click
  return true;
});
```

### Selection with SelectableTrait

Use `AsSelectable()` for toggle selection behavior:

```cpp
View toggle = View::New()
  .AsSelectable([](SelectableTrait trait) {
    trait.EnableToggleByClick(true);
  });
```

Query and set selection state:

```cpp
SelectableTrait trait = view.EnsureSelectableTrait();
trait.SetSelected(true);
bool selected = trait.IsSelected();
```

Connect to selection changes:

```cpp
trait.SelectionChangedSignal().Connect(this, [](View v, bool selected, InputEvent e) {
  // Selection changed
});
```

### Using InteractiveView

For convenience, `InteractiveView` provides a View subclass with built-in interactive behavior:

```cpp
InteractiveView button = InteractiveView::New()
  .SetBackgroundColor(UiColor::PRIMARY)
  .SetRequestedWidth(200_spx)
  .SetRequestedHeight(100_spx)
  .ConnectClickedSignal(this, &MyClass::OnButtonClicked);
```

## View Hierarchy

Build view hierarchies using `Add()` and declarative methods.

### Adding Children

```cpp
View parent = View::New();
View child = View::New();
parent.Add(child);
```

### Declarative Children

Use `Children()` for declarative UI construction:

```cpp
View container = View::New().Children({
  View::New().SetBackgroundColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f)),
  View::New().SetBackgroundColor(UiColor(0.0f, 1.0f, 0.0f, 1.0f)),
  View::New().SetBackgroundColor(UiColor(0.0f, 0.0f, 1.0f, 1.0f))
});
```

### Child Access

Query children by index:

```cpp
uint32_t count = parent.GetChildCount();
View child = parent.GetChildAt(0);
int32_t index = parent.IndexOfChild(someChild);  // -1 if not found
```

### Sibling Order

Control visual z-order and layout order:

```cpp
view.Raise(LayoutOrderPolicy::UPDATE);       // Move up one sibling
view.Lower(LayoutOrderPolicy::PRESERVE);     // Move down, layout unchanged
view.RaiseToTop(LayoutOrderPolicy::UPDATE);
view.LowerToBottom(LayoutOrderPolicy::UPDATE);
view.RaiseAbove(targetView, LayoutOrderPolicy::UPDATE);
view.LowerBelow(targetView, LayoutOrderPolicy::UPDATE);
```

### Capturing View References

Use `As()` to capture a reference during declarative construction:

```cpp
View myButton;
View container = View::New().Children({
  View::New()
    .SetBackgroundColor(UiColor::PRIMARY)
    .As(myButton)  // Captures reference
});
// myButton is now valid
```

## Animation

`View` provides typed animation APIs through `ViewAnimationBridge` and `ViewAnimationSpec`.

### Bridge Pattern (View-First)

Create animations directly on a View using `Animate()`:

```cpp
auto anim = Animation::New();
view.Animate(anim)
  .Opacity(0.0f, 300_ms, AlphaFunction::EASE_OUT)
  .PositionX(100.0f, 500_ms);
anim.Play();
```

### Spec Pattern (Reusable)

Create reusable animation specs with `NewAnimationSpec()`:

```cpp
// Define a reusable spec
ViewAnimationSpec fadeInSpec = View::NewAnimationSpec()
  .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);

// Apply to any view
auto anim = Animation::New();
fadeInSpec.ApplyTo(anim, view);
anim.Play();
```

### Animatable Properties

Common animatable properties include:

- `Opacity()` - animate opacity
- `PositionX()`, `PositionY()` - animate position
- `ScaleX()`, `ScaleY()` - animate scale
- `CornerRadius()` - animate corner radius
- `BorderlineWidth()`, `BorderlineOffset()` - animate borderline
- `BackgroundColor()` - animate background color

Each property has a `By` variant for relative animations:

```cpp
view.Animate(anim)
  .OpacityBy(-0.3f, 200_ms)  // Reduce opacity by 0.3
  .PositionXBy(50.0f, 300_ms); // Move right by 50 pixels
```

## State and Focus

`View` tracks state through `ViewState` and emits state change signals.

### Querying State

```cpp
const ViewState& state = view.GetState();
if (state.Has(ViewState::FOCUSED))
{
  // View is focused
}
```

### State Changed Signal

Connect to state changes:

```cpp
view.StateChangedSignal().Connect(this, [](View v, StateEvent e) {
  if (e.Added(ViewState::FOCUSED))
  {
    // Gained focus
  }
  if (e.Removed(ViewState::ENABLED))
  {
    // Became disabled
  }
});
```

### Enabled State

Control the enabled state:

```cpp
view.SetEnabled(false);  // Disable the view
bool enabled = view.IsEnabled();
bool effectivelyEnabled = view.IsEffectivelyEnabled();  // Checks ancestors too
```

### Focus Navigation

Set up focus navigation between views:

```cpp
view.SetFocusable(true);
view.SetLeftFocusableView(leftView);
view.SetRightFocusableView(rightView);
view.SetUpFocusableView(upView);
view.SetDownFocusableView(downView);
```

## Accessibility

Configure accessibility properties for assistive technologies.

### Accessibility Name and Description

```cpp
view.SetProperty(View::Property::ACCESSIBILITY_NAME, "Submit Button");
view.SetProperty(View::Property::ACCESSIBILITY_DESCRIPTION, "Submits the form");
```

### Accessibility Role

```cpp
view.SetProperty(View::Property::ACCESSIBILITY_ROLE, (int)Dali::Accessibility::Role::BUTTON);
```

### Accessibility States

```cpp
view.SetProperty(View::Property::ACCESSIBILITY_STATES, (int)Dali::Accessibility::State::FOCUSABLE);
```

### Other Accessibility Properties

- `ACCESSIBILITY_HIDDEN`: Hide from accessibility tree
- `ACCESSIBILITY_HIGHLIGHTABLE`: Allow highlight
- `ACCESSIBILITY_IS_MODAL`: Mark as modal
- `ACCESSIBILITY_SCROLLABLE`: Mark as scrollable
- `ACCESSIBILITY_VALUE`: Current value (e.g., "50%" for slider)
- `AUTOMATION_ID`: String identifier for automation frameworks