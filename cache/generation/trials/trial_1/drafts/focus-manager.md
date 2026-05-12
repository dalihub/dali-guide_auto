---
title: Focus Manager
sidebar_label: Focus Manager
category: input-interaction
---

# Focus Manager

`Dali::Ui::FocusManager` controls focus ownership, focus movement, focus groups, focus indicators, and focus-change notifications for `Dali::Ui::View` objects in a dali-ui application.

## Table of Contents

- [Getting the Focus Manager](#getting-the-focus-manager)
- [Requesting and Setting Focus](#requesting-and-setting-focus)
- [Moving and Clearing Focus](#moving-and-clearing-focus)
- [Containing Focus with Focus Groups](#containing-focus-with-focus-groups)
- [Customizing the Focus Indicator](#customizing-the-focus-indicator)
- [Reacting to Focus Changes](#reacting-to-focus-changes)
- [Window Focus Loss Policy](#window-focus-loss-policy)
- [Inspecting the Last Focus Change Device](#inspecting-the-last-focus-change-device)

## Getting the Focus Manager

Use `Dali::Ui::FocusManager::Get` to access the singleton focus manager. The manager is a handle type, so application code usually stores it briefly where focus behavior is configured.

```cpp
void ConfigureFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  Dali::Ui::View focusedView = focusManager.GetCurrentFocusView();
  if(focusedView)
  {
    // A View currently owns focus.
  }
}
```

The default constructor `Dali::Ui::FocusManager::FocusManager` creates an uninitialized handle. For application focus control, prefer `Dali::Ui::FocusManager::Get`.

```cpp
Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
```

## Requesting and Setting Focus

Use `Dali::Ui::FocusManager::RequestFocus` for normal application focus requests. It accepts a `Dali::Ui::View` and returns `true` when focus is successfully assigned.

```cpp
bool FocusFirstField(Dali::Ui::View firstField)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  bool focused = focusManager.RequestFocus(firstField);
  if(focused)
  {
    Dali::Ui::View current = focusManager.GetCurrentFocusView();
    return current == firstField;
  }

  return false;
}
```

`Dali::Ui::FocusManager::SetCurrentFocusView` sets focus directly to the supplied `Dali::Ui::View`. Use it when the exact view should become the focused view.

```cpp
bool RestoreKnownFocus(Dali::Ui::View previouslyFocusedView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  if(focusManager.SetCurrentFocusView(previouslyFocusedView))
  {
    return focusManager.GetCurrentFocusView() == previouslyFocusedView;
  }

  return false;
}
```

A common pattern is to request focus first, then read back `Dali::Ui::FocusManager::GetCurrentFocusView` when the application needs the resolved focus owner.

```cpp
Dali::Ui::View RequestAndReadFocus(Dali::Ui::View target)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  if(focusManager.RequestFocus(target))
  {
    return focusManager.GetCurrentFocusView();
  }

  return Dali::Ui::View();
}
```

## Moving and Clearing Focus

Use `Dali::Ui::FocusManager::MoveFocus` to move from the current focused `Dali::Ui::View` in a focus direction. The method returns `true` when a new focus target is found and focused.

```cpp
bool MoveFocusRight()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.MoveFocus(Dali::Ui::FocusDirection::RIGHT);
}
```

Forward and backward traversal can be driven through `Dali::Ui::FocusManager::MoveFocus`.

```cpp
bool MoveToNextFocusableView()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.MoveFocus(Dali::Ui::FocusDirection::FORWARD);
}

bool MoveToPreviousFocusableView()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.MoveFocus(Dali::Ui::FocusDirection::BACKWARD);
}
```

`Dali::Ui::FocusManager::MoveFocusBackward` moves back to the previously focused view. `Dali::Ui::FocusManager::ClearFocus` removes focus so that no `Dali::Ui::View` is currently focused.

```cpp
void LeaveCurrentPanel()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.MoveFocusBackward();

  if(!focusManager.GetCurrentFocusView())
  {
    focusManager.ClearFocus();
  }
}
```

For explicit cancellation flows, call `Dali::Ui::FocusManager::ClearFocus` directly.

```cpp
void CancelEditing()
{
  Dali::Ui::FocusManager::Get().ClearFocus();
}
```

## Containing Focus with Focus Groups

A focus group confines focus movement inside a `Dali::Ui::View` subtree. Use `Dali::Ui::FocusManager::SetAsFocusGroup` to enable or disable the group state.

```cpp
void EnableDialogFocusGroup(Dali::Ui::View dialogRoot)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetAsFocusGroup(dialogRoot, true);
}
```

Use `Dali::Ui::FocusManager::IsFocusGroup` to check whether a view is configured as a focus group.

```cpp
bool ToggleFocusGroup(Dali::Ui::View container)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  bool enabled = focusManager.IsFocusGroup(container);
  focusManager.SetAsFocusGroup(container, !enabled);

  return focusManager.IsFocusGroup(container);
}
```

Use `Dali::Ui::FocusManager::GetFocusGroup` to find the closest focus group for a focused view.

```cpp
Dali::Ui::View GetCurrentFocusGroup()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  Dali::Ui::View current = focusManager.GetCurrentFocusView();
  if(current)
  {
    return focusManager.GetFocusGroup(current);
  }

  return Dali::Ui::View();
}
```

## Customizing the Focus Indicator

`Dali::Ui::FocusManager::SetFocusIndicatorActor` replaces the default focus indicator with a `Dali::Ui::View`. Although the method name contains `Actor`, the dali-ui application-facing argument is a `Dali::Ui::View`.

```cpp
void UseCustomFocusIndicator(Dali::Ui::View indicatorView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetFocusIndicatorActor(indicatorView);
}
```

Use `Dali::Ui::FocusManager::GetFocusIndicatorView` when code needs to reuse or inspect the currently configured focus indicator view.

```cpp
Dali::Ui::View ReplaceFocusIndicator(Dali::Ui::View newIndicator)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  Dali::Ui::View previousIndicator = focusManager.GetFocusIndicatorView();
  focusManager.SetFocusIndicatorActor(newIndicator);

  return previousIndicator;
}
```

## Reacting to Focus Changes

`Dali::Ui::FocusManager::FocusChangedSignal` is emitted after the current focused view changes. The callback receives the previous focused `Dali::Ui::View` and the new focused `Dali::Ui::View`.

```cpp
class FocusStatus : public Dali::ConnectionTracker
{
public:
  void Connect()
  {
    Dali::Ui::FocusManager::Get().FocusChangedSignal().Connect(
      this,
      &FocusStatus::OnFocusChanged);
  }

private:
  void OnFocusChanged(Dali::Ui::View previous, Dali::Ui::View current)
  {
    mPreviousFocus = previous;
    mCurrentFocus  = current;
  }

  Dali::Ui::View mPreviousFocus;
  Dali::Ui::View mCurrentFocus;
};
```

The `current` argument can be empty after `Dali::Ui::FocusManager::ClearFocus`.

```cpp
class FocusClearedWatcher : public Dali::ConnectionTracker
{
public:
  void Start()
  {
    Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();
    focusManager.FocusChangedSignal().Connect(this, &FocusClearedWatcher::OnFocusChanged);
  }

private:
  void OnFocusChanged(Dali::Ui::View previous, Dali::Ui::View current)
  {
    if(previous && !current)
    {
      mFocusWasCleared = true;
    }
  }

  bool mFocusWasCleared{false};
};
```

## Window Focus Loss Policy

By default, focus is cleared when the window loses focus. Use `Dali::Ui::FocusManager::SetClearFocusOnWindowFocusLost` to change that policy, and `Dali::Ui::FocusManager::GetClearFocusOnWindowFocusLost` to read it back.

```cpp
void PreserveFocusWhileWindowIsInactive()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(false);

  bool preserveFocus = !focusManager.GetClearFocusOnWindowFocusLost();
  if(preserveFocus)
  {
    // The focused View can remain recorded while the window is inactive.
  }
}
```

Restore the default clear-on-loss behavior when leaving a mode that needs focus preservation.

```cpp
void RestoreDefaultWindowFocusPolicy()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(true);
}
```

## Inspecting the Last Focus Change Device

`Dali::Ui::FocusManager::GetLastFocusChangeDevice` reports the device category for the most recent focus change. Programmatic changes made through focus APIs are reported with the programmatic device value.

```cpp
bool LastFocusChangeWasProgrammatic()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetLastFocusChangeDevice() == Dali::Ui::FocusDevice::PROGRAMMATIC;
}
```

`Dali::Ui::FocusManager::GetLastFocusChangeDeviceName` returns the device name associated with the last focus change. For programmatic focus changes, the returned string can be empty.

```cpp
const Dali::String& ReadLastFocusDeviceName()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetLastFocusChangeDeviceName();
}
```
