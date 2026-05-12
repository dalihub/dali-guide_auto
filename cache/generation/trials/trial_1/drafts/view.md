---
title: View
sidebar_label: View
category: views-components
---

# View

`Dali::Ui::View` is the base app-facing UI object in dali-ui: use it to represent view objects, inspect layout results, attach interaction behavior, and drive View-first animation.

## Table of Contents

- [Creating and Passing Views](#creating-and-passing-views)
- [Working With View Trees](#working-with-view-trees)
- [Reading Layout and Visual State](#reading-layout-and-visual-state)
- [Adding Click and Press Behavior](#adding-click-and-press-behavior)
- [Managing Selection State](#managing-selection-state)
- [Animating a View](#animating-a-view)
- [Property Owner Rules](#property-owner-rules)

## Creating and Passing Views

Create a base UI object with `Dali::Ui::View::New()`. In application code, keep APIs typed as `Dali::Ui::View` when the logic works with any dali-ui view or component.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

View CreatePanelView()
{
  View panel = View::New();
  return panel;
}

void CacheView(View view)
{
  Dali::String name = view.GetName();
  UiColor color = view.GetColor();
  UiColor currentColor = view.GetCurrentColor();
}
```

Use `Dali::Ui::View` as the app-facing object model instead of raw actor handles. Downcast only when an API gives you a generic handle and you need to recover a view handle.

```cpp
View ResolveView(BaseHandle handle)
{
  return View::DownCast(handle);
}
```

## Working With View Trees

A `Dali::Ui::View` owns child views through the dali-ui view tree. `GetChildCount()` reports the number of direct child views, and `GetChildAt()` returns a child as another `Dali::Ui::View`.

```cpp
void VisitDirectChildren(View parent)
{
  const uint32_t count = parent.GetChildCount();

  for(uint32_t index = 0u; index < count; ++index)
  {
    View child = parent.GetChildAt(index);

    MeasuredSize currentSize = child.GetCurrentSize();
    Vector2 currentPosition = child.GetCurrentPosition();
    float opacity = child.GetOpacity();
  }
}
```

This keeps traversal code independent of the concrete component type. For example, a layout, a reusable component, or a simple visual container can all be passed around as `Dali::Ui::View` when the code only needs child traversal or common view state.

## Reading Layout and Visual State

`Dali::Ui::View` exposes layout results and common visual state through typed getters. Use these getters to inspect the current arranged state rather than reading raw actor properties.

```cpp
struct ViewSnapshot
{
  MeasuredSize currentSize;
  MeasuredSize measuredSize;
  Vector2 currentPosition;
  Vector2 currentScale;
  float currentScaleX;
  float currentScaleY;
  float opacity;
};

ViewSnapshot CaptureSnapshot(View view)
{
  ViewSnapshot snapshot;
  snapshot.currentSize = view.GetCurrentSize();
  snapshot.measuredSize = view.GetMeasuredSize();
  snapshot.currentPosition = view.GetCurrentPosition();
  snapshot.currentScale = view.GetCurrentScale();
  snapshot.currentScaleX = view.GetCurrentScaleX();
  snapshot.currentScaleY = view.GetCurrentScaleY();
  snapshot.opacity = view.GetOpacity();
  return snapshot;
}
```

For shape and color styling state, read the corresponding `Dali::Ui::View` properties through the typed getters.

```cpp
struct ViewStyleState
{
  UiColor backgroundColor;
  UiColor borderColor;
  float borderWidth;
  float borderOffset;
  Vector4 cornerRadius;
  Vector4 cornerSquareness;
  CornerRadiusPolicy cornerRadiusPolicy;
};

ViewStyleState ReadStyleState(View view)
{
  ViewStyleState state;
  state.backgroundColor = view.GetBackgroundColor();
  state.borderColor = view.GetBorderlineColor();
  state.borderWidth = view.GetBorderlineWidth();
  state.borderOffset = view.GetBorderlineOffset();
  state.cornerRadius = view.GetCornerRadius();
  state.cornerSquareness = view.GetCornerSquareness();
  state.cornerRadiusPolicy = view.GetCornerRadiusPolicy();
  return state;
}
```

Layout direction is also read from the view layer. `GetEffectiveLayoutDirection()` returns the resolved direction after inheritance has been applied.

```cpp
bool IsRightToLeft(View view)
{
  return view.GetEffectiveLayoutDirection() == Dali::LayoutDirection::RIGHT_TO_LEFT;
}
```

## Adding Click and Press Behavior

Use `Dali::Ui::InteractiveTrait` for input behavior on a view. The trait owns click, long-press, pressed-state, pseudo-disabled, clickable, and key-click-policy behavior.

In app code, obtain or configure the trait from the view-facing interaction path, then connect the trait signals.

```cpp
class ButtonController
{
public:
  void Configure(View button)
  {
    button.AsInteractive([this](InteractiveTrait& trait)
    {
      trait.SetClickable(true);
      trait.ConnectClickedSignal(this, &ButtonController::OnClicked);
      trait.ConnectPressedChangedSignal(this, &ButtonController::OnPressedChanged);
      trait.ConnectLongPressedSignal(this, &ButtonController::OnLongPressed);
    });
  }

  void OnClicked(View view, InputEvent event)
  {
    UiColor color = view.GetCurrentColor();
  }

  void OnPressedChanged(View view, bool pressed, InputEvent event)
  {
    float opacity = view.GetOpacity();
  }

  bool OnLongPressed(View view, InputEvent event)
  {
    return true;
  }
};
```

Use `IsClickable()` and `SetClickable()` when the view should temporarily ignore click activation while still remaining part of the UI. Use `SetPseudoDisabled()` when the view should present a disabled-like state while still accepting interaction.

```cpp
void UpdateInteractionAvailability(InteractiveTrait trait, bool actionAvailable)
{
  trait.SetClickable(actionAvailable);
  trait.SetPseudoDisabled(!actionAvailable);
}

bool CanActivate(InteractiveTrait trait)
{
  return trait.IsClickable() && !trait.IsPseudoDisabled();
}
```

For key-driven activation, `GetKeyClickPolicy()` and `SetKeyClickPolicy()` belong to `Dali::Ui::InteractiveTrait`.

```cpp
void CopyKeyPolicy(InteractiveTrait source, InteractiveTrait target)
{
  target.SetKeyClickPolicy(source.GetKeyClickPolicy());
}
```

## Managing Selection State

Use `Dali::Ui::SelectableTrait` when a view needs selected/unselected state. The trait owns the selected state, optional toggle-by-click behavior, and `SelectionChangedSignal()`.

```cpp
class SelectionController
{
public:
  void Configure(SelectableTrait trait)
  {
    trait.SetSelected(false);
    trait.EnableToggleByClick(true);
    trait.SelectionChangedSignal().Connect(this, &SelectionController::OnSelectionChanged);
  }

  void OnSelectionChanged(View view, bool selected, InputEvent event)
  {
    mLastSelected = selected;
  }

private:
  bool mLastSelected{false};
};
```

Use `IsSelected()` for the current state and `IsToggleByClickEnabled()` to query whether click toggling is enabled.

```cpp
bool ShouldShowSelectedState(SelectableTrait trait)
{
  return trait.IsSelected();
}

void EnsureClickToggle(SelectableTrait trait)
{
  if(!trait.IsToggleByClickEnabled())
  {
    trait.EnableToggleByClick(true);
  }
}
```

`Dali::Ui::InteractiveTrait` and `Dali::Ui::SelectableTrait` are separate traits. A selectable view can use selection state, while an interactive view owns click and pressed-state behavior.

## Animating a View

Animate the `Dali::Ui::View` itself with the dali-ui View-first animation API. The view remains the object being animated.

```cpp
void FadeAndMove(View view)
{
  Animation animation = Animation::New();

  view.Animate(animation)
    .Opacity(0.0f, 300_ms)
    .PositionY(40.0f, 300_ms);

  animation.Play();
}
```

For reusable animation definitions, create a spec with `Dali::Ui::View::NewAnimationSpec()` and apply it to a view.

```cpp
ViewAnimationSpec CreateFadeInSpec()
{
  return View::NewAnimationSpec()
    .Opacity(1.0f, 250_ms);
}

void ApplyFadeIn(View view, ViewAnimationSpec spec)
{
  Animation animation = Animation::New();
  spec.ApplyTo(animation, view);
  animation.Play();
}
```

When referring to inherited transform or opacity property IDs, use the correct owner. `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION`, and `Dali::Actor::Property::SCALE` are actor property identifiers, even when the animated object is a `Dali::Ui::View`.

## Property Owner Rules

`Dali::Ui::View::Property` owns view-specific property identifiers such as accessibility, automation, focus navigation, background, borderline, corner, layout, and sizing properties.

```cpp
void CollectViewPropertyIds()
{
  const Property::Index automation = View::Property::AUTOMATION_ID;
  const Property::Index accessibilityName = View::Property::ACCESSIBILITY_NAME;
  const Property::Index accessibilityRole = View::Property::ACCESSIBILITY_ROLE;
  const Property::Index background = View::Property::BACKGROUND;
  const Property::Index borderlineColor = View::Property::BORDERLINE_COLOR;
  const Property::Index cornerRadius = View::Property::CORNER_RADIUS;
  const Property::Index focusGroup = View::Property::FOCUS_GROUP;
}
```

Use `Dali::Actor::Property` only for inherited actor property identifiers such as opacity, position, scale, name, and layout direction property IDs.

```cpp
void CollectInheritedPropertyIds()
{
  const Property::Index opacity = Dali::Actor::Property::OPACITY;
  const Property::Index position = Dali::Actor::Property::POSITION;
  const Property::Index scale = Dali::Actor::Property::SCALE;
  const Property::Index name = Dali::Actor::Property::NAME;
}
```

For application logic, prefer the typed `Dali::Ui::View` APIs such as `GetOpacity()`, `GetCurrentPosition()`, `GetCurrentScale()`, `GetBackgroundColor()`, `GetBorderlineWidth()`, and `GetCornerRadius()` over raw property access.
