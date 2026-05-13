---
title: View (Base UI Object)
sidebar_label: View (Base UI Object)
category: views-components
---

# View (Base UI Object)

`Dali::Ui::View` is the base app-facing UI object for dali-ui applications.

## Table of Contents

- [Create and Keep View Handles](#create-and-keep-view-handles)
- [Build View Trees](#build-view-trees)
- [Read Layout and Visual State](#read-layout-and-visual-state)
- [Handle Click, Press, and Long Press](#handle-click-press-and-long-press)
- [Use Selectable State](#use-selectable-state)
- [Animate a View](#animate-a-view)

## Create and Keep View Handles

Use `Dali::Ui::View::New` when you need a general-purpose UI object. A `Dali::Ui::View` handle refers to the underlying UI object, so copying the handle keeps another reference to the same view.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

View CreatePanel()
{
  View panel = View::New();
  return panel;
}
```

Store `Dali::Ui::View` handles for views that you will inspect or update later.

```cpp
class PageController
{
public:
  void Create()
  {
    mHeader = View::New();
    mContent = View::New();
  }

  Dali::String HeaderName() const
  {
    return mHeader.GetName();
  }

private:
  View mHeader;
  View mContent;
};
```

`Dali::Ui::View::GetName`, `Dali::Ui::View::GetColor`, `Dali::Ui::View::GetCurrentColor`, and `Dali::Ui::View::GetBackgroundColor` are useful when app code needs to inspect the current view identity or color state.

```cpp
void ReadViewIdentity(View view)
{
  Dali::String name = view.GetName();
  UiColor color = view.GetColor();
  UiColor currentColor = view.GetCurrentColor();
  UiColor background = view.GetBackgroundColor();
}
```

## Build View Trees

Treat `Dali::Ui::View` as the app-facing object model for UI hierarchy work. Parent views expose child lookup through `Dali::Ui::View::GetChildCount` and `Dali::Ui::View::GetChildAt`.

```cpp
void InspectChildren(View container)
{
  const uint32_t childCount = container.GetChildCount();

  for(uint32_t index = 0u; index < childCount; ++index)
  {
    View child = container.GetChildAt(index);
    Dali::String childName = child.GetName();
  }
}
```

Use child handles as `Dali::Ui::View` objects when passing views to helper functions.

```cpp
View FindFirstChild(View container)
{
  if(container.GetChildCount() == 0u)
  {
    return View();
  }

  return container.GetChildAt(0u);
}
```

When code receives a generic handle from framework setup, `Dali::Ui::View::DownCast` can recover a `Dali::Ui::View` only when the handle actually refers to a view.

```cpp
View ResolveView(BaseHandle handle)
{
  View view = View::DownCast(handle);
  return view;
}
```

## Read Layout and Visual State

`Dali::Ui::View` separates requested layout inputs from measured and current results. Use `Dali::Ui::View::GetMeasuredSize` for the last measure pass and `Dali::Ui::View::GetCurrentSize` for the current rendered size.

```cpp
void ReadSizeState(View view)
{
  MeasuredSize measured = view.GetMeasuredSize();
  MeasuredSize current = view.GetCurrentSize();
}
```

For bounds constraints, read `Dali::Ui::View::GetMinimumWidth`, `Dali::Ui::View::GetMinimumHeight`, `Dali::Ui::View::GetMaximumWidth`, and `Dali::Ui::View::GetMaximumHeight`.

```cpp
bool HasWidthRange(View view)
{
  const float minimumWidth = view.GetMinimumWidth();
  const float maximumWidth = view.GetMaximumWidth();

  return minimumWidth <= maximumWidth;
}
```

For placement and scaling, use `Dali::Ui::View::GetCurrentPosition`, `Dali::Ui::View::GetCurrentScale`, `Dali::Ui::View::GetCurrentScaleX`, and `Dali::Ui::View::GetCurrentScaleY`.

```cpp
void ReadTransformState(View view)
{
  Vector2 position = view.GetCurrentPosition();
  Vector2 scale = view.GetCurrentScale();

  const float scaleX = view.GetCurrentScaleX();
  const float scaleY = view.GetCurrentScaleY();
}
```

For layout participation, inspect `Dali::Ui::View::GetLayoutMode`, `Dali::Ui::View::GetMargin`, and `Dali::Ui::View::GetEffectiveLayoutDirection`.

```cpp
void ReadLayoutState(View view)
{
  LayoutMode mode = view.GetLayoutMode();
  Extents margin = view.GetMargin();
  Dali::LayoutDirection::Type direction = view.GetEffectiveLayoutDirection();
}
```

Visual decoration state is available through view-owned APIs such as `Dali::Ui::View::GetCornerRadius`, `Dali::Ui::View::GetCornerRadiusPolicy`, `Dali::Ui::View::GetCornerSquareness`, `Dali::Ui::View::GetBorderlineWidth`, `Dali::Ui::View::GetBorderlineColor`, and `Dali::Ui::View::GetBorderlineOffset`.

```cpp
void ReadDecorationState(View view)
{
  Vector4 radius = view.GetCornerRadius();
  CornerRadiusPolicy radiusPolicy = view.GetCornerRadiusPolicy();
  Vector4 squareness = view.GetCornerSquareness();

  const float borderWidth = view.GetBorderlineWidth();
  UiColor borderColor = view.GetBorderlineColor();
  const float borderOffset = view.GetBorderlineOffset();
}
```

## Handle Click, Press, and Long Press

Attach interaction behavior to a `Dali::Ui::View` through `Dali::Ui::View::AsInteractive`, then configure the resulting `Dali::Ui::InteractiveTrait`. The trait owns click, press, long-press, pseudo-disabled, and key-click behavior for that view.

```cpp
class ButtonController
{
public:
  void Create()
  {
    mButton = View::New();

    mButton.AsInteractive([this](InteractiveTrait& trait)
    {
      trait.SetClickable(true);
      trait.ConnectClickedSignal(this, &ButtonController::OnClicked);
      trait.ConnectPressedChangedSignal(this, &ButtonController::OnPressedChanged);
      trait.ConnectLongPressedSignal(this, &ButtonController::OnLongPressed);
    });
  }

  void OnClicked(View view, InputEvent event)
  {
    mLastClicked = view;
  }

  void OnPressedChanged(View view, bool pressed, InputEvent event)
  {
    mPressed = pressed;
  }

  bool OnLongPressed(View view, InputEvent event)
  {
    return true;
  }

private:
  View mButton;
  View mLastClicked;
  bool mPressed{false};
};
```

`Dali::Ui::InteractiveTrait::ClickedSignal` emits `void(View, InputEvent)`. `Dali::Ui::InteractiveTrait::PressedChangedSignal` emits `void(View, bool, InputEvent)`. `Dali::Ui::InteractiveTrait::LongPressedSignal` emits `bool(View, InputEvent)`, where returning `true` consumes the long press and suppresses the following click.

Use query methods when app state depends on the current interaction state.

```cpp
void ReadInteractionState(InteractiveTrait trait)
{
  const bool clickable = trait.IsClickable();
  const bool pressed = trait.IsPressed();
  const bool pseudoDisabled = trait.IsPseudoDisabled();
  KeyClickPolicy keyClickPolicy = trait.GetKeyClickPolicy();
}
```

Use `Dali::Ui::InteractiveTrait::SetPseudoDisabled` when a view should still receive interaction but present a disabled-like state.

```cpp
void ConfigureGuidedDisabledState(InteractiveTrait trait)
{
  trait.SetClickable(true);
  trait.SetPseudoDisabled(true);
}
```

## Use Selectable State

`Dali::Ui::SelectableTrait` owns selected state for a view. Attach it through `Dali::Ui::View::AsSelectable`, then use `Dali::Ui::SelectableTrait::SetSelected`, `Dali::Ui::SelectableTrait::IsSelected`, and `Dali::Ui::SelectableTrait::SelectionChangedSignal`.

```cpp
class SelectionController
{
public:
  void Create()
  {
    mItem = View::New();

    mItem.AsSelectable([this](SelectableTrait& trait)
    {
      trait.SetSelected(false);
      trait.EnableToggleByClick(true);
      trait.SelectionChangedSignal().Connect(this, &SelectionController::OnSelectionChanged);
    });
  }

  void OnSelectionChanged(View view, bool selected, InputEvent event)
  {
    mSelectedView = view;
    mSelected = selected;
  }

private:
  View mItem;
  View mSelectedView;
  bool mSelected{false};
};
```

When toggle-by-click is enabled with `Dali::Ui::SelectableTrait::EnableToggleByClick`, clicking the owner view toggles the selected state. The selectable trait can coexist with `Dali::Ui::InteractiveTrait` on the same `Dali::Ui::View`.

```cpp
void ConfigureSelectableButton(View view)
{
  view.AsInteractive([](InteractiveTrait& interactive)
  {
    interactive.SetClickable(true);
  });

  view.AsSelectable([](SelectableTrait& selectable)
  {
    selectable.EnableToggleByClick(true);
  });
}
```

Use `Dali::Ui::SelectableTrait::IsToggleByClickEnabled` to check whether click toggling is active.

```cpp
bool IsUserToggleable(SelectableTrait trait)
{
  return trait.IsToggleByClickEnabled();
}
```

## Animate a View

Use `Dali::Ui::View::Animate` when applying animation to a `Dali::Ui::View`. The view remains the object being animated; inherited transform property enums such as `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION`, and `Dali::Actor::Property::SCALE` identify core transform properties when property ownership matters.

```cpp
void FadeAndMove(View view)
{
  Animation animation = Animation::New();

  view.Animate(animation)
    .Opacity(0.3f, 300_ms)
    .PositionY(50.0f, 300_ms, AlphaFunction::EASE_OUT);

  animation.Play();
}
```

Use `Dali::Ui::View::NewAnimationSpec` when the same view animation should be reused for multiple views.

```cpp
class FadeController
{
public:
  void Create()
  {
    mFadeIn = View::NewAnimationSpec()
      .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);
  }

  void Play(View target)
  {
    Animation animation = Animation::New();
    mFadeIn.ApplyTo(animation, target);
    animation.Play();
  }

private:
  ViewAnimationSpec mFadeIn;
};
```

Animation can also target view-owned visual properties such as background color, corner radius, corner squareness, borderline width, borderline color, and borderline offset through the dali-ui animation bridge.

```cpp
void Highlight(View view)
{
  Animation animation = Animation::New();

  view.Animate(animation)
    .BackgroundColor(UiColor::PRIMARY, 250_ms)
    .CornerRadius(Vector4(12.0f, 12.0f, 12.0f, 12.0f), 250_ms)
    .BorderlineWidth(2.0f, 250_ms);

  animation.Play();
}
```
