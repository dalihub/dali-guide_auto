---
title: Interactive View
sidebar_label: Interactive View
category: views-components
---

# Interactive View

`Dali::Ui::InteractiveView` is the dali-ui view type for app-facing click, press, long-press, and key-triggered interaction.

## Table of Contents

- [Creating an Interactive View](#creating-an-interactive-view)
- [Handling Clicks](#handling-clicks)
- [Tracking Pressed and Disabled Interaction State](#tracking-pressed-and-disabled-interaction-state)
- [Handling Long Press](#handling-long-press)
- [Controlling Key Click Behavior](#controlling-key-click-behavior)
- [Using InteractiveView Handles](#using-interactiveview-handles)
- [Chain Support for Derived Interactive Views](#chain-support-for-derived-interactive-views)

## Creating an Interactive View

Use `Dali::Ui::InteractiveView::New()` when a dali-ui app needs a plain `Dali::Ui::View` object that can emit interaction signals directly. `Dali::Ui::InteractiveView` is a `Dali::Ui::View` subclass, so application code can keep working with the dali-ui view object model while adding built-in interaction behavior.

```cpp
using namespace Dali::Ui;

InteractiveView actionView = InteractiveView::New();

actionView
  .SetClickable(true)
  .SetPseudoDisabled(false);
```

`SetClickable(bool)` controls whether click generation is allowed. `SetPseudoDisabled(bool)` changes the pseudo-disabled interaction state without replacing the view object. The matching query APIs are `IsClickable()` and `IsPseudoDisabled()`.

```cpp
using namespace Dali::Ui;

InteractiveView actionView = InteractiveView::New();

actionView.SetClickable(true);

if(actionView.IsClickable())
{
  actionView.SetPseudoDisabled(false);
}
```

## Handling Clicks

`ClickedSignal()` returns a `Dali::Signal` owned by `Dali::Ui::InteractiveView`. The clicked signal callback receives the clicked `Dali::Ui::View` and the `Dali::Ui::InputEvent` that caused the click.

```cpp
using namespace Dali::Ui;

class Controller
{
public:
  void Create()
  {
    mButton = InteractiveView::New();

    mButton.ClickedSignal().Connect(this, &Controller::OnClicked);
  }

private:
  void OnClicked(View view, InputEvent event)
  {
    mLastClicked = view;
  }

  InteractiveView mButton;
  View mLastClicked;
};
```

For fluent setup, use `ConnectClickedSignal(T* obj, Func func)`. It connects to the same `ClickedSignal()` and returns the `InteractiveView`, so it can be chained with typed setters.

```cpp
using namespace Dali::Ui;

class Controller
{
public:
  void Create()
  {
    mButton = InteractiveView::New()
      .SetClickable(true)
      .ConnectClickedSignal(this, &Controller::OnClicked);
  }

private:
  void OnClicked(View view, InputEvent event)
  {
    mLastClicked = view;
  }

  InteractiveView mButton;
  View mLastClicked;
};
```

Multiple `ConnectClickedSignal()` calls add multiple handlers.

```cpp
using namespace Dali::Ui;

class Controller
{
public:
  void Create()
  {
    mButton = InteractiveView::New();

    mButton.ConnectClickedSignal(this, &Controller::OnPrimaryClicked);
    mButton.ConnectClickedSignal(this, &Controller::OnAnalyticsClicked);
  }

private:
  void OnPrimaryClicked(View view, InputEvent event)
  {
    mLastClicked = view;
  }

  void OnAnalyticsClicked(View view, InputEvent event)
  {
    mTrackedClicked = view;
  }

  InteractiveView mButton;
  View mLastClicked;
  View mTrackedClicked;
};
```

## Tracking Pressed and Disabled Interaction State

Use `PressedChangedSignal()` when the UI needs to react to press-down and release transitions. The signal callback receives the `Dali::Ui::View`, a `bool pressed` value, and the `Dali::Ui::InputEvent`.

```cpp
using namespace Dali::Ui;

class Controller
{
public:
  void Create()
  {
    mButton = InteractiveView::New();

    mButton.PressedChangedSignal().Connect(this, &Controller::OnPressedChanged);
  }

private:
  void OnPressedChanged(View view, bool pressed, InputEvent event)
  {
    mPressedView = view;
    mPressed = pressed;
  }

  InteractiveView mButton;
  View mPressedView;
  bool mPressed{false};
};
```

`ConnectPressedChangedSignal(T* obj, Func func)` is the chainable form. `IsPressed()` can be queried when other app logic needs the current state.

```cpp
using namespace Dali::Ui;

class Controller
{
public:
  void Create()
  {
    mButton = InteractiveView::New()
      .SetClickable(true)
      .ConnectPressedChangedSignal(this, &Controller::OnPressedChanged);
  }

  bool IsButtonCurrentlyPressed() const
  {
    return mButton.IsPressed();
  }

private:
  void OnPressedChanged(View view, bool pressed, InputEvent event)
  {
    mPressed = pressed;
  }

  InteractiveView mButton;
  bool mPressed{false};
};
```

For pseudo-disabled state changes, connect to `PseudoDisabledChangedSignal()`. This signal receives the `Dali::Ui::View` and the new pseudo-disabled value.

```cpp
using namespace Dali::Ui;

class Controller
{
public:
  void Create()
  {
    mButton = InteractiveView::New();

    mButton.PseudoDisabledChangedSignal().Connect(
      this,
      &Controller::OnPseudoDisabledChanged);

    mButton.SetPseudoDisabled(true);
  }

private:
  void OnPseudoDisabledChanged(View view, bool pseudoDisabled)
  {
    mDisabledView = view;
    mPseudoDisabled = pseudoDisabled;
  }

  InteractiveView mButton;
  View mDisabledView;
  bool mPseudoDisabled{false};
};
```

## Handling Long Press

`LongPressedSignal()` is emitted when a long press is detected. Its callback receives the `Dali::Ui::View` and `Dali::Ui::InputEvent`, and returns `bool`.

Return `true` to consume the long press and suppress the following click. Return `false` to allow the later click flow.

```cpp
using namespace Dali::Ui;

class Controller
{
public:
  void Create()
  {
    mButton = InteractiveView::New();

    mButton.LongPressedSignal().Connect(this, &Controller::OnLongPressed);
  }

private:
  bool OnLongPressed(View view, InputEvent event)
  {
    mLongPressedView = view;
    return true;
  }

  InteractiveView mButton;
  View mLongPressedView;
};
```

The chainable helper is `ConnectLongPressedSignal(T* obj, Func func)`.

```cpp
using namespace Dali::Ui;

class Controller
{
public:
  void Create()
  {
    mButton = InteractiveView::New()
      .SetClickable(true)
      .ConnectLongPressedSignal(this, &Controller::OnLongPressed)
      .ConnectClickedSignal(this, &Controller::OnClicked);
  }

private:
  bool OnLongPressed(View view, InputEvent event)
  {
    mLongPressedView = view;
    return true;
  }

  void OnClicked(View view, InputEvent event)
  {
    mClickedView = view;
  }

  InteractiveView mButton;
  View mLongPressedView;
  View mClickedView;
};
```

## Controlling Key Click Behavior

`SetKeyClickPolicy(KeyClickPolicy)` controls when key input produces `ClickedSignal()`. `GetKeyClickPolicy()` returns the active policy.

Use `Dali::Ui::KeyClickPolicy::ON_RELEASE` for the default release-style activation, `Dali::Ui::KeyClickPolicy::ON_PRESS` for immediate activation on key press, and `Dali::Ui::KeyClickPolicy::DISABLED` to disable click generation from key input.

```cpp
using namespace Dali::Ui;

InteractiveView keyButton = InteractiveView::New();

keyButton.SetKeyClickPolicy(KeyClickPolicy::ON_PRESS);

KeyClickPolicy policy = keyButton.GetKeyClickPolicy();
```

A common app pattern is to keep touch clicking enabled while choosing a key policy that matches the surrounding focus and remote-control behavior.

```cpp
using namespace Dali::Ui;

class Controller
{
public:
  void Create()
  {
    mButton = InteractiveView::New()
      .SetClickable(true)
      .SetKeyClickPolicy(KeyClickPolicy::ON_RELEASE)
      .ConnectClickedSignal(this, &Controller::OnActivated);
  }

private:
  void OnActivated(View view, InputEvent event)
  {
    mActivatedView = view;
  }

  InteractiveView mButton;
  View mActivatedView;
};
```

## Using InteractiveView Handles

Use `InteractiveView::DownCast(BaseHandle handle)` when app code receives a generic handle and needs to use the `InteractiveView` API. A successful downcast returns a valid `InteractiveView`; otherwise the returned handle is uninitialized.

```cpp
using namespace Dali::Ui;

void ConfigureIfInteractive(BaseHandle handle)
{
  InteractiveView interactiveView = InteractiveView::DownCast(handle);

  if(interactiveView)
  {
    interactiveView
      .SetClickable(true)
      .SetPseudoDisabled(false);
  }
}
```

`InteractiveView` handles support copy and move handle semantics. Copy assignment keeps the same underlying view handle available through another `InteractiveView` variable.

```cpp
using namespace Dali::Ui;

InteractiveView source = InteractiveView::New();
InteractiveView target;

target = source;

target.SetClickable(true);
```

## Chain Support for Derived Interactive Views

`interactive-view.autogen.h` is part of the `InteractiveView` public feature surface. It provides `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS(ChildClass)` for dali-ui component classes that derive from `InteractiveView` and want the same fluent API shape.

The macro forwards chainable methods such as `SetClickable(bool)`, `SetPseudoDisabled(bool)`, `SetKeyClickPolicy(KeyClickPolicy)`, `ConnectClickedSignal(T* obj, Func func)`, `ConnectPressedChangedSignal(T* obj, Func func)`, and `ConnectLongPressedSignal(T* obj, Func func)` so derived view classes can return their own `ChildClass&`.

```cpp
using namespace Dali::Ui;

class AppActionView : public InteractiveView
{
public:
  DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS(AppActionView)
};
```

Application code normally uses concrete dali-ui view classes directly. This macro matters when implementing a reusable app component that should keep fluent chaining after adding an `InteractiveView` base.
