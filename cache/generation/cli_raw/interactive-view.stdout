---
title: Interactive View
sidebar_label: Interactive View
category: views-components
---

# Interactive View

`Dali::Ui::InteractiveView` is a `Dali::Ui::View`-based app-facing object for clickable UI regions, pressed state tracking, long press handling, and key-driven click behavior.

## Table of Contents

- [Create an Interactive View](#create-an-interactive-view)
- [Handle Clicks](#handle-clicks)
- [Track Pressed and Pseudo Disabled State](#track-pressed-and-pseudo-disabled-state)
- [Configure Key Click Behavior](#configure-key-click-behavior)
- [Use Chaining in Derived Components](#use-chaining-in-derived-components)

## Create an Interactive View

Create an interactive region with `Dali::Ui::InteractiveView::New()`. The returned handle is a `Dali::Ui::View` subclass, so application code can keep working with the dali-ui view model while using the direct interactive API exposed by `Dali::Ui::InteractiveView`.

`SetClickable()` controls whether the view emits click interactions. `IsClickable()` reads the current clickable state.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::InteractiveView CreateInteractiveRegion()
{
  Dali::Ui::InteractiveView view = Dali::Ui::InteractiveView::New();

  view
    .SetClickable(true)
    .SetPseudoDisabled(false);

  bool clickable = view.IsClickable();
  bool pseudoDisabled = view.IsPseudoDisabled();

  return view;
}
```

Use `Dali::Ui::InteractiveView::DownCast()` when application code receives a base handle and needs the interactive API only if the handle is actually an interactive view.

```cpp
Dali::Ui::InteractiveView AsInteractiveView(Dali::BaseHandle handle)
{
  Dali::Ui::InteractiveView view = Dali::Ui::InteractiveView::DownCast(handle);

  if(view)
  {
    view.SetClickable(true);
  }

  return view;
}
```

## Handle Clicks

`ClickedSignal()` is emitted when the view is clicked. The signal type is `Dali::Signal<void(Dali::Ui::View, Dali::Ui::InputEvent)>`, so callbacks receive the clicked `Dali::Ui::View` and the associated `Dali::Ui::InputEvent`.

For application code, `ConnectClickedSignal()` is the concise form. It connects a handler and returns the same `Dali::Ui::InteractiveView`, which makes it convenient in fluent setup code.

```cpp
class Controller
{
public:
  Dali::Ui::InteractiveView CreateButton()
  {
    Dali::Ui::InteractiveView button = Dali::Ui::InteractiveView::New();

    button
      .SetClickable(true)
      .ConnectClickedSignal(this, &Controller::OnClicked);

    return button;
  }

private:
  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mLastClicked = view;
  }

  Dali::Ui::View mLastClicked;
};
```

You can also connect through `ClickedSignal()` directly when you want to keep the signal object visible in the code.

```cpp
class Controller
{
public:
  void Attach(Dali::Ui::InteractiveView view)
  {
    view.ClickedSignal().Connect(this, &Controller::OnClicked);
  }

private:
  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mClicked = true;
  }

  bool mClicked{false};
};
```

Multiple click handlers can be connected to the same `Dali::Ui::InteractiveView`. Each call to `ConnectClickedSignal()` adds another handler.

```cpp
class Controller
{
public:
  void AttachHandlers(Dali::Ui::InteractiveView view)
  {
    view
      .ConnectClickedSignal(this, &Controller::UpdateSelection)
      .ConnectClickedSignal(this, &Controller::ClosePopup);
  }

private:
  void UpdateSelection(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mSelected = view;
  }

  void ClosePopup(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mPopupOpen = false;
  }

  Dali::Ui::View mSelected;
  bool mPopupOpen{true};
};
```

## Track Pressed and Pseudo Disabled State

`PressedChangedSignal()` reports transitions into and out of the pressed state. The callback receives the `Dali::Ui::View`, a `bool` pressed value, and the `Dali::Ui::InputEvent`.

Use `IsPressed()` when the current state is needed outside the signal callback.

```cpp
class PressController
{
public:
  Dali::Ui::InteractiveView CreatePressableView()
  {
    Dali::Ui::InteractiveView view = Dali::Ui::InteractiveView::New();

    view
      .SetClickable(true)
      .ConnectPressedChangedSignal(this, &PressController::OnPressedChanged);

    return view;
  }

private:
  void OnPressedChanged(Dali::Ui::View view, bool pressed, Dali::Ui::InputEvent event)
  {
    mPressed = pressed;
  }

  bool mPressed{false};
};
```

`SetPseudoDisabled()` sets the pseudo disabled state. This is separate from removing clickability with `SetClickable(false)`: pseudo disabled is a state exposed by `IsPseudoDisabled()` and `PseudoDisabledChangedSignal()`.

```cpp
class StateController
{
public:
  void Attach(Dali::Ui::InteractiveView view)
  {
    view.PseudoDisabledChangedSignal().Connect(
      this,
      &StateController::OnPseudoDisabledChanged);

    view.SetPseudoDisabled(true);

    bool pseudoDisabled = view.IsPseudoDisabled();
  }

private:
  void OnPseudoDisabledChanged(Dali::Ui::View view, bool pseudoDisabled)
  {
    mPseudoDisabled = pseudoDisabled;
  }

  bool mPseudoDisabled{false};
};
```

A common pattern is to use pseudo disabled for visual state while leaving explicit control over click handling through `SetClickable()`.

```cpp
void SetTemporaryUnavailable(Dali::Ui::InteractiveView view, bool unavailable)
{
  view
    .SetPseudoDisabled(unavailable)
    .SetClickable(!unavailable);
}
```

## Configure Key Click Behavior

`SetKeyClickPolicy()` controls when key input produces click behavior. The public `Dali::Ui::KeyClickPolicy` values are:

- `Dali::Ui::KeyClickPolicy::ON_RELEASE`: trigger the click when the key is released.
- `Dali::Ui::KeyClickPolicy::ON_PRESS`: trigger the click when the key is pressed.
- `Dali::Ui::KeyClickPolicy::DISABLED`: disable click events from key input.

Use `GetKeyClickPolicy()` to inspect the current policy.

```cpp
Dali::Ui::InteractiveView CreateRemoteFriendlyView()
{
  Dali::Ui::InteractiveView view = Dali::Ui::InteractiveView::New();

  view
    .SetClickable(true)
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE);

  Dali::Ui::KeyClickPolicy policy = view.GetKeyClickPolicy();

  return view;
}
```

For controls that should respond immediately to a key press, use `Dali::Ui::KeyClickPolicy::ON_PRESS`.

```cpp
void UseImmediateKeyClick(Dali::Ui::InteractiveView view)
{
  view.SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_PRESS);
}
```

For a touch-only interactive region, keep pointer click handling enabled and disable key-generated clicks.

```cpp
void UsePointerOnlyClick(Dali::Ui::InteractiveView view)
{
  view
    .SetClickable(true)
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::DISABLED);
}
```

## Handle Long Press

`LongPressedSignal()` is emitted when the view receives a long press gesture. Its signal type is `Dali::Signal<bool(Dali::Ui::View, Dali::Ui::InputEvent)>`.

Use `ConnectLongPressedSignal()` for fluent setup. The callback returns `true` to consume the long press and suppress the subsequent click, or `false` to allow the click path to continue.

```cpp
class MenuController
{
public:
  Dali::Ui::InteractiveView CreateMenuTarget()
  {
    Dali::Ui::InteractiveView target = Dali::Ui::InteractiveView::New();

    target
      .SetClickable(true)
      .ConnectLongPressedSignal(this, &MenuController::OnLongPressed)
      .ConnectClickedSignal(this, &MenuController::OnClicked);

    return target;
  }

private:
  bool OnLongPressed(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mMenuOpen = true;
    return true;
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mClicked = true;
  }

  bool mMenuOpen{false};
  bool mClicked{false};
};
```

Connect to `LongPressedSignal()` directly when you prefer the explicit signal form.

```cpp
class MenuController
{
public:
  void Attach(Dali::Ui::InteractiveView view)
  {
    view.LongPressedSignal().Connect(this, &MenuController::OnLongPressed);
  }

private:
  bool OnLongPressed(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mHandledLongPress = true;
    return false;
  }

  bool mHandledLongPress{false};
};
```

## Use Chaining in Derived Components

`interactive-view.autogen.h` provides `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS` for dali-ui component classes that derive from `Dali::Ui::InteractiveView`. The macro adds chain-returning wrappers for interactive methods such as `SetClickable()`, `SetPseudoDisabled()`, `SetKeyClickPolicy()`, `ConnectClickedSignal()`, `ConnectPressedChangedSignal()`, and `ConnectLongPressedSignal()` so derived component setup remains fluent.

```cpp
class IconButton : public Dali::Ui::InteractiveView
{
public:
  DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS(IconButton)
};
```

With the chain methods available on the derived type, app code can configure the component without dropping back to the base handle type.

```cpp
class ToolbarController
{
public:
  IconButton CreateToolbarButton()
  {
    IconButton button;

    button
      .SetClickable(true)
      .SetPseudoDisabled(false)
      .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE)
      .ConnectClickedSignal(this, &ToolbarController::OnToolbarButtonClicked);

    return button;
  }

private:
  void OnToolbarButtonClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mActivated = true;
  }

  bool mActivated{false};
};
```
