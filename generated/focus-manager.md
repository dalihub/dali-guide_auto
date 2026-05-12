---
title: Focus Manager
sidebar_label: Focus Manager
category: input-interaction
---

# Focus Manager

`Dali::Ui::FocusManager` manages view focus, directional focus movement, focus groups, focus indicators, and focus-change notifications in a dali-ui application.

## Table of Contents

- [Getting the Focus Manager](#getting-the-focus-manager)
- [Requesting and Reading Focus](#requesting-and-reading-focus)
- [Moving Focus](#moving-focus)
- [Focus Groups](#focus-groups)
- [Focus Indicators](#focus-indicators)
- [Focus Change Signals](#focus-change-signals)
- [Focus Clearing and Window Focus Policy](#focus-clearing-and-window-focus-policy)
- [Last Focus Change Device](#last-focus-change-device)

## Getting the Focus Manager

`Dali::Ui::FocusManager` is the application-facing manager for keyboard and directional focus. In application code, get the singleton handle with `Dali::Ui::FocusManager::Get()` and keep your focus logic in terms of `Dali::Ui::View`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void InitializeFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(true);
}
```

A default-constructed `Dali::Ui::FocusManager` is only a handle. Use `Dali::Ui::FocusManager::Get()` before calling focus-management methods.

```cpp
void UseFocusManager()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  const bool clearOnLost = focusManager.GetClearFocusOnWindowFocusLost();
  focusManager.SetClearFocusOnWindowFocusLost(clearOnLost);
}
```

## Requesting and Reading Focus

Use `Dali::Ui::FocusManager::RequestFocus()` when an application wants focus to land on a `Dali::Ui::View` or on an appropriate focusable descendant of a container view. Use `Dali::Ui::FocusManager::SetCurrentFocusView()` when the application wants to set focus directly to that exact view.

```cpp
bool FocusInitialView(Dali::Ui::View initialView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  const bool focused = focusManager.RequestFocus(initialView);
  if(focused)
  {
    Dali::Ui::View current = focusManager.GetCurrentFocusView();
    return current == initialView;
  }

  return false;
}
```

`Dali::Ui::FocusManager::GetCurrentFocusView()` returns the currently focused `Dali::Ui::View`. If no view is focused, it returns an empty handle.

```cpp
Dali::Ui::View ReplaceFocus(Dali::Ui::View nextView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  Dali::Ui::View previousView = focusManager.GetCurrentFocusView();
  const bool changed = focusManager.SetCurrentFocusView(nextView);

  return changed ? previousView : focusManager.GetCurrentFocusView();
}
```

## Moving Focus

Use `Dali::Ui::FocusManager::MoveFocus()` for directional navigation. It takes a `Dali::Ui::FocusDirection` value such as `Dali::Ui::FocusDirection::LEFT`, `Dali::Ui::FocusDirection::RIGHT`, `Dali::Ui::FocusDirection::UP`, `Dali::Ui::FocusDirection::DOWN`, `Dali::Ui::FocusDirection::FORWARD`, or `Dali::Ui::FocusDirection::BACKWARD`.

```cpp
bool MoveFocusForArrowKey(Dali::Ui::FocusDirection direction)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.MoveFocus(direction);
}

void MoveFocusRightThenDown()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  const bool movedRight = focusManager.MoveFocus(Dali::Ui::FocusDirection::RIGHT);
  if(movedRight)
  {
    focusManager.MoveFocus(Dali::Ui::FocusDirection::DOWN);
  }
}
```

For reverse navigation, `Dali::Ui::FocusManager::MoveFocusBackward()` moves focus back to the previously focused view.

```cpp
void RestorePreviousFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.MoveFocusBackward();
}
```

## Focus Groups

A focus group contains directional focus movement inside a view subtree. Mark a `Dali::Ui::View` as a group with `Dali::Ui::FocusManager::SetAsFocusGroup()`, check it with `Dali::Ui::FocusManager::IsFocusGroup()`, and find the containing group for a child view with `Dali::Ui::FocusManager::GetFocusGroup()`.

```cpp
void EnableDialogFocusGroup(Dali::Ui::View dialogRoot, Dali::Ui::View firstField)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetAsFocusGroup(dialogRoot, true);

  if(focusManager.IsFocusGroup(dialogRoot))
  {
    focusManager.RequestFocus(firstField);
  }
}
```

When the current focus is inside a focus group, directional movement such as `Dali::Ui::FocusManager::MoveFocus(Dali::Ui::FocusDirection::FORWARD)` is evaluated within that group boundary.

```cpp
bool MoveInsideCurrentGroup(Dali::Ui::View currentView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  Dali::Ui::View group = focusManager.GetFocusGroup(currentView);
  if(group)
  {
    return focusManager.MoveFocus(Dali::Ui::FocusDirection::FORWARD);
  }

  return false;
}
```

Disable the group when the view should no longer trap focus.

```cpp
void DisableDialogFocusGroup(Dali::Ui::View dialogRoot)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetAsFocusGroup(dialogRoot, false);
}
```

## Focus Indicators

`Dali::Ui::FocusManager::SetFocusIndicatorActor()` installs the `Dali::Ui::View` used as the focus indicator. `Dali::Ui::FocusManager::GetFocusIndicatorView()` returns the current indicator view.

```cpp
void ConfigureFocusIndicator(Dali::Ui::View indicatorView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetFocusIndicatorActor(indicatorView);

  Dali::Ui::View activeIndicator = focusManager.GetFocusIndicatorView();
  if(activeIndicator == indicatorView)
  {
    focusManager.SetClearFocusOnWindowFocusLost(true);
  }
}
```

Use this for application-level focus styling that should follow the currently focused `Dali::Ui::View`.

## Focus Change Signals

`Dali::Ui::FocusManager::FocusChangedSignal()` is emitted after the current focused view changes. The callback receives the previous focused `Dali::Ui::View` and the new focused `Dali::Ui::View`.

```cpp
class FocusStatusController : public Dali::ConnectionTracker
{
public:
  void Connect()
  {
    Dali::Ui::FocusManager::Get().FocusChangedSignal().Connect(
      this,
      &FocusStatusController::OnFocusChanged);
  }

private:
  void OnFocusChanged(Dali::Ui::View previousView, Dali::Ui::View currentView)
  {
    mPreviousFocusedView = previousView;
    mCurrentFocusedView = currentView;
  }

  Dali::Ui::View mPreviousFocusedView;
  Dali::Ui::View mCurrentFocusedView;
};
```

A lambda can also be connected when the owning object is a valid connection tracker.

```cpp
class FocusLogger : public Dali::ConnectionTracker
{
public:
  void Connect()
  {
    Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

    focusManager.FocusChangedSignal().Connect(
      this,
      [this](Dali::Ui::View previousView, Dali::Ui::View currentView)
      {
        mLastPreviousView = previousView;
        mLastCurrentView = currentView;
      });
  }

private:
  Dali::Ui::View mLastPreviousView;
  Dali::Ui::View mLastCurrentView;
};
```

## Focus Clearing and Window Focus Policy

Use `Dali::Ui::FocusManager::ClearFocus()` to remove focus from the current view. After clearing, `Dali::Ui::FocusManager::GetCurrentFocusView()` returns an empty handle.

```cpp
void CloseFocusedPopup()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.ClearFocus();

  Dali::Ui::View currentView = focusManager.GetCurrentFocusView();
  if(!currentView)
  {
    focusManager.SetClearFocusOnWindowFocusLost(true);
  }
}
```

`Dali::Ui::FocusManager::SetClearFocusOnWindowFocusLost()` controls whether focus is cleared when the application window loses focus. Read the current policy with `Dali::Ui::FocusManager::GetClearFocusOnWindowFocusLost()`.

```cpp
void PreserveFocusAcrossWindowFocusLoss(bool preserve)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(!preserve);

  const bool clearOnWindowFocusLost = focusManager.GetClearFocusOnWindowFocusLost();
  if(clearOnWindowFocusLost)
  {
    focusManager.ClearFocus();
  }
}
```

## Last Focus Change Device

`Dali::Ui::FocusManager::GetLastFocusChangeDevice()` reports the device category that caused the most recent focus change. `Dali::Ui::FocusManager::GetLastFocusChangeDeviceName()` returns the device name string for that change.

```cpp
bool LastFocusChangeWasProgrammatic()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetLastFocusChangeDevice() == Dali::Ui::FocusDevice::PROGRAMMATIC;
}
```

Use the device information when your app needs different behavior for keyboard, pointer, touch, wheel, gamepad, or programmatic focus changes.

```cpp
Dali::String GetLastFocusDeviceName()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  const Dali::String& deviceName = focusManager.GetLastFocusChangeDeviceName();
  return deviceName;
}
```
