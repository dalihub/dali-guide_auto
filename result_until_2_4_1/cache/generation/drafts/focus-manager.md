---
title: Focus Manager
sidebar_label: Focus Manager
category: input-interaction
---

# Focus Manager

`Dali::Ui::FocusManager` manages keyboard focus for `Dali::Ui::View` objects in a dali-ui application.

## Table of Contents

- [Getting the Focus Manager](#getting-the-focus-manager)
- [Requesting Focus](#requesting-focus)
- [Moving Focus](#moving-focus)
- [Focus Groups](#focus-groups)
- [Focus Indicator](#focus-indicator)
- [Focus Change Signals](#focus-change-signals)
- [Clearing and Preserving Focus](#clearing-and-preserving-focus)
- [Inspecting the Last Focus Change Device](#inspecting-the-last-focus-change-device)

## Getting the Focus Manager

Use `Dali::Ui::FocusManager::Get()` to access the application-wide focus manager. The returned `Dali::Ui::FocusManager` handle is the entry point for setting focus, moving focus, listening for focus changes, and configuring focus behavior.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ConfigureFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(true);
}
```

Avoid constructing a standalone `Dali::Ui::FocusManager` with `Dali::Ui::FocusManager::FocusManager()` for normal application work. Use the singleton returned by `Dali::Ui::FocusManager::Get()`.

## Requesting Focus

Use `Dali::Ui::FocusManager::RequestFocus()` for normal application focus requests. It accepts a `Dali::Ui::View` and returns `true` when focus is applied.

```cpp
bool FocusContent(Dali::Ui::View contentView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.RequestFocus(contentView);
}
```

`Dali::Ui::FocusManager::RequestFocus()` is the preferred call when the target may be a container view. If the requested view can delegate focus to a focusable descendant, the resolved descendant becomes the current focus view.

Use `Dali::Ui::FocusManager::SetCurrentFocusView()` when you intentionally want to focus exactly the specified `Dali::Ui::View`.

```cpp
bool FocusExactView(Dali::Ui::View view)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.SetCurrentFocusView(view);
}
```

After either request, use `Dali::Ui::FocusManager::GetCurrentFocusView()` to retrieve the focused `Dali::Ui::View`.

```cpp
Dali::Ui::View FocusAndReturnCurrent(Dali::Ui::View requestedView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.RequestFocus(requestedView);

  return focusManager.GetCurrentFocusView();
}
```

## Moving Focus

Use `Dali::Ui::FocusManager::MoveFocus()` to move focus in a direction. It returns `true` if a next focus target is found and focused.

```cpp
bool MoveFocusRight()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.MoveFocus(Dali::Ui::FocusDirection::RIGHT);
}
```

Directional movement can be used for arrow-key style navigation.

```cpp
bool MoveFocusForArrowKey(Dali::Ui::FocusDirection direction)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.MoveFocus(direction);
}
```

For history-style navigation, use `Dali::Ui::FocusManager::MoveFocusBackward()`. This attempts to restore a previously focused view from the focus history.

```cpp
void ReturnToPreviousFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.MoveFocusBackward();
}
```

## Focus Groups

A focus group limits focus navigation within a view subtree. Mark a `Dali::Ui::View` as a group with `Dali::Ui::FocusManager::SetAsFocusGroup()`.

```cpp
void EnableFocusGroup(Dali::Ui::View panel)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetAsFocusGroup(panel, true);
}
```

Use `Dali::Ui::FocusManager::IsFocusGroup()` to check whether a view is currently configured as a focus group.

```cpp
bool PanelTrapsFocus(Dali::Ui::View panel)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.IsFocusGroup(panel);
}
```

Use `Dali::Ui::FocusManager::GetFocusGroup()` to find the closest focus group that contains a view.

```cpp
Dali::Ui::View FindOwningFocusGroup(Dali::Ui::View focusedChild)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetFocusGroup(focusedChild);
}
```

Focus groups are most useful for modal panels, popups, and composite controls where `Dali::Ui::FocusManager::MoveFocus()` should stay inside a local section until the application explicitly moves focus elsewhere.

## Focus Indicator

`Dali::Ui::FocusManager` owns the focus indicator configuration. Use `Dali::Ui::FocusManager::SetFocusIndicatorActor()` to replace the focus indicator with a custom `Dali::Ui::View`.

```cpp
void InstallFocusIndicator(Dali::Ui::View indicatorView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetFocusIndicatorActor(indicatorView);
}
```

Use `Dali::Ui::FocusManager::GetFocusIndicatorView()` to retrieve the current indicator view.

```cpp
Dali::Ui::View GetCurrentFocusIndicator()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetFocusIndicatorView();
}
```

Although the setter name contains `Actor`, application code should still pass a `Dali::Ui::View` as the indicator object.

## Focus Change Signals

Use `Dali::Ui::FocusManager::FocusChangedSignal()` to observe focus changes at the manager level. The callback receives the previously focused `Dali::Ui::View` and the newly focused `Dali::Ui::View`.

```cpp
class FocusController
{
public:
  void ConnectFocusSignal()
  {
    Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

    focusManager.FocusChangedSignal().Connect(
      this,
      &FocusController::OnFocusChanged);
  }

private:
  void OnFocusChanged(Dali::Ui::View previousFocus, Dali::Ui::View currentFocus)
  {
    mPreviousFocus = previousFocus;
    mCurrentFocus  = currentFocus;
  }

  Dali::Ui::View mPreviousFocus;
  Dali::Ui::View mCurrentFocus;
};
```

Use the manager-level signal when application state depends on global focus ownership, such as updating command availability or synchronizing focus-dependent UI state.

## Clearing and Preserving Focus

Use `Dali::Ui::FocusManager::ClearFocus()` to remove focus from the current view.

```cpp
void ClearApplicationFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.ClearFocus();
}
```

Use `Dali::Ui::FocusManager::SetClearFocusOnWindowFocusLost()` to configure whether focus is cleared when the window loses focus. Query the current setting with `Dali::Ui::FocusManager::GetClearFocusOnWindowFocusLost()`.

```cpp
bool PreserveFocusWhileWindowIsInactive()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(false);

  return focusManager.GetClearFocusOnWindowFocusLost();
}
```

Enable clearing when focus should be reset after the application window loses activation. Disable it when the app should keep its previous focus state for restoration.

## Inspecting the Last Focus Change Device

Use `Dali::Ui::FocusManager::GetLastFocusChangeDevice()` to inspect what caused the most recent focus change. For focus changes made through public focus manager calls, the device can be `Dali::Ui::FocusDevice::PROGRAMMATIC`.

```cpp
bool LastFocusChangeWasProgrammatic()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetLastFocusChangeDevice() ==
         Dali::Ui::FocusDevice::PROGRAMMATIC;
}
```

Use `Dali::Ui::FocusManager::GetLastFocusChangeDeviceName()` when device-specific focus behavior needs the device name.

```cpp
const Dali::String& GetLastFocusDeviceName()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetLastFocusChangeDeviceName();
}
```
