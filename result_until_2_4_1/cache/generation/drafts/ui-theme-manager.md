---
title: Ui Theme Manager
sidebar_label: Ui Theme Manager
category: styling-theme-config
---

# Ui Theme Manager

`Dali::Ui::UiThemeManager` lets a dali-ui application read the active theme identifier and react when the theme changes.

## Table of Contents

- [Accessing the Theme Manager](#accessing-the-theme-manager)
- [Reading the Current Theme](#reading-the-current-theme)
- [Reacting to Theme Changes](#reacting-to-theme-changes)
- [Using Theme State from a View-Oriented App Layer](#using-theme-state-from-a-view-oriented-app-layer)

## Accessing the Theme Manager

`Dali::Ui::UiThemeManager` is a handle-based singleton. Application code obtains the active manager with `Dali::Ui::UiThemeManager::Get()` and keeps the returned handle only as long as it is useful.

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

class ThemeController
{
public:
  ThemeController()
  : mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
  }

private:
  Dali::Ui::UiThemeManager mThemeManager;
};
```

If your code receives a generic handle from infrastructure code, `Dali::Ui::UiThemeManager::DownCast()` can be used to recover a `Dali::Ui::UiThemeManager` handle when the object is actually the theme manager.

```cpp
#include <dali/public-api/object/base-handle.h>
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

Dali::Ui::UiThemeManager AsThemeManager(Dali::BaseHandle handle)
{
  return Dali::Ui::UiThemeManager::DownCast(handle);
}
```

## Reading the Current Theme

Use `Dali::Ui::UiThemeManager::GetCurrentThemeId()` when application logic needs to know which theme is active. The returned value is the current theme identifier; it can be empty when no theme is active.

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

auto ReadThemeId()
{
  Dali::Ui::UiThemeManager themeManager = Dali::Ui::UiThemeManager::Get();
  return themeManager.GetCurrentThemeId();
}
```

For short-lived checks, it is also valid to query the singleton directly.

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

auto currentThemeId = Dali::Ui::UiThemeManager::Get().GetCurrentThemeId();
```

## Reacting to Theme Changes

`Dali::Ui::UiThemeManager::ThemeChangedSignal()` returns the theme-change signal owned by `Dali::Ui::UiThemeManager`. The signal type is `Dali::Signal<void()>`, so callbacks connected to it take no arguments.

Use `Dali::ConnectionTracker` for receiver objects that own signal connections.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

class ThemeObserver : public Dali::ConnectionTracker
{
public:
  ThemeObserver()
  {
    Dali::Ui::UiThemeManager themeManager = Dali::Ui::UiThemeManager::Get();
    themeManager.ThemeChangedSignal().Connect(this, &ThemeObserver::OnThemeChanged);
  }

private:
  void OnThemeChanged()
  {
    auto themeId = Dali::Ui::UiThemeManager::Get().GetCurrentThemeId();
    ApplyThemeId(themeId);
  }

  void ApplyThemeId(const decltype(Dali::Ui::UiThemeManager::Get().GetCurrentThemeId())& themeId)
  {
    mLastThemeId = themeId;
  }

  decltype(Dali::Ui::UiThemeManager::Get().GetCurrentThemeId()) mLastThemeId;
};
```

A theme-change callback should be small. Read the current identifier with `Dali::Ui::UiThemeManager::GetCurrentThemeId()`, then update the application state that drives your dali-ui views.

## Using Theme State from a View-Oriented App Layer

In a dali-ui application, keep theme reactions close to the code that owns the `Dali::Ui::View` tree. `Dali::Ui::UiThemeManager` is the app-facing entry point for theme state; it should not require application code to manipulate raw actors or set low-level properties.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

class MainViewThemeBinding : public Dali::ConnectionTracker
{
public:
  MainViewThemeBinding()
  : mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
    mThemeManager.ThemeChangedSignal().Connect(this, &MainViewThemeBinding::OnThemeChanged);
    RefreshFromTheme();
  }

private:
  void OnThemeChanged()
  {
    RefreshFromTheme();
  }

  void RefreshFromTheme()
  {
    mCurrentThemeId = mThemeManager.GetCurrentThemeId();

    // Use mCurrentThemeId to update state that your Dali::Ui::View layer consumes.
  }

  Dali::Ui::UiThemeManager mThemeManager;
  decltype(Dali::Ui::UiThemeManager::Get().GetCurrentThemeId()) mCurrentThemeId;
};
```

`Dali::Ui::UiThemeManager` does not expose property constants. Use `Dali::Ui::UiThemeManager::Get()`, `Dali::Ui::UiThemeManager::GetCurrentThemeId()`, and `Dali::Ui::UiThemeManager::ThemeChangedSignal()` as the public theme manager surface for application code.
