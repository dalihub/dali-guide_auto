---
title: View
sidebar_label: View
category: views-components
---

# View

`Dali::Ui::View` is the base app-facing UI object in dali-ui. Use it to build view trees, read layout/render state, and attach interaction or selection behavior without working directly with raw `Dali::Actor`.

## Table of Contents

- [Creating a View](#creating-a-view)
- [Reading View State](#reading-view-state)
- [Working with Child Views](#working-with-child-views)
- [Making a View Interactive](#making-a-view-interactive)
- [Selection State](#selection-state)
- [Animating a View](#animating-a-view)
- [View Property Constants](#view-property-constants)

## Creating a View

Create a base UI object with `Dali::Ui::View::New()`. In dali-ui application code, keep the object model at the `Dali::Ui::View` level and pass `View` handles through your own UI code.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

View CreateBaseView()
{
  View view = View::New();
  return view;
}
```

`Dali::Ui::View` is a handle type, so it is cheap to pass by value. A copied `View` handle refers to the same underlying UI object.

```cpp
void CacheView(View source)
{
  View sameObject = source;

  Dali::String name = sameObject.GetName();
  float opacity = sameObject.GetOpacity();
}
```

## Reading View State

Use typed `View` getters to inspect the state that application code commonly needs after configuration, layout, or animation. For example, `GetCurrentPosition()`, `GetCurrentScale()`, and `GetCurrentSize()` read current rendered values, while `GetMeasuredSize()` reads the measured layout result.

```cpp
void InspectView(View view)
{
  Vector2      position = view.GetCurrentPosition();
  Vector2      scale = view.GetCurrentScale();
  MeasuredSize currentSize = view.GetCurrentSize();
  MeasuredSize measuredSize = view.GetMeasuredSize();

  float opacity = view.GetOpacity();
  UiColor color = view.GetColor();
  UiColor currentColor = view.GetCurrentColor();
}
```

For visual decoration, `View` exposes typed accessors for its own view-level properties. These keep application code readable and avoid raw property access for common view styling state.

```cpp
void InspectDecoration(View view)
{
  UiColor background = view.GetBackgroundColor();

  Vector4 cornerRadius = view.GetCornerRadius();
  Vector4 cornerSquareness = view.GetCornerSquareness();

  float borderlineWidth = view.GetBorderlineWidth();
  UiColor borderlineColor = view.GetBorderlineColor();
  float borderlineOffset = view.GetBorderlineOffset();
}
```

Layout-related getters are also owned by `Dali::Ui::View`. Use them when a component needs to react to the view’s resolved or constrained layout state.

```cpp
void InspectLayoutState(View view)
{
  float minimumWidth = view.GetMinimumWidth();
  float minimumHeight = view.GetMinimumHeight();
  float maximumWidth = view.GetMaximumWidth();
  float maximumHeight = view.GetMaximumHeight();

  Extents margin = view.GetMargin();
  Dali::LayoutDirection::Type direction = view.GetEffectiveLayoutDirection();
}
```

## Working with Child Views

A `View` can be used as the app-facing node in a view tree. When you need to inspect children that are already attached, use `GetChildCount()` and `GetChildAt()` and continue working with `Dali::Ui::View` handles.

```cpp
void VisitChildren(View parent)
{
  const uint32_t childCount = parent.GetChildCount();

  for(uint32_t index = 0u; index < childCount; ++index)
  {
    View child = parent.GetChildAt(index);

    Dali::String childName = child.GetName();
    MeasuredSize childSize = child.GetCurrentSize();
  }
}
```

This keeps traversal code aligned with dali-ui’s object model. The child returned by `GetChildAt()` is a `Dali::Ui::View`, so downstream code can use the same typed getters and traits.

```cpp
View FindFirstVisibleSizedChild(View parent)
{
  for(uint32_t index = 0u; index < parent.GetChildCount(); ++index)
  {
    View child = parent.GetChildAt(index);

    MeasuredSize size = child.GetCurrentSize();
    if(size.width > 0.0f && size.height > 0.0f)
    {
      return child;
    }
  }

  return View();
}
```

## Making a View Interactive

Attach interaction behavior to a `View` through `View::AsInteractive()`, then configure the provided `Dali::Ui::InteractiveTrait`. The trait owns click, long-press, pressed-state, clickable, pseudo-disabled, and key-click policy behavior.

```cpp
class Controller
{
public:
  View CreateClickableView()
  {
    View view = View::New();

    view.AsInteractive([this](InteractiveTrait& trait)
    {
      trait.SetClickable(true);
      trait.ConnectClickedSignal(this, &Controller::OnClicked);
      trait.ConnectPressedChangedSignal(this, &Controller::OnPressedChanged);
    });

    return view;
  }

  void OnClicked(View view, InputEvent event)
  {
    UiColor color = view.GetCurrentColor();
  }

  void OnPressedChanged(View view, bool pressed, InputEvent event)
  {
    float opacity = view.GetOpacity();
  }
};
```

Use `InteractiveTrait::ClickedSignal()` when the view should perform an action on tap. Use `InteractiveTrait::PressedChangedSignal()` when the view needs separate press-down and release handling.

```cpp
class PressController
{
public:
  void Configure(View view)
  {
    view.AsInteractive([this](InteractiveTrait& trait)
    {
      trait.ConnectPressedChangedSignal(this, &PressController::OnPressedChanged);
    });
  }

  void OnPressedChanged(View view, bool pressed, InputEvent event)
  {
    if(pressed)
    {
      Vector2 position = view.GetCurrentPosition();
    }
    else
    {
      MeasuredSize size = view.GetCurrentSize();
    }
  }
};
```

Long press handlers use `InteractiveTrait::ConnectLongPressedSignal()` and return `bool`. Returning `true` consumes the long press and suppresses the later click emission.

```cpp
class LongPressController
{
public:
  void Configure(View view)
  {
    view.AsInteractive([this](InteractiveTrait& trait)
    {
      trait.ConnectLongPressedSignal(this, &LongPressController::OnLongPressed);
      trait.ConnectClickedSignal(this, &LongPressController::OnClicked);
    });
  }

  bool OnLongPressed(View view, InputEvent event)
  {
    return true;
  }

  void OnClicked(View view, InputEvent event)
  {
  }
};
```

`InteractiveTrait` also lets you query and adjust interaction state.

```cpp
void UpdateInteractionState(InteractiveTrait trait)
{
  bool clickable = trait.IsClickable();
  bool pressed = trait.IsPressed();
  bool pseudoDisabled = trait.IsPseudoDisabled();

  trait.SetClickable(!pressed);
  trait.SetPseudoDisabled(pseudoDisabled);
}
```

## Selection State

Use `Dali::Ui::SelectableTrait` for boolean selection state. It owns `SetSelected()`, `IsSelected()`, `SelectionChangedSignal()`, and optional toggle-by-click behavior.

```cpp
class SelectionController
{
public:
  void Configure(View view)
  {
    view.AsSelectable([this](SelectableTrait& trait)
    {
      trait.SetSelected(false);
      trait.EnableToggleByClick(true);
      trait.SelectionChangedSignal().Connect(this, &SelectionController::OnSelectionChanged);
    });
  }

  void OnSelectionChanged(View view, bool selected, InputEvent event)
  {
    UiColor currentColor = view.GetCurrentColor();
  }
};
```

`SelectableTrait::EnableToggleByClick()` makes click input toggle the selected state. If you need to inspect state without changing it, use `IsSelected()` and `IsToggleByClickEnabled()`.

```cpp
void SyncSelectionState(SelectableTrait trait)
{
  bool selected = trait.IsSelected();
  bool togglesByClick = trait.IsToggleByClickEnabled();

  if(!selected && togglesByClick)
  {
    trait.SetSelected(true);
  }
}
```

## Animating a View

Animate the `View` itself with the dali-ui app-facing animation API. `View::Animate()` creates a view animation bridge for one target view, and `View::NewAnimationSpec()` creates a reusable animation spec.

```cpp
class FadeController
{
public:
  void FadeOut(View view)
  {
    Animation animation = Animation::New();

    view.Animate(animation)
      .Opacity(0.0f, 300_ms);

    animation.Play();
  }
};
```

For reusable animations, build a `ViewAnimationSpec` from `View::NewAnimationSpec()` and apply it to a target `View`.

```cpp
class FadeInController
{
public:
  FadeInController()
  : mFadeInSpec(View::NewAnimationSpec()
      .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT))
  {
  }

  void FadeIn(View view)
  {
    Animation animation = Animation::New();

    mFadeInSpec.ApplyTo(animation, view);

    animation.Play();
  }

private:
  ViewAnimationSpec mFadeInSpec;
};
```

When referring to inherited transform or opacity property constants for animation-related metadata, use the owning DALi property owner. For example, opacity, position, and scale constants are owned by `Dali::Actor::Property`, while view-specific decoration constants are owned by `Dali::Ui::View::Property`.

## View Property Constants

Use `Dali::Ui::View::Property` for view-owned property constants. These constants identify view-level state such as accessibility metadata, automation identity, focus routing, background, borderline, corner radius, layout mode, margin, requested size, shadow, and offscreen rendering.

```cpp
Property::Index backgroundProperty = View::Property::BACKGROUND;
Property::Index borderlineColorProperty = View::Property::BORDERLINE_COLOR;
Property::Index cornerRadiusProperty = View::Property::CORNER_RADIUS;
Property::Index focusGroupProperty = View::Property::FOCUS_GROUP;
Property::Index automationIdProperty = View::Property::AUTOMATION_ID;
```

Accessibility-related constants are also owned by `Dali::Ui::View::Property`.

```cpp
Property::Index accessibilityName = View::Property::ACCESSIBILITY_NAME;
Property::Index accessibilityDescription = View::Property::ACCESSIBILITY_DESCRIPTION;
Property::Index accessibilityRole = View::Property::ACCESSIBILITY_ROLE;
Property::Index accessibilityHidden = View::Property::ACCESSIBILITY_HIDDEN;
Property::Index accessibilityStates = View::Property::ACCESSIBILITY_STATES;
```

For inherited actor-level constants, use the owner shown by the API. For example, `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION`, and `Dali::Actor::Property::SCALE` are actor property constants even when they are relevant to a `View` animation.
