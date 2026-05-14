---
title: Ui Theme Manager
sidebar_label: Ui Theme Manager
category: styling-theme-config
---
# Ui Theme Manager

UiThemeManager is a singleton that provides access to the current theme identifier and notifies your application when the theme changes.

## Table of Contents

- [Accessing the Theme Manager](#accessing-the-theme-manager)
- [Getting the Current Theme](#getting-the-current-theme)
- [Responding to Theme Changes](#responding-to-theme-changes)

## Accessing the Theme Manager

`UiThemeManager` is a handle-based singleton. Obtain the instance using the static `Get()` method:

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>

using namespace Dali::Ui;

UiThemeManager manager = UiThemeManager::Get();
```

The returned handle can be safely copied or moved:

```cpp
UiThemeManager manager = UiThemeManager::Get();
// Copy construction
UiThemeManager copy(manager);
// Move construction
UiThemeManager moved(std::move(manager));
```

To downcast a `BaseHandle` to a `UiThemeManager`:

```cpp
BaseHandle handle = UiThemeManager::Get();
UiThemeManager manager = UiThemeManager::DownCast(handle);
if (manager) {
  // Valid UiThemeManager
}
```

## Getting the Current Theme

Call `GetCurrentThemeId()` to retrieve the identifier of the active theme:

```cpp
UiThemeManager manager = UiThemeManager::Get();
String themeId = manager.GetCurrentThemeId();
```

The method returns the theme identifier string, or an empty string if no theme is active. The default theme loader returns `"default"` when no custom theme has been set.

## Responding to Theme Changes

Connect to `ThemeChangedSignal()` to react when the application theme changes:

```cpp
#include <dali-ui-foundation/public-api/ui-theme-manager.h>
#include <dali/public-api/signals/connection-tracker.h>

using namespace Dali;
using namespace Dali::Ui;

class ThemeObserver : public ConnectionTracker
{
public:
  ThemeObserver()
  {
    UiThemeManager manager = UiThemeManager::Get();
    manager.ThemeChangedSignal().Connect(this, &ThemeObserver::OnThemeChanged);
  }

  void OnThemeChanged()
  {
    UiThemeManager manager = UiThemeManager::Get();
    String newThemeId = manager.GetCurrentThemeId();
    // Update UI elements for the new theme
  }
};
```

The signal type is `Signal<void()>`, so the callback takes no parameters and returns void. Use a class derived from `ConnectionTracker` to manage signal connections automatically.
