---
title: View (Base UI Object)
sidebar_label: View (Base UI Object)
category: views-components
---

# View (Base UI Object)

`Dali::Ui::View` is the base app-facing UI object for dali-ui applications.

## Table of Contents

- [Creating a View](#creating-a-view)
- [Working with View Trees](#working-with-view-trees)
- [Reading Visual State](#reading-visual-state)
- [Layout and Measurement State](#layout-and-measurement-state)
- [Interaction with Traits](#interaction-with-traits)
- [Selection State](#selection-state)
- [Animation Entry Point](#animation-entry-point)
- [Property Ownership](#property-ownership)

## Creating a View

Use `Dali::Ui::View::New()` when you need a generic UI object. In dali-ui app code, keep `Dali::Ui::View` as the object model you pass around for containers, controls, and reusable view helpers.

```cpp
#include <dali-ui/dali-ui.h>

Dali::Ui::View CreatePanel()
{
  Dali::Ui::View panel = Dali::Ui::View::New();

  return panel;
}
```

A `Dali::Ui::View` can also be passed to helper functions without exposing a lower-level actor model.

```cpp
void InspectViewIdentity(const Dali::Ui::View& view)
{
  Dali::String name = view.GetName();
  float opacity = view.GetOpacity();
  Dali::Ui::UiColor color = view.GetColor();

  (void)name;
  (void)opacity;
  (void)color;
}
```

## Working with View Trees

Use `Dali::Ui::View::GetChildCount()` and `Dali::Ui::View::GetChildAt()` to inspect a view tree from application code. This keeps traversal expressed in dali-ui terms.

```cpp
void VisitImmediateChildren(const Dali::Ui::View& parent)
{
  const uint32_t count = parent.GetChildCount();

  for(uint32_t index = 0u; index < count; ++index)
  {
    Dali::Ui::View child = parent.GetChildAt(index);

    Dali::String childName = child.GetName();
    Dali::Ui::MeasuredSize childSize = child.GetCurrentSize();

    (void)childName;
    (void)childSize;
  }
}
```

For utility code, prefer accepting `Dali::Ui::View` directly instead of requiring callers to convert to another object type.

```cpp
Dali::Ui::View FindFirstVisibleSizedChild(const Dali::Ui::View& parent)
{
  for(uint32_t index = 0u; index < parent.GetChildCount(); ++index)
  {
    Dali::Ui::View child = parent.GetChildAt(index);

    if(child.GetOpacity() > 0.0f)
    {
      return child;
    }
  }

  return Dali::Ui::View();
}
```

## Reading Visual State

`Dali::Ui::View` owns the app-facing accessors for common visual state such as color, background, opacity, border, and corner styling.

```cpp
struct ViewVisualSnapshot
{
  Dali::Ui::UiColor color;
  Dali::Ui::UiColor currentColor;
  Dali::Ui::UiColor backgroundColor;
  Dali::Ui::UiColor borderlineColor;
  float borderlineWidth;
  float borderlineOffset;
  Dali::Vector4 cornerRadius;
  Dali::Vector4 cornerSquareness;
};

ViewVisualSnapshot CaptureVisualState(Dali::Ui::View view)
{
  ViewVisualSnapshot snapshot;
  snapshot.color             = view.GetColor();
  snapshot.currentColor      = view.GetCurrentColor();
  snapshot.backgroundColor   = view.GetBackgroundColor();
  snapshot.borderlineColor   = view.GetBorderlineColor();
  snapshot.borderlineWidth   = view.GetBorderlineWidth();
  snapshot.borderlineOffset  = view.GetBorderlineOffset();
  snapshot.cornerRadius      = view.GetCornerRadius();
  snapshot.cornerSquareness  = view.GetCornerSquareness();

  return snapshot;
}
```

Use `Dali::Ui::View::GetCornerRadiusPolicy()` with `Dali::Ui::View::GetCornerRadius()` when layout or rendering code needs to interpret corner values consistently.

```cpp
void ReadCornerConfiguration(const Dali::Ui::View& view)
{
  Dali::Vector4 radius = view.GetCornerRadius();
  Dali::Ui::CornerRadiusPolicy policy = view.GetCornerRadiusPolicy();

  (void)radius;
  (void)policy;
}
```

## Layout and Measurement State

A view exposes measured, current, and constraint-related layout state through `Dali::Ui::View` accessors. This is useful after layout has produced concrete values.

```cpp
struct ViewLayoutSnapshot
{
  Dali::Ui::MeasuredSize measuredSize;
  Dali::Ui::MeasuredSize currentSize;
  Dali::Vector2 currentPosition;
  Dali::Vector2 currentScale;
  float currentScaleX;
  float currentScaleY;
  Dali::Ui::Extents margin;
};

ViewLayoutSnapshot CaptureLayoutState(const Dali::Ui::View& view)
{
  ViewLayoutSnapshot snapshot;
  snapshot.measuredSize    = view.GetMeasuredSize();
  snapshot.currentSize     = view.GetCurrentSize();
  snapshot.currentPosition = view.GetCurrentPosition();
  snapshot.currentScale    = view.GetCurrentScale();
  snapshot.currentScaleX   = view.GetCurrentScaleX();
  snapshot.currentScaleY   = view.GetCurrentScaleY();
  snapshot.margin          = view.GetMargin();

  return snapshot;
}
```

Minimum and maximum dimensions are also view-level state.

```cpp
bool HasWidthLimits(const Dali::Ui::View& view)
{
  const float minimumWidth = view.GetMinimumWidth();
  const float maximumWidth = view.GetMaximumWidth();

  return minimumWidth > 0.0f || maximumWidth > 0.0f;
}

bool HasHeightLimits(const Dali::Ui::View& view)
{
  const float minimumHeight = view.GetMinimumHeight();
  const float maximumHeight = view.GetMaximumHeight();

  return minimumHeight > 0.0f || maximumHeight > 0.0f;
}
```

For layout-aware code, `Dali::Ui::View::GetLayoutMode()`, `Dali::Ui::View::GetLayoutParams()`, and `Dali::Ui::View::GetEffectiveLayoutDirection()` keep the query on the view itself.

```cpp
template<typename LayoutParams>
LayoutParams ReadTypedLayoutParams(const Dali::Ui::View& view)
{
  return view.GetLayoutParams<LayoutParams>();
}

void ReadLayoutDirection(const Dali::Ui::View& view)
{
  Dali::Ui::LayoutMode mode = view.GetLayoutMode();
  Dali::LayoutDirection::Type direction = view.GetEffectiveLayoutDirection();

  (void)mode;
  (void)direction;
}
```

## Interaction with Traits

Use `Dali::Ui::InteractiveTrait` when a view should respond to input. The trait owns click, press, long-press, pseudo-disabled, and key-click behavior.

```cpp
class ButtonController
{
public:
  void Attach(Dali::Ui::InteractiveTrait interactive)
  {
    interactive.SetClickable(true);
    interactive.ConnectClickedSignal(this, &ButtonController::OnClicked);
    interactive.ConnectPressedChangedSignal(this, &ButtonController::OnPressedChanged);
  }

private:
  void OnClicked(Dali::Ui::View view, Dali::InputEvent event)
  {
    Dali::String name = view.GetName();

    (void)name;
    (void)event;
  }

  void OnPressedChanged(Dali::Ui::View view, bool pressed, Dali::InputEvent event)
  {
    float opacity = view.GetOpacity();

    (void)pressed;
    (void)opacity;
    (void)event;
  }
};
```

`Dali::Ui::InteractiveTrait` can be queried for current interaction state.

```cpp
void UpdateInteractionState(Dali::Ui::InteractiveTrait interactive)
{
  const bool clickable = interactive.IsClickable();
  const bool pressed = interactive.IsPressed();
  const bool pseudoDisabled = interactive.IsPseudoDisabled();

  if(clickable && !pseudoDisabled)
  {
    Dali::Ui::InteractiveTrait::ClickedSignalType& clicked = interactive.ClickedSignal();
    (void)clicked;
  }

  (void)pressed;
}
```

Use long-press and pseudo-disabled signals when the interaction model needs more than a simple click.

```cpp
class HoldController
{
public:
  void Attach(Dali::Ui::InteractiveTrait interactive)
  {
    interactive.ConnectLongPressedSignal(this, &HoldController::OnLongPressed);
    interactive.PseudoDisabledChangedSignal().Connect(this, &HoldController::OnPseudoDisabledChanged);
  }

private:
  bool OnLongPressed(Dali::Ui::View view, Dali::InputEvent event)
  {
    Dali::Ui::MeasuredSize size = view.GetCurrentSize();

    (void)size;
    (void)event;
    return true;
  }

  void OnPseudoDisabledChanged(Dali::Ui::View view, bool disabled)
  {
    Dali::Ui::UiColor color = view.GetCurrentColor();

    (void)view;
    (void)disabled;
    (void)color;
  }
};
```

## Selection State

Use `Dali::Ui::SelectableTrait` when a view has selected or unselected state. This keeps selection behavior separate from the base view object.

```cpp
class SelectionController
{
public:
  void Attach(Dali::Ui::SelectableTrait selectable)
  {
    selectable.EnableToggleByClick(true);
    selectable.SelectionChangedSignal().Connect(this, &SelectionController::OnSelectionChanged);
  }

  void Select(Dali::Ui::SelectableTrait selectable)
  {
    selectable.SetSelected(true);
  }

private:
  void OnSelectionChanged(Dali::Ui::View view, bool selected, Dali::InputEvent event)
  {
    Dali::String name = view.GetName();

    (void)name;
    (void)selected;
    (void)event;
  }
};
```

Selection state can be inspected without changing the view.

```cpp
bool IsUserToggleSelection(Dali::Ui::SelectableTrait selectable)
{
  return selectable.IsSelected() && selectable.IsToggleByClickEnabled();
}
```

## Animation Entry Point

Create view animation configuration from the view with `Dali::Ui::View::NewAnimationSpec()`. When animating view transform or opacity, the inherited property enums such as `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION`, and `Dali::Actor::Property::SCALE` are owned by `Dali::Actor::Property`, while the app-facing animated object remains `Dali::Ui::View`.

```cpp
void PrepareAnimation(Dali::Ui::View view)
{
  Dali::Ui::ViewAnimationSpec spec = view.NewAnimationSpec();

  (void)spec;
}
```

When using dali-ui animation APIs, keep the view as the target object.

```cpp
void FadeView(Dali::Ui::View view)
{
  Dali::Ui::ViewAnimationSpec spec = view.NewAnimationSpec();

  view.Animate(spec, Dali::Actor::Property::OPACITY);

  (void)spec;
}
```

## Property Ownership

`Dali::Ui::View::Property` owns view-specific property identifiers. Use these names when referring to view behavior, accessibility, focus navigation, visual styling, and layout policy.

```cpp
void ReferenceViewProperties()
{
  auto backgroundProperty = Dali::Ui::View::Property::BACKGROUND;
  auto borderlineProperty = Dali::Ui::View::Property::BORDERLINE;
  auto cornerRadiusProperty = Dali::Ui::View::Property::CORNER_RADIUS;
  auto focusGroupProperty = Dali::Ui::View::Property::FOCUS_GROUP;
  auto automationIdProperty = Dali::Ui::View::Property::AUTOMATION_ID;
  auto accessibilityNameProperty = Dali::Ui::View::Property::ACCESSIBILITY_NAME;

  (void)backgroundProperty;
  (void)borderlineProperty;
  (void)cornerRadiusProperty;
  (void)focusGroupProperty;
  (void)automationIdProperty;
  (void)accessibilityNameProperty;
}
```

Use `Dali::Ui::View::Property` for these view-owned areas:

- Visuals: `Dali::Ui::View::Property::BACKGROUND`, `Dali::Ui::View::Property::BORDERLINE`, `Dali::Ui::View::Property::BORDERLINE_COLOR`, `Dali::Ui::View::Property::BORDERLINE_WIDTH`, `Dali::Ui::View::Property::BORDERLINE_OFFSET`, `Dali::Ui::View::Property::CORNER_RADIUS`, `Dali::Ui::View::Property::CORNER_RADIUS_POLICY`, `Dali::Ui::View::Property::CORNER_SQUARENESS`
- Layout: `Dali::Ui::View::Property::LAYOUT_MODE`, `Dali::Ui::View::Property::MARGIN`, `Dali::Ui::View::Property::PADDING`, `Dali::Ui::View::Property::MINIMUM_WIDTH`, `Dali::Ui::View::Property::MINIMUM_HEIGHT`, `Dali::Ui::View::Property::MAXIMUM_WIDTH`, `Dali::Ui::View::Property::MAXIMUM_HEIGHT`
- Focus: `Dali::Ui::View::Property::FOCUS_GROUP`, `Dali::Ui::View::Property::LEFT_FOCUSABLE_VIEW_ID`, `Dali::Ui::View::Property::RIGHT_FOCUSABLE_VIEW_ID`, `Dali::Ui::View::Property::UP_FOCUSABLE_VIEW_ID`, `Dali::Ui::View::Property::DOWN_FOCUSABLE_VIEW_ID`, `Dali::Ui::View::Property::FORWARD_FOCUSABLE_VIEW_ID`, `Dali::Ui::View::Property::BACKWARD_FOCUSABLE_VIEW_ID`
- Accessibility: `Dali::Ui::View::Property::ACCESSIBILITY_NAME`, `Dali::Ui::View::Property::ACCESSIBILITY_DESCRIPTION`, `Dali::Ui::View::Property::ACCESSIBILITY_ROLE`, `Dali::Ui::View::Property::ACCESSIBILITY_VALUE`, `Dali::Ui::View::Property::ACCESSIBILITY_STATES`, `Dali::Ui::View::Property::ACCESSIBILITY_ATTRIBUTES`, `Dali::Ui::View::Property::ACCESSIBILITY_HIDDEN`, `Dali::Ui::View::Property::ACCESSIBILITY_HIGHLIGHTABLE`, `Dali::Ui::View::Property::ACCESSIBILITY_SCROLLABLE`, `Dali::Ui::View::Property::ACCESSIBILITY_IS_MODAL`

Use `Dali::Actor::Property` only for inherited core property identifiers, such as `Dali::Actor::Property::NAME`, `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION`, `Dali::Actor::Property::SCALE`, `Dali::Actor::Property::MINIMUM_SIZE`, and `Dali::Actor::Property::MAXIMUM_SIZE`. The app-facing object should still be `Dali::Ui::View`.
