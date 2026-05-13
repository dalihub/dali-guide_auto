---
title: Focus Manager
sidebar_label: Focus Manager
category: input-interaction
---

# Focus Manager

`Dali::Ui::FocusManager` controls which `Dali::Ui::View` has focus and how focus moves through a dali-ui view tree.

## Table of Contents

- [Get the Focus Manager](#get-the-focus-manager)
- [Request Focus for a View](#request-focus-for-a-view)
- [Move Focus Through the View Tree](#move-focus-through-the-view-tree)
- [Contain Focus with Focus Groups](#contain-focus-with-focus-groups)
- [Customize the Focus Indicator](#customize-the-focus-indicator)
- [React to Focus Changes](#react-to-focus-changes)
- [Preserve or Clear Focus on Window Focus Loss](#preserve-or-clear-focus-on-window-focus-loss)
- [Inspect the Last Focus Device](#inspect-the-last-focus-device)

## Get the Focus Manager

Use the singleton returned by `Dali::Ui::FocusManager::Get` when an application needs to read or change focus state. A default-constructed `Dali::Ui::FocusManager` is only a handle; application code normally works with the initialized singleton.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ConfigureFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  Dali::Ui::View focusedView = focusManager.GetCurrentFocusView();
  if(focusedView)
  {
    // The application already has a focused View.
  }
}
```

`Dali::Ui::FocusManager::GetCurrentFocusView` returns the currently focused `Dali::Ui::View`, or an empty handle when no view is focused.

## Request Focus for a View

Use `Dali::Ui::FocusManager::RequestFocus` when the target may be a container, because it can resolve focus to an eligible descendant. If no descendant accepts focus and the supplied `Dali::Ui::View` is focusable, that view receives focus. Use `Dali::Ui::FocusManager::SetCurrentFocusView` when you need to focus exactly the supplied `Dali::Ui::View`.

```cpp
bool FocusInitialView(Dali::Ui::View primaryView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
  return focusManager.RequestFocus(primaryView);
}
```

When the target must be focused directly, call `Dali::Ui::FocusManager::SetCurrentFocusView` and check its return value.

```cpp
bool FocusExactView(Dali::Ui::View view)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  const bool focused = focusManager.SetCurrentFocusView(view);
  return focused && focusManager.GetCurrentFocusView() == view;
}
```

To remove focus from the current view, call `Dali::Ui::FocusManager::ClearFocus`.

```cpp
void LeaveFocusState()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.ClearFocus();

  Dali::Ui::View focusedView = focusManager.GetCurrentFocusView();
  if(!focusedView)
  {
    // No View is focused.
  }
}
```

## Move Focus Through the View Tree

Use `Dali::Ui::FocusManager::MoveFocus` for directional or sequential navigation. The method returns `true` when focus moves to another eligible `Dali::Ui::View`.

```cpp
bool MoveFocusRight()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
  return focusManager.MoveFocus(Dali::Ui::FocusDirection::RIGHT);
}
```

For linear navigation, use `Dali::Ui::FocusDirection::FORWARD` and `Dali::Ui::FocusDirection::BACKWARD`.

```cpp
bool MoveToNextItem()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
  return focusManager.MoveFocus(Dali::Ui::FocusDirection::FORWARD);
}

bool MoveToPreviousItem()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
  return focusManager.MoveFocus(Dali::Ui::FocusDirection::BACKWARD);
}
```

`Dali::Ui::FocusManager::MoveFocusBackward` moves back to the previous focused view.

```cpp
void RestorePreviousFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
  focusManager.MoveFocusBackward();
}
```

## Contain Focus with Focus Groups

A focus group marks a `Dali::Ui::View` subtree as a boundary for `Dali::Ui::FocusManager::MoveFocus` traversal. Use `Dali::Ui::FocusManager::SetAsFocusGroup` to enable or disable the boundary.

```cpp
void EnableDialogFocusGroup(Dali::Ui::View dialogRoot)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetAsFocusGroup(dialogRoot, true);

  if(focusManager.IsFocusGroup(dialogRoot))
  {
    focusManager.RequestFocus(dialogRoot);
  }
}
```

Use `Dali::Ui::FocusManager::GetFocusGroup` to find the nearest focus group that contains a focused view.

```cpp
bool IsFocusInsideDialog(Dali::Ui::View dialogRoot)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  Dali::Ui::View focusedView = focusManager.GetCurrentFocusView();
  if(!focusedView)
  {
    return false;
  }

  Dali::Ui::View focusGroup = focusManager.GetFocusGroup(focusedView);
  return focusGroup == dialogRoot;
}
```

Disable the boundary by calling `Dali::Ui::FocusManager::SetAsFocusGroup` with `false`. A direct `Dali::Ui::FocusManager::RequestFocus` call can still move focus outside the group.

```cpp
void DisableDialogFocusGroup(Dali::Ui::View dialogRoot)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
  focusManager.SetAsFocusGroup(dialogRoot, false);
}
```

## Customize the Focus Indicator

`Dali::Ui::FocusManager` can use an application-provided `Dali::Ui::View` as the focus indicator. Set it with `Dali::Ui::FocusManager::SetFocusIndicatorActor`.

```cpp
void UseCustomFocusIndicator(Dali::Ui::View indicatorView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetFocusIndicatorActor(indicatorView);

  Dali::Ui::View activeIndicator = focusManager.GetFocusIndicatorView();
  if(activeIndicator == indicatorView)
  {
    // The custom indicator View is now registered.
  }
}
```

`Dali::Ui::FocusManager::GetFocusIndicatorView` returns the current focus indicator view.

```cpp
Dali::Ui::View GetCurrentIndicator()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
  return focusManager.GetFocusIndicatorView();
}
```

## React to Focus Changes

Use `Dali::Ui::FocusManager::FocusChangedSignal` when application state depends on global focus movement. The signal callback receives the previously focused `Dali::Ui::View` and the newly focused `Dali::Ui::View`.

```cpp
class FocusObserver
{
public:
  void Connect()
  {
    Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
    focusManager.FocusChangedSignal().Connect(this, &FocusObserver::OnFocusChanged);
  }

private:
  void OnFocusChanged(Dali::Ui::View previousFocus, Dali::Ui::View currentFocus)
  {
    if(previousFocus)
    {
      // A View lost focus.
    }

    if(currentFocus)
    {
      // A View gained focus.
    }
  }
};
```

The current focus can also be queried inside the callback with `Dali::Ui::FocusManager::GetCurrentFocusView`.

```cpp
class CurrentFocusReader
{
public:
  void Connect()
  {
    Dali::Ui::FocusManager::Get().FocusChangedSignal().Connect(
      this,
      &CurrentFocusReader::OnFocusChanged);
  }

private:
  void OnFocusChanged(Dali::Ui::View, Dali::Ui::View)
  {
    Dali::Ui::View currentFocus = Dali::Ui::FocusManager::Get().GetCurrentFocusView();
    if(currentFocus)
    {
      // Use the focused View for application state.
    }
  }
};
```

## Preserve or Clear Focus on Window Focus Loss

By default, `Dali::Ui::FocusManager` clears focus when the focused window loses focus. Configure this policy with `Dali::Ui::FocusManager::SetClearFocusOnWindowFocusLost`.

```cpp
void PreserveFocusAcrossWindowFocusLoss()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(false);

  const bool clearsFocus = focusManager.GetClearFocusOnWindowFocusLost();
  if(!clearsFocus)
  {
    // The current focus state is configured to remain set.
  }
}
```

Enable clearing again by passing `true`.

```cpp
void RestoreDefaultWindowFocusPolicy()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(true);
}
```

## Inspect the Last Focus Device

Use `Dali::Ui::FocusManager::GetLastFocusChangeDevice` to determine what kind of input last changed focus. Programmatic focus changes are reported with `Dali::Ui::FocusDevice::PROGRAMMATIC`.

```cpp
bool WasFocusChangedByCode()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetLastFocusChangeDevice() == Dali::Ui::FocusDevice::PROGRAMMATIC;
}
```

Use `Dali::Ui::FocusManager::GetLastFocusChangeDeviceName` when the application needs the device name associated with the last focus change.

```cpp
const Dali::String& GetLastFocusDeviceName()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
  return focusManager.GetLastFocusChangeDeviceName();
}
```
