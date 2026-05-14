---
title: View (Base UI Object)
sidebar_label: View (Base UI Object)
category: views-components
---

# View (Base UI Object)

`View` is the foundational UI component class in dali-ui, providing essential functionality for building user interfaces including layout, rendering, input handling, and state management.

## Table of Contents

- [Creating Views](#creating-views)
- [Layout and Sizing](#layout-and-sizing)
- [Visual Styling](#visual-styling)
- [View Hierarchy](#view-hierarchy)
- [Input and Interaction](#input-and-interaction)
- [Focus Navigation](#focus-navigation)
- [State Management](#state-management)
- [Animation](#animation)

## Creating Views

Create a `View` using the static `New()` method:

```cpp
using namespace Dali::Ui;

View view = View::New();
```

For interactive views that respond to clicks, use `InteractiveView::New()` or attach the `InteractiveTrait` to a base `View`:

```cpp
View view = View::New()
  .AsInteractive()
  .SetBackgroundColor(UiColor(0x4488FF));
```

## Layout and Sizing

### Requested Size

Set the desired dimensions using `SetRequestedWidth()` and `SetRequestedHeight()`. Special values include `WRAP_CONTENT` to size to content and `MATCH_PARENT` to fill the parent container:

```cpp
view.SetRequestedWidth(200.0f);
view.SetRequestedHeight(MATCH_PARENT);
```

### Margins and Padding

Configure external spacing with `SetMargin()` and internal spacing with `SetPadding()`:

```cpp
view.SetMargin(Extents(10.0f, 10.0f, 5.0f, 5.0f));  // start, end, top, bottom
view.SetPadding(Extents(20.0f, 20.0f, 15.0f, 15.0f));
```

### Size Constraints

Apply minimum and maximum size limits:

```cpp
view.SetMinimumWidth(100.0f);
view.SetMaximumWidth(400.0f);
view.SetMinimumHeight(50.0f);
view.SetMaximumHeight(300.0f);
```

### Layout Parameters

Attach layout-specific parameters using `SetLayoutParams()`. The parameters type depends on the parent container:

```cpp
using namespace Dali::Ui;

View child = View::New();
child.SetLayoutParams(
  AbsoluteLayoutParams::New()
    .SetBounds(LayoutRect(50.0f, 50.0f, 100.0f, 100.0f)));
parent.Add(child);
```

### Layout Mode

Use `SetLayoutMode()` to control how the view participates in its parent's layout. `LayoutMode::STANDALONE` excludes the view from parent accumulation, useful for overlays and absolute positioning:

```cpp
overlayView.SetLayoutMode(LayoutMode::STANDALONE);
overlayView.SetRequestedPositionX(20.0f);
overlayView.SetRequestedPositionY(30.0f);
```

### Measure and Arrange

The layout system uses `Measure()` and `Arrange()` passes. Trigger recalculation with `InvalidateMeasure()` or `InvalidateArrange()`:

```cpp
view.InvalidateMeasure();
view.InvalidateArrange();
```

Retrieve the measured or current size:

```cpp
MeasuredSize measured = view.GetMeasuredSize();
MeasuredSize current = view.GetCurrentSize();
```

## Visual Styling

### Background Color

Set a solid background color using `SetBackgroundColor()`. Pass a `UiColor` with either a color ID (theme-aware) or direct RGBA values:

```cpp
view.SetBackgroundColor(UiColor(0xFF6600));       // Direct RGBA
view.SetBackgroundColor(UiColor::PRIMARY);        // Theme color
```

### Corner Radius

Apply rounded corners with `SetCornerRadius()`. Control whether values are absolute or relative using `SetCornerRadiusPolicy()`:

```cpp
view.SetCornerRadius(20.0f);  // Uniform radius for all corners

// Individual corners: topLeft, topRight, bottomRight, bottomLeft
view.SetCornerRadius(10.0f, 20.0f, 10.0f, 20.0f);

// Relative policy: values are percentage [0.0, 0.5] of shorter side
view.SetCornerRadiusPolicy(CornerRadiusPolicy::RELATIVE);
view.SetCornerRadius(0.2f);
```

### Borderline

Add a border with `SetBorderlineWidth()`, `SetBorderlineColor()`, and `SetBorderlineOffset()`:

```cpp
view.SetBorderlineWidth(2.0f);
view.SetBorderlineColor(UiColor(0x333333));
view.SetBorderlineOffset(0.0f);  // Centered on edge
```

### Visibility and Opacity

Control visibility with `SetVisibility()` and transparency with `SetOpacity()`:

```cpp
view.SetVisibility(true);
view.SetOpacity(0.8f);

bool visible = view.IsVisible();
float opacity = view.GetOpacity();
```

### Color

Set the overall color tint using `SetColor()`:

```cpp
view.SetColor(UiColor(0xFF0000));
UiColor color = view.GetColor();
UiColor currentColor = view.GetCurrentColor();
```

## View Hierarchy

### Adding Children

Add child views using `Add()` or the declarative `Children()` method:

```cpp
parent.Add(childView);

// Declarative style
View container = View::New().Children({
  View::New().SetBackgroundColor(UiColor(0xFF0000)),
  View::New().SetBackgroundColor(UiColor(0x00FF00)),
  View::New().SetBackgroundColor(UiColor(0x0000FF))
});
```

### Accessing Children

Query children by index or count:

```cpp
uint32_t count = parent.GetChildCount();
View first = parent.GetChildAt(0);
int32_t index = parent.IndexOfChild(someChild);
```

### Removing Children

Remove all children or specific views:

```cpp
parent.Remove(childView);
parent.RemoveAllChildren();
```

### Sibling Order

Control visual z-order with `Raise()`, `Lower()`, and related methods. Pass `LayoutOrderPolicy::UPDATE` to also reorder layout children:

```cpp
view.Raise(LayoutOrderPolicy::PRESERVE);      // Visual only
view.RaiseToTop(LayoutOrderPolicy::UPDATE);    // Also updates layout order
view.Lower(LayoutOrderPolicy::PRESERVE);
view.LowerToBottom(LayoutOrderPolicy::PRESERVE);
view.RaiseAbove(siblingView, LayoutOrderPolicy::PRESERVE);
view.LowerBelow(siblingView, LayoutOrderPolicy::PRESERVE);
```

## Input and Interaction

### Interactive Trait

Attach the `InteractiveTrait` using `AsInteractive()` to enable click, long-press, and pressed-state handling:

```cpp
View button = View::New()
  .AsInteractive([](InteractiveTrait trait) {
    trait.SetClickable(true);
  });
```

### Click Handling

Connect to the clicked signal using `ConnectClickedSignal()`:

```cpp
view.AsInteractive();
InteractiveTrait trait = view.EnsureInteractiveTrait();
trait.ConnectClickedSignal(this, &MyClass::OnClicked);

// Handler signature: void(View, InputEvent)
void OnClicked(View view, InputEvent event) {
  // Handle click
}
```

### Long Press

Handle long-press gestures with `ConnectLongPressedSignal()`. Return `true` to suppress the subsequent click:

```cpp
trait.ConnectLongPressedSignal(this, &MyClass::OnLongPressed);

// Handler signature: bool(View, InputEvent)
bool OnLongPressed(View view, InputEvent event) {
  // Handle long press
  return true;  // Consume event, suppress click
}
```

### Pressed State

Track pressed state changes with `ConnectPressedChangedSignal()`:

```cpp
trait.ConnectPressedChangedSignal(this, &MyClass::OnPressedChanged);

// Handler signature: void(View, bool pressed, InputEvent)
void OnPressedChanged(View view, bool pressed, InputEvent event) {
  if (pressed) {
    // Visual feedback for press
  } else {
    // Reset visual state
  }
}
```

### Query State

Check interaction state:

```cpp
bool pressed = trait.IsPressed();
bool clickable = trait.IsClickable();
```

## Focus Navigation

### Focusability

Enable keyboard focus with `SetFocusable()`:

```cpp
view.SetFocusable(true);
view.SetTouchFocusable(true);  // Focus on touch

bool focusable = view.IsFocusable();
```

### Directional Navigation

Explicitly set focusable neighbors for directional navigation:

```cpp
view.SetLeftFocusableView(leftView);
view.SetRightFocusableView(rightView);
view.SetUpFocusableView(upView);
view.SetDownFocusableView(downView);
view.SetClockwiseFocusableView(clockwiseView);
view.SetCounterClockwiseFocusableView(counterClockwiseView);
view.SetForwardFocusableView(forwardView);   // Tab key
view.SetBackwardFocusableView(backwardView); // Shift+Tab
```

### Focus Groups

Mark a view as a focus group boundary:

```cpp
view.SetProperty(View::Property::FOCUS_GROUP, true);
```

### Blocking Descendant Focus

Prevent descendants from receiving focus:

```cpp
view.SetDescendantFocusBlocked(true);
bool blocked = view.IsDescendantFocusBlocked();
bool hasBlockingAncestor = view.HasAncestorBlockingFocus();
```

## State Management

### ViewState

Query the current state using `GetState()`, which returns a `ViewState` containing combined state flags:

```cpp
const ViewState& state = view.GetState();
```

### State Changes

Connect to `StateChangedSignal()` to react to state transitions:

```cpp
view.StateChangedSignal().Connect(this, &MyClass::OnStateChanged);

// Handler signature: void(View, StateEvent)
void OnStateChanged(View view, StateEvent event) {
  if (event.Added(ViewState::FOCUSED)) {
    // View gained focus
  }
  if (event.Removed(ViewState::FOCUSED)) {
    // View lost focus
  }
}
```

### Enabled State

Control the enabled state with `SetEnabled()`:

```cpp
view.SetEnabled(false);  // Disable the view
bool enabled = view.IsEnabled();
bool effectivelyEnabled = view.IsEffectivelyEnabled();  // Checks ancestors too
```

## Animation

### View-First Animation

Use `Animate()` to create a `ViewAnimationBridge` for direct animation:

```cpp
auto anim = Animation::New();
view.Animate(anim)
  .Opacity(0.0f, 300_ms, AlphaFunction::EASE_OUT)
  .PositionY(50.0f, 200_ms);
anim.Play();
```

### Reusable Animation Specs

Create a reusable `ViewAnimationSpec` with `NewAnimationSpec()` for defining animation parameters:

```cpp
auto fadeInSpec = View::NewAnimationSpec()
  .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);

// Apply using view-first animation
auto anim = Animation::New();
view.Animate(anim)
  .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);
anim.Play();
```

### Animatable Properties

Common animatable properties include opacity, position, and scale. These are accessed through the animation bridge:

```cpp
view.Animate(anim)
  .Opacity(0.5f, 300_ms)
  .PositionX(100.0f, 200_ms)
  .Scale(1.2f, 150_ms);
```

## Selection with SelectableTrait

### Enabling Selection

Attach the `SelectableTrait` using `AsSelectable()`:

```cpp
View checkbox = View::New()
  .AsSelectable([](SelectableTrait trait) {
    trait.EnableToggleByClick(true);
  });
```

### Selection State

Query and set selection programmatically:

```cpp
SelectableTrait trait = view.EnsureSelectableTrait();
trait.SetSelected(true);
bool selected = trait.IsSelected();
```

### Selection Changes

Connect to `SelectionChangedSignal()`:

```cpp
trait.SelectionChangedSignal().Connect(this, &MyClass::OnSelectionChanged);

// Handler signature: void(View, bool selected, InputEvent)
void OnSelectionChanged(View view, bool selected, InputEvent event) {
  if (selected) {
    // Update visual for selected state
  }
}
```

## Layout Direction

Control text and layout direction with `SetLayoutDirection()`:

```cpp
view.SetLayoutDirection(Dali::LayoutDirection::RIGHT_TO_LEFT);
view.ClearLayoutDirection();  // Revert to inheritance

bool inheriting = view.IsLayoutDirectionInherited();
Dali::LayoutDirection::Type effective = view.GetEffectiveLayoutDirection();
```

## Utility Methods

### Name and Identification

Set a debug name:

```cpp
view.SetName("submitButton");
Dali::String name = view.GetName();
```

### Parent Origin and Pivot

Control positioning anchor points:

```cpp
view.SetParentOrigin(Vector3(0.5f, 0.5f, 0.0f));  // Center
view.SetPivot(Vector3(0.5f, 0.5f, 0.0f));          // Center pivot

Vector3 origin = view.GetParentOrigin();
Vector3 pivot = view.GetPivot();
```

### Scale

Apply scale transformations:

```cpp
view.SetScale(1.5f, 1.5f);
view.SetScaleX(2.0f);
view.SetScaleY(0.5f);

Vector2 scale = view.GetCurrentScale();
float scaleX = view.GetCurrentScaleX();
float scaleY = view.GetCurrentScaleY();
```

### Scene Connection

Check if the view is on the scene:

```cpp
bool onScene = view.IsOnScene();
```

### Resource Readiness

Check if resources are loaded:

```cpp
if (view.IsResourceReady()) {
  // Resources loaded
} else {
  view.ResourceReadySignal().Connect(this, &MyClass::OnResourceReady);
}
```
