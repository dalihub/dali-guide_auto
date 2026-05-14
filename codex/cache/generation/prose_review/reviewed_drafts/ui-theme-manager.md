---
title: Ui Theme Manager
sidebar_label: Ui Theme Manager
category: styling-theme-config
---

# Ui Theme Manager

`Dali::Ui::UiThemeManager` lets a dali-ui application read the active theme identifier and react when the current theme changes.

## Table of Contents

- [Using the Theme Manager Singleton](#using-the-theme-manager-singleton)
- [Reading the Current Theme Identifier](#reading-the-current-theme-identifier)
- [Reacting to Theme Changes](#reacting-to-theme-changes)
- [Working with Theme Manager Handles](#working-with-theme-manager-handles)

## Using the Theme Manager Singleton

`Dali::Ui::UiThemeManager` is a handle-based singleton. Application code normally obtains it with `Dali::Ui::UiThemeManager::Get()` when a `Dali::Ui::View` or another app-facing object needs theme information.

The manager is for theme-level access: it exposes the current theme identifier through `Dali::Ui::UiThemeManager::GetCurrentThemeId()` and theme-change notification through `Dali::Ui::UiThemeManager::ThemeChangedSignal()`. Color lookups are handled by `Dali::Ui::UiColorManager`, not directly by `Dali::Ui::UiThemeManager`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

class ThemeAwarePage
{
public:
  ThemeAwarePage()
  : mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
  }

  bool HasThemeManager() const
  {
    return static_cast<bool>(mThemeManager);
  }

private:
  Dali::Ui::UiThemeManager mThemeManager;
};
```

Repeated calls to `Dali::Ui::UiThemeManager::Get()` return handles to the same singleton instance, so a view controller can either keep a handle or call `Get()` where the theme state is needed.

```cpp
Dali::Ui::UiThemeManager first = Dali::Ui::UiThemeManager::Get();
Dali::Ui::UiThemeManager second = Dali::Ui::UiThemeManager::Get();

if(first && second && first == second)
{
  // Both handles refer to the same theme manager singleton.
}
```

## Reading the Current Theme Identifier

Use `Dali::Ui::UiThemeManager::GetCurrentThemeId()` to query the active theme id. The public contract returns the theme identifier, or an empty string when no theme is active.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

class ThemeLabelModel
{
public:
  void Refresh()
  {
    Dali::Ui::UiThemeManager themeManager = Dali::Ui::UiThemeManager::Get();

    if(themeManager)
    {
      mCurrentThemeId = themeManager.GetCurrentThemeId();
    }
    else
    {
      mCurrentThemeId.Clear();
    }
  }

  const Dali::String& GetThemeIdText() const
  {
    return mCurrentThemeId;
  }

private:
  Dali::String mCurrentThemeId;
};
```

A practical dali-ui app usually uses the theme id to choose app-level behavior around a `Dali::Ui::View`, such as selecting app-owned assets or refreshing view models. Keep the object model view-based: avoid reaching through to raw actor property updates just because the theme changed.

```cpp
class SettingsPage
{
public:
  explicit SettingsPage(Dali::Ui::View pageView)
  : mPageView(pageView),
    mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
    RefreshThemeState();
  }

  void RefreshThemeState()
  {
    if(!mThemeManager)
    {
      mThemeId.Clear();
      return;
    }

    mThemeId = mThemeManager.GetCurrentThemeId();
  }

private:
  Dali::Ui::View mPageView;
  Dali::Ui::UiThemeManager mThemeManager;
  Dali::String mThemeId;
};
```

## Reacting to Theme Changes

`Dali::Ui::UiThemeManager::ThemeChangedSignal()` returns the manager-owned theme-changed signal. The signal type is `Dali::Signal<void()>`, so callbacks connected to it should take no parameters and return `void`.

Use `Dali::ConnectionTracker` for receiver objects that own signal connections. This keeps the connection tied to the lifetime of the receiver.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

class ThemeAwareController : public Dali::ConnectionTracker
{
public:
  explicit ThemeAwareController(Dali::Ui::View rootView)
  : mRootView(rootView),
    mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
    if(mThemeManager)
    {
      mThemeManager.ThemeChangedSignal().Connect(
        this,
        &ThemeAwareController::OnThemeChanged);
    }

    UpdateForCurrentTheme();
  }

private:
  void OnThemeChanged()
  {
    UpdateForCurrentTheme();
  }

  void UpdateForCurrentTheme()
  {
    if(mThemeManager)
    {
      mThemeId = mThemeManager.GetCurrentThemeId();
    }
  }

private:
  Dali::Ui::View mRootView;
  Dali::Ui::UiThemeManager mThemeManager;
  Dali::String mThemeId;
};
```

The signal is emitted when the theme loader reports a theme change. App code does not switch themes through `Dali::Ui::UiThemeManager`; it observes the current theme state and updates app-owned state around its `Dali::Ui::View` hierarchy.

For small handlers, it is also fine to query the singleton inside the callback.

```cpp
class CompactThemeObserver : public Dali::ConnectionTracker
{
public:
  CompactThemeObserver()
  {
    Dali::Ui::UiThemeManager::Get().ThemeChangedSignal().Connect(
      this,
      &CompactThemeObserver::OnThemeChanged);
  }

private:
  void OnThemeChanged()
  {
    const auto themeId = Dali::Ui::UiThemeManager::Get().GetCurrentThemeId();

    if(!themeId.Empty())
    {
      mLastKnownThemeId = themeId;
    }
  }

private:
  Dali::String mLastKnownThemeId;
};
```

## Working with Theme Manager Handles

`Dali::Ui::UiThemeManager` follows the normal DALi handle pattern. A default-constructed `Dali::Ui::UiThemeManager` is uninitialized, while `Dali::Ui::UiThemeManager::Get()` returns the usable singleton handle.

```cpp
Dali::Ui::UiThemeManager emptyManager;

if(!emptyManager)
{
  emptyManager = Dali::Ui::UiThemeManager::Get();
}

if(emptyManager)
{
  const auto themeId = emptyManager.GetCurrentThemeId();
}
```

Theme manager handles can be copied when multiple objects need to keep access to the singleton.

```cpp
class ThemeHandleOwner
{
public:
  ThemeHandleOwner()
  : mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
  }

  Dali::Ui::UiThemeManager GetThemeManager() const
  {
    return mThemeManager;
  }

private:
  Dali::Ui::UiThemeManager mThemeManager;
};

ThemeHandleOwner owner;
Dali::Ui::UiThemeManager copiedManager = owner.GetThemeManager();

if(copiedManager)
{
  const auto themeId = copiedManager.GetCurrentThemeId();
}
```

Move construction and move assignment are supported through `Dali::Ui::UiThemeManager::UiThemeManager(Dali::Ui::UiThemeManager&&)` and `Dali::Ui::UiThemeManager::operator=(Dali::Ui::UiThemeManager&&)`. After moving, keep using the destination handle.

```cpp
Dali::Ui::UiThemeManager manager = Dali::Ui::UiThemeManager::Get();
Dali::Ui::UiThemeManager movedManager(std::move(manager));

if(movedManager)
{
  const auto themeId = movedManager.GetCurrentThemeId();
}
```

Use `Dali::Ui::UiThemeManager::DownCast()` only when you already have a generic DALi handle that may contain the theme manager. It returns a `Dali::Ui::UiThemeManager` handle when the object is compatible, or an uninitialized handle otherwise.

```cpp
Dali::Ui::UiThemeManager TryThemeManagerFromHandle(Dali::BaseHandle handle)
{
  Dali::Ui::UiThemeManager themeManager =
    Dali::Ui::UiThemeManager::DownCast(handle);

  return themeManager;
}
```
