---
title: Interactive View
sidebar_label: Interactive View
category: views-components
---

# Interactive View

`Dali::Ui::InteractiveView` is the dali-ui `View` type for clickable, press-aware UI surfaces.

## Table of Contents

- [Create an Interactive Surface](#create-an-interactive-surface)
- [Handle Clicks](#handle-clicks)
- [Control Keyboard Click Timing](#control-keyboard-click-timing)
- [Track Pressed and Pseudo Disabled State](#track-pressed-and-pseudo-disabled-state)
- [Handle Long Press](#handle-long-press)
- [Use InteractiveView in Typed Handles](#use-interactiveview-in-typed-handles)

## Create an Interactive Surface

Use `Dali::Ui::InteractiveView::New()` when a plain interactive container should receive interaction events directly. `Dali::Ui::InteractiveView` is clickable by default, and `SetClickable()` lets you enable or suppress click generation without changing the object model.

```cpp
#include <dali-ui-foundation/public-api/interactive-view.h>

class PanelController
{
public:
  void Create()
  {
    Dali::Ui::InteractiveView panel =
      Dali::Ui::InteractiveView::New()
        .SetClickable(true)
        .SetPseudoDisabled(false)
        .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE);

    const bool canClick = panel.IsClickable();
    const bool disabled = panel.IsPseudoDisabled();

    (void)canClick;
    (void)disabled;
  }
};
```

`SetClickable(false)` prevents tap and key-click activation while keeping the `Dali::Ui::InteractiveView` handle and its configured signals intact. Combine it with `SetPseudoDisabled()` when the surface should look unavailable as well as stop click activation.

```cpp
void SetUnavailable(Dali::Ui::InteractiveView view, bool unavailable)
{
  view
    .SetClickable(!unavailable)
    .SetPseudoDisabled(unavailable);
}
```

## Handle Clicks

`ClickedSignal()` returns a `Dali::Signal` with callback shape `void(Dali::Ui::View, Dali::Ui::InputEvent)`. The first argument is the source `Dali::Ui::View`, so handlers can stay written against the app-facing view model.

For concise setup, prefer `ConnectClickedSignal()` on `Dali::Ui::InteractiveView`. It returns the same `Dali::Ui::InteractiveView`, so it fits fluent construction.

```cpp
class ClickController
{
public:
  void Create()
  {
    mButton =
      Dali::Ui::InteractiveView::New()
        .SetClickable(true)
        .ConnectClickedSignal(this, &ClickController::OnClicked);
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    ++mClickCount;

    (void)view;
    (void)event;
  }

private:
  Dali::Ui::InteractiveView mButton;
  int                       mClickCount{0};
};
```

You can also connect directly to `ClickedSignal()` when you want to work with the returned signal object.

```cpp
class DirectSignalController
{
public:
  void Create()
  {
    mButton = Dali::Ui::InteractiveView::New();

    mButton.ClickedSignal().Connect(
      this,
      &DirectSignalController::OnClicked);
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mLastClicked = view;
    (void)event;
  }

private:
  Dali::Ui::InteractiveView mButton;
  Dali::Ui::View            mLastClicked;
};
```

## Control Keyboard Click Timing

`SetKeyClickPolicy()` controls how D-Pad, remote, or keyboard input becomes a click. Use `Dali::Ui::KeyClickPolicy::ON_RELEASE` to emit clicked on release, `Dali::Ui::KeyClickPolicy::ON_PRESS` to emit clicked on press, or `Dali::Ui::KeyClickPolicy::DISABLED` to prevent key input from producing click events.

```cpp
class KeyClickController
{
public:
  void UseReleaseActivation()
  {
    mActionView.SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE);
  }

  void UseImmediateActivation()
  {
    mActionView.SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_PRESS);
  }

  void DisableKeyActivation()
  {
    mActionView.SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::DISABLED);
  }

  bool IsImmediateActivation() const
  {
    return mActionView.GetKeyClickPolicy() == Dali::Ui::KeyClickPolicy::ON_PRESS;
  }

private:
  Dali::Ui::InteractiveView mActionView{Dali::Ui::InteractiveView::New()};
};
```

Keyboard policy and pointer clickability are separate controls. A view can remain pointer-clickable while key-triggered clicks are disabled.

```cpp
Dali::Ui::InteractiveView MakePointerOnlyAction()
{
  return Dali::Ui::InteractiveView::New()
    .SetClickable(true)
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::DISABLED);
}
```

## Track Pressed and Pseudo Disabled State

`PressedChangedSignal()` reports transitions into and out of pressed state with callback shape `void(Dali::Ui::View, bool, Dali::Ui::InputEvent)`. Use `IsPressed()` when later code needs the current state.

```cpp
class PressController
{
public:
  void Create()
  {
    mSurface =
      Dali::Ui::InteractiveView::New()
        .ConnectPressedChangedSignal(this, &PressController::OnPressedChanged);
  }

  void OnPressedChanged(
    Dali::Ui::View       view,
    bool                 pressed,
    Dali::Ui::InputEvent event)
  {
    mPressed = pressed;

    const bool currentState = mSurface.IsPressed();

    (void)view;
    (void)event;
    (void)currentState;
  }

private:
  Dali::Ui::InteractiveView mSurface;
  bool                      mPressed{false};
};
```

`SetPseudoDisabled()` changes a visual pseudo-disabled state, and `PseudoDisabledChangedSignal()` reports that state with callback shape `void(Dali::Ui::View, bool)`. A pseudo-disabled view is presented as disabled but can still accept interaction; when pseudo disabled is set while the view is pressed, the pressed state is cleared.

```cpp
class DisabledStateController
{
public:
  void Create()
  {
    mSurface = Dali::Ui::InteractiveView::New();

    mSurface.PseudoDisabledChangedSignal().Connect(
      this,
      &DisabledStateController::OnPseudoDisabledChanged);
  }

  void SetUnavailable(bool unavailable)
  {
    mSurface.SetPseudoDisabled(unavailable);
  }

  void OnPseudoDisabledChanged(Dali::Ui::View view, bool pseudoDisabled)
  {
    mUnavailable = pseudoDisabled;

    (void)view;
  }

private:
  Dali::Ui::InteractiveView mSurface;
  bool                      mUnavailable{false};
};
```

## Handle Long Press

`LongPressedSignal()` uses callback shape `bool(Dali::Ui::View, Dali::Ui::InputEvent)`. Return `true` when the long press consumes the interaction and should suppress the later click. Return `false` when the click may still be emitted.

```cpp
class LongPressController
{
public:
  void Create()
  {
    mSurface =
      Dali::Ui::InteractiveView::New()
        .ConnectClickedSignal(this, &LongPressController::OnClicked)
        .ConnectLongPressedSignal(this, &LongPressController::OnLongPressed);
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    ++mClickCount;

    (void)view;
    (void)event;
  }

  bool OnLongPressed(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    ++mLongPressCount;

    (void)view;
    (void)event;

    return true;
  }

private:
  Dali::Ui::InteractiveView mSurface;
  int                       mClickCount{0};
  int                       mLongPressCount{0};
};
```

If your long-press action is supplemental rather than exclusive, return `false`.

```cpp
bool ShowPreviewWithoutConsuming(
  Dali::Ui::View       view,
  Dali::Ui::InputEvent event)
{
  (void)view;
  (void)event;

  return false;
}
```

## Use InteractiveView in Typed Handles

`Dali::Ui::InteractiveView::DownCast()` converts a compatible base handle back to `Dali::Ui::InteractiveView`. Use it when a generic handle is known to come from an interactive view-producing API.

```cpp
Dali::Ui::InteractiveView AsInteractiveView(Dali::BaseHandle handle)
{
  Dali::Ui::InteractiveView view =
    Dali::Ui::InteractiveView::DownCast(handle);

  if(view)
  {
    view.SetClickable(true);
  }

  return view;
}
```

The generated header `interactive-view.autogen.h` provides `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS` for dali-ui handle types that derive from `Dali::Ui::InteractiveView`. It forwards interactive setters and signal helpers so derived handles can keep their own fluent return type.

```cpp
class ToolbarAction : public Dali::Ui::InteractiveView
{
public:
  DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS(ToolbarAction)
};
```

Application code normally consumes the resulting typed fluent API through the concrete handle. The inherited methods still map to the `Dali::Ui::InteractiveView` behavior.

```cpp
void ConfigureToolbarAction(ToolbarAction action)
{
  action
    .SetClickable(true)
    .SetPseudoDisabled(false)
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE);
}
```
