---
title: Interactive View
sidebar_label: Interactive View
category: views-components
---

# Interactive View

`Dali::Ui::InteractiveView` is the dali-ui view type for clickable, pressable UI surfaces.

## Table of Contents

- [Create an Interactive View](#create-an-interactive-view)
- [Handle Click, Press, and Long-Press Input](#handle-click-press-and-long-press-input)
- [Control Clickability and Disabled-Like State](#control-clickability-and-disabled-like-state)
- [Choose Keyboard Click Timing](#choose-keyboard-click-timing)
- [Use InteractiveView in Custom View Handles](#use-interactiveview-in-custom-view-handles)

## Create an Interactive View

Use `Dali::Ui::InteractiveView::New()` when an application needs a plain interactive container or the base handle for a custom component. The handle is a `Dali::Ui::View`, so app code should keep working with the dali-ui view model rather than raw actor APIs.

```cpp
Dali::Ui::InteractiveView actionView = Dali::Ui::InteractiveView::New();

actionView
  .SetClickable(true)
  .SetPseudoDisabled(false)
  .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE);
```

`Dali::Ui::InteractiveView` also supports normal handle copying and assignment.

```cpp
Dali::Ui::InteractiveView primary = Dali::Ui::InteractiveView::New();
Dali::Ui::InteractiveView copy(primary);

Dali::Ui::InteractiveView assigned;
assigned = primary;
```

When code receives a generic handle from framework code or a reusable view factory, use `Dali::Ui::InteractiveView::DownCast()` before calling interactive APIs.

```cpp
Dali::Ui::InteractiveView ResolveInteractiveView(Dali::BaseHandle handle)
{
  return Dali::Ui::InteractiveView::DownCast(handle);
}
```

## Handle Click, Press, and Long-Press Input

`Dali::Ui::InteractiveView::ConnectClickedSignal()` is the concise way to handle click activation. The callback signature is `void(Dali::Ui::View, Dali::Ui::InputEvent)`.

```cpp
class ActionController
{
public:
  void Build()
  {
    mActionView = Dali::Ui::InteractiveView::New()
      .SetClickable(true)
      .ConnectClickedSignal(this, &ActionController::OnClicked);
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    ++mClickCount;
  }

private:
  Dali::Ui::InteractiveView mActionView;
  unsigned int              mClickCount{0u};
};
```

Use `Dali::Ui::InteractiveView::ConnectPressedChangedSignal()` when the UI should react separately to press-down and release. The callback receives the pressed state as the second argument.

```cpp
class PressController
{
public:
  void Build()
  {
    mPressable = Dali::Ui::InteractiveView::New()
      .ConnectPressedChangedSignal(this, &PressController::OnPressedChanged);
  }

  void OnPressedChanged(Dali::Ui::View view, bool pressed, Dali::Ui::InputEvent event)
  {
    mPressed = pressed;
  }

private:
  Dali::Ui::InteractiveView mPressable;
  bool                      mPressed{false};
};
```

Use `Dali::Ui::InteractiveView::ConnectLongPressedSignal()` for long-press behavior. The callback signature is `bool(Dali::Ui::View, Dali::Ui::InputEvent)`. Return `true` to consume the long press and suppress the later click; return `false` to allow the click path to continue.

```cpp
class ContextActionController
{
public:
  void Build()
  {
    mTarget = Dali::Ui::InteractiveView::New()
      .ConnectClickedSignal(this, &ContextActionController::OnClicked)
      .ConnectLongPressedSignal(this, &ContextActionController::OnLongPressed);
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    ++mClickCount;
  }

  bool OnLongPressed(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mLongPressed = true;
    return true;
  }

private:
  Dali::Ui::InteractiveView mTarget;
  unsigned int              mClickCount{0u};
  bool                      mLongPressed{false};
};
```

The raw signal accessors are also available when code needs to work directly with `Dali::Signal`: `Dali::Ui::InteractiveView::ClickedSignal()`, `Dali::Ui::InteractiveView::PressedChangedSignal()`, and `Dali::Ui::InteractiveView::LongPressedSignal()`.

```cpp
class DirectSignalController
{
public:
  void Build()
  {
    mView = Dali::Ui::InteractiveView::New();

    Dali::Signal<void(Dali::Ui::View, Dali::Ui::InputEvent)>& clicked =
      mView.ClickedSignal();

    clicked.Connect(this, &DirectSignalController::OnClicked);
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mHandled = true;
  }

private:
  Dali::Ui::InteractiveView mView;
  bool                      mHandled{false};
};
```

## Control Clickability and Disabled-Like State

`Dali::Ui::InteractiveView::SetClickable()` controls whether click activation is allowed. `Dali::Ui::InteractiveView::IsClickable()` reports the current value.

```cpp
Dali::Ui::InteractiveView command = Dali::Ui::InteractiveView::New();

command.SetClickable(false);

if(!command.IsClickable())
{
  command.SetClickable(true);
}
```

`Dali::Ui::InteractiveView::SetPseudoDisabled()` stores a disabled-like state on the interactive view. Use `Dali::Ui::InteractiveView::IsPseudoDisabled()` when app code needs to query that state.

```cpp
class AvailabilityController
{
public:
  void SetAvailable(bool available)
  {
    mCommand.SetPseudoDisabled(!available);
    mCommand.SetClickable(available);
  }

  bool IsAvailable() const
  {
    return !mCommand.IsPseudoDisabled() && mCommand.IsClickable();
  }

private:
  Dali::Ui::InteractiveView mCommand{Dali::Ui::InteractiveView::New()};
};
```

For state observers, `Dali::Ui::InteractiveView::PseudoDisabledChangedSignal()` emits `void(Dali::Ui::View, bool)`.

```cpp
class DisabledStateController
{
public:
  void Build()
  {
    mView = Dali::Ui::InteractiveView::New();
    mView.PseudoDisabledChangedSignal().Connect(
      this,
      &DisabledStateController::OnPseudoDisabledChanged);
  }

  void OnPseudoDisabledChanged(Dali::Ui::View view, bool pseudoDisabled)
  {
    mPseudoDisabled = pseudoDisabled;
  }

private:
  Dali::Ui::InteractiveView mView;
  bool                      mPseudoDisabled{false};
};
```

## Choose Keyboard Click Timing

`Dali::Ui::InteractiveView::SetKeyClickPolicy()` configures when keyboard, D-Pad, or remote-control input should produce click activation. Use `Dali::Ui::InteractiveView::GetKeyClickPolicy()` to inspect the current policy.

```cpp
Dali::Ui::InteractiveView remoteButton = Dali::Ui::InteractiveView::New();

remoteButton.SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_PRESS);

if(remoteButton.GetKeyClickPolicy() == Dali::Ui::KeyClickPolicy::ON_PRESS)
{
  remoteButton.SetClickable(true);
}
```

Use `Dali::Ui::KeyClickPolicy::ON_RELEASE` when activation should happen after the key is released, `Dali::Ui::KeyClickPolicy::ON_PRESS` when activation should happen immediately on key press, and `Dali::Ui::KeyClickPolicy::DISABLED` when key input should not generate click activation for that view.

```cpp
Dali::Ui::InteractiveView pointerOnlyView = Dali::Ui::InteractiveView::New()
  .SetClickable(true)
  .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::DISABLED);
```

## Use InteractiveView in Custom View Handles

For a custom handle class that derives from `Dali::Ui::InteractiveView`, include the generated chaining methods with `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS`. The macro is defined by `interactive-view.autogen.h` and returns the derived type from the interactive setters and connection helpers.

```cpp
class MenuItemView : public Dali::Ui::InteractiveView
{
public:
  MenuItemView() = default;

  static MenuItemView New()
  {
    return MenuItemView(Dali::Ui::InteractiveView::New());
  }

  DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS(MenuItemView)

private:
  explicit MenuItemView(Dali::Ui::InteractiveView view)
  : Dali::Ui::InteractiveView(view)
  {
  }
};
```

The derived handle can then use fluent chaining while preserving the derived return type.

```cpp
class MenuController
{
public:
  void Build()
  {
    mItem = MenuItemView::New()
      .SetClickable(true)
      .SetPseudoDisabled(false)
      .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE)
      .ConnectClickedSignal(this, &MenuController::OnItemClicked);
  }

  void OnItemClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    MenuItemView item = MenuItemView::DownCast(view);
    if(item)
    {
      mSelected = true;
    }
  }

private:
  MenuItemView mItem;
  bool         mSelected{false};
};
```