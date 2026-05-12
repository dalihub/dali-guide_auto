---
title: Ui Theme Manager
sidebar_label: Ui Theme Manager
category: styling-theme-config
---

# Ui Theme Manager

`Dali::Ui::UiThemeManager` is the dali-ui singleton for reading the current theme identity and observing theme changes in an application.

## Table of Contents

- [Getting the Theme Manager](#getting-the-theme-manager)
- [Reading the Current Theme ID](#reading-the-current-theme-id)
- [Reacting to Theme Changes](#reacting-to-theme-changes)
- [Handle Passing and Down Casting](#handle-passing-and-down-casting)

## Getting the Theme Manager

Use `Dali::Ui::UiThemeManager::Get` to access the application-wide theme manager. `Dali::Ui::UiThemeManager` is a handle type, so application code can copy or move the handle while still referring to the same singleton manager.

```cpp
void InitializeThemeAccess()
{
  Dali::Ui::UiThemeManager themeManager = Dali::Ui::UiThemeManager::Get();

  auto currentThemeId = themeManager.GetCurrentThemeId();
}
```

For dali-ui application code, keep this as a theme-level service object. Use it alongside your `Dali::Ui::View` tree to update styling decisions when the theme changes, rather than treating theme state as a raw scene object.

```cpp
class ThemeAwarePage
{
public:
  ThemeAwarePage()
  : mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
  }

  void RefreshThemeState()
  {
    auto themeId = mThemeManager.GetCurrentThemeId();

    if(themeId.empty())
    {
      return;
    }

    ApplyThemeId(themeId);
  }

private:
  void ApplyThemeId(const std::string& themeId)
  {
    // Update dali-ui view styling owned by this page.
  }

private:
  Dali::Ui::UiThemeManager mThemeManager;
};
```

## Reading the Current Theme ID

`Dali::Ui::UiThemeManager::GetCurrentThemeId` returns the identifier of the currently active theme. The public API returns an empty string when no theme is active.

```cpp
void UpdateThemeLabel()
{
  Dali::Ui::UiThemeManager themeManager = Dali::Ui::UiThemeManager::Get();

  auto themeId = themeManager.GetCurrentThemeId();

  if(themeId.empty())
  {
    ShowThemeName("No active theme");
  }
  else
  {
    ShowThemeName(themeId);
  }
}
```

The theme ID is useful as application state: for example, a page controller can cache the last applied ID and skip unnecessary restyling work.

```cpp
class ThemeState
{
public:
  void Sync()
  {
    Dali::Ui::UiThemeManager themeManager = Dali::Ui::UiThemeManager::Get();

    auto themeId = themeManager.GetCurrentThemeId();

    if(themeId == mAppliedThemeId)
    {
      return;
    }

    mAppliedThemeId = themeId;
    RebuildThemeDependentViews();
  }

private:
  void RebuildThemeDependentViews()
  {
    // Recreate or restyle dali-ui views that depend on the active theme.
  }

private:
  std::string mAppliedThemeId;
};
```

## Reacting to Theme Changes

Use `Dali::Ui::UiThemeManager::ThemeChangedSignal` to react when the active theme changes. The signal type is `Dali::Signal<void()>`, so callbacks connected to it take no parameters and return `void`.

When the receiver object owns the connection, derive it from `Dali::ConnectionTracker` so signal connections can be tracked with the receiver lifetime.

```cpp
class ThemeController : public Dali::ConnectionTracker
{
public:
  ThemeController()
  : mThemeManager(Dali::Ui::UiThemeManager::Get())
  {
    mThemeManager.ThemeChangedSignal().Connect(this, &ThemeController::OnThemeChanged);
  }

  void Initialize()
  {
    ApplyCurrentTheme();
  }

private:
  void OnThemeChanged()
  {
    ApplyCurrentTheme();
  }

  void ApplyCurrentTheme()
  {
    auto themeId = mThemeManager.GetCurrentThemeId();

    if(themeId.empty())
    {
      return;
    }

    ApplyThemeToViews(themeId);
  }

  void ApplyThemeToViews(const std::string& themeId)
  {
    // Apply theme-dependent values to the controller's dali-ui views.
  }

private:
  Dali::Ui::UiThemeManager mThemeManager;
};
```

For smaller components, keep the callback focused on invalidating or restyling the relevant `Dali::Ui::View` subtree.

```cpp
class ThemeAwarePanel : public Dali::ConnectionTracker
{
public:
  ThemeAwarePanel()
  {
    Dali::Ui::UiThemeManager manager = Dali::Ui::UiThemeManager::Get();
    manager.ThemeChangedSignal().Connect(this, &ThemeAwarePanel::ReloadStyle);
  }

private:
  void ReloadStyle()
  {
    auto themeId = Dali::Ui::UiThemeManager::Get().GetCurrentThemeId();
    ApplyPanelStyle(themeId);
  }

  void ApplyPanelStyle(const std::string& themeId)
  {
    // Restyle the panel's dali-ui views for the new theme.
  }
};
```

## Handle Passing and Down Casting

`Dali::Ui::UiThemeManager` is a DALi handle. You can pass it through application helper layers by value, copy it, or move it when transferring ownership of a stored handle.

```cpp
class ThemeHandleStore
{
public:
  void SetManager(Dali::Ui::UiThemeManager manager)
  {
    mThemeManager = std::move(manager);
  }

  void PrintCurrentTheme()
  {
    auto themeId = mThemeManager.GetCurrentThemeId();
    LogThemeId(themeId);
  }

private:
  void LogThemeId(const std::string& themeId)
  {
    // Application logging.
  }

private:
  Dali::Ui::UiThemeManager mThemeManager;
};

void ConfigureThemeStore()
{
  ThemeHandleStore store;
  store.SetManager(Dali::Ui::UiThemeManager::Get());
}
```

Use `Dali::Ui::UiThemeManager::DownCast` only when generic DALi handle plumbing gives you a base handle and you need to recover the theme manager handle.

```cpp
Dali::Ui::UiThemeManager ResolveThemeManager(Dali::BaseHandle handle)
{
  return Dali::Ui::UiThemeManager::DownCast(handle);
}

void UseGenericHandle(Dali::BaseHandle handle)
{
  Dali::Ui::UiThemeManager manager = Dali::Ui::UiThemeManager::DownCast(handle);

  auto themeId = manager.GetCurrentThemeId();
  ApplyTheme(themeId);
}
```

`Dali::Ui::UiThemeManager::DownCast` returns a `Dali::Ui::UiThemeManager` handle. Use this for interop boundaries only; normal dali-ui application code should get the singleton directly with `Dali::Ui::UiThemeManager::Get`.
