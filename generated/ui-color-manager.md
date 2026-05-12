---
title: Ui Color Manager
sidebar_label: Ui Color Manager
category: uncategorized
---

# Ui Color Manager

UiColorManager provides centralized access to theme colors and manages color bindings for views. It enables applications to query colors by identifier and automatically update views when the theme changes.

## Table of Contents

- [Accessing the Singleton](#accessing-the-singleton)
- [Querying Theme Colors](#querying-theme-colors)
- [Binding Colors to Views](#binding-colors-to-views)
- [Overriding Theme Colors](#overriding-theme-colors)
- [Cache Management](#cache-management)

## Accessing the Singleton

`UiColorManager` is a handle-based singleton. Use `UiColorManager::Get()` to obtain the instance:

```cpp
#include <dali-ui-foundation/public-api/ui-color-manager.h>

using namespace Dali::Ui;

// Get the singleton instance
UiColorManager manager = UiColorManager::Get();
```

The default constructor creates an uninitialized handle:

```cpp
UiColorManager uninitialized;  // !uninitialized == true

// Copy and move semantics are supported
UiColorManager copy = UiColorManager::Get();
UiColorManager moved(std::move(copy));
```

To downcast from a `BaseHandle`:

```cpp
BaseHandle handle = UiColorManager::Get();
UiColorManager manager = UiColorManager::DownCast(handle);
```

## Querying Theme Colors

Use `GetColor()` to retrieve RGBA values by their string identifier:

```cpp
UiColorManager manager = UiColorManager::Get();

// Returns Vector4::ZERO if not found
Vector4 primary = manager.GetColor("Primary");

// With output parameter for error checking
Vector4 background;
if (manager.GetColor("Background", background)) {
  // Color found, use background
}
```

The default theme provides colors such as `"Primary"`, `"Background"`, and `"Outline"`.

## Binding Colors to Views

Color bindings associate a `UiColor` with a view, automatically invoking a callback when the theme changes. This enables dynamic theming without manual refresh logic.

### Registering a Binding

Use `RegisterBinding()` to associate a callback with a binding identifier, then `SetBindingColor()` to set the color:

```cpp
#include <dali-ui-foundation/public-api/ui-color.h>
#include <dali-ui-foundation/public-api/view.h>

class MyControl {
public:
  void Initialize() {
    UiColorManager manager = UiColorManager::Get();
    mView = View::New();

    // Register a binding with a callback
    manager.RegisterBinding(
      mView,
      "BackgroundColor",
      ColorCallback::New(this, &MyControl::OnBackgroundColorChanged)
    );

    // Set the color for the binding
    UiColor color("Primary");
    manager.SetBindingColor(mView, "BackgroundColor", color);
  }

  void OnBackgroundColorChanged(const Vector4& color) {
    mView.SetBackgroundColor(color);
  }

private:
  Dali::Ui::View mView;
};
```

### Querying Binding State

Check if a binding exists and retrieve its current color:

```cpp
UiColorManager manager = UiColorManager::Get();
View view = View::New();

// Check if binding exists
if (manager.HasBinding(view, "BackgroundColor")) {
  UiColor boundColor;
  if (manager.GetBindingColor(view, "BackgroundColor", boundColor)) {
    // Use boundColor
  }
}
```

### Clearing Bindings

Remove individual bindings or all bindings for a view:

```cpp
UiColorManager manager = UiColorManager::Get();
View view = View::New();

// Clear a specific binding
manager.ClearBinding(view, "BackgroundColor");

// Clear all bindings for this view
manager.ClearBindings(view);
```

## Overriding Theme Colors

`SetColorOverride()` installs a function that intercepts all color lookups. This is useful for implementing custom theming or accessibility features.

```cpp
// Override function signature: bool(StringView colorId, Vector4& outColor)
bool MyColorOverride(Dali::StringView colorId, Dali::Vector4& outColor) {
  if (colorId == "Primary") {
    outColor = Dali::Vector4(1.0f, 0.0f, 0.0f, 1.0f);  // Red
    return true;  // Override applied
  }
  return false;  // Fall through to theme
}

void EnableCustomTheme() {
  UiColorManager::Get().SetColorOverride(MyColorOverride);
}

void DisableCustomTheme() {
  UiColorManager::Get().ClearColorOverride();
}
```

When an override is set or cleared, all existing view bindings are automatically refreshed with the new colors.

Passing `nullptr` to `SetColorOverride()` is equivalent to calling `ClearColorOverride()`:

```cpp
manager.SetColorOverride(nullptr);  // Clears any existing override
```

**Note:** The override function must be a plain function pointer (free function or stateless lambda). Lambdas with captures are not accepted.

## Cache Management

`UiColorManager` caches resolved colors for performance. When an override function changes its internal state, invalidate the cache to force re-resolution:

```cpp
UiColorManager manager = UiColorManager::Get();

// Invalidate all cached colors
manager.InvalidateCache();

// Invalidate a specific UiColor's cache
UiColor primary("Primary");
manager.InvalidateCache(primary);
```

This ensures subsequent calls to `UiColor::GetRgba()` return updated values based on the current override or theme state.
