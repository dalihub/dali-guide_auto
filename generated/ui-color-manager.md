---
title: Ui Color Manager
sidebar_label: Ui Color Manager
category: styling-theme-config
---

# Ui Color Manager

`UiColorManager` provides centralized access to the application's color table, enabling theme-driven color lookups and dynamic color bindings for views.

## Table of Contents

- [Accessing the Color Manager](#accessing-the-color-manager)
- [Querying Theme Colors](#querying-theme-colors)
- [Binding Colors to Views](#binding-colors-to-views)
- [Overriding Theme Colors](#overriding-theme-colors)
- [Cache Invalidation](#cache-invalidation)

## Accessing the Color Manager

`UiColorManager` is a singleton. Obtain the instance using `UiColorManager::Get()`:

```cpp
#include <dali-ui-foundation/public-api/ui-color-manager.h>

using namespace Dali::Ui;

UiColorManager manager = UiColorManager::Get();
```

The returned handle is valid for the lifetime of the application. Copy and move semantics are supported:

```cpp
UiColorManager copy = UiColorManager::Get();  // Copy constructor
UiColorManager moved(std::move(copy));         // Move constructor
```

To downcast a `BaseHandle` to `UiColorManager`:

```cpp
BaseHandle handle = UiColorManager::Get();
UiColorManager manager = UiColorManager::DownCast(handle);
```

## Querying Theme Colors

### Retrieving Colors by ID

Use `GetColor()` to look up a color by its string identifier. The method returns the resolved RGBA value, or `Vector4::ZERO` if the color is not found:

```cpp
UiColorManager manager = UiColorManager::Get();

Vector4 primary = manager.GetColor("Primary");
```

An overload provides a boolean return to indicate success:

```cpp
Vector4 outColor;
bool found = manager.GetColor("Primary", outColor);

if (found) {
  // Use outColor
}
```

### Default Theme Colors

The default theme provides standard color identifiers:

- `"Primary"` — Primary accent color
- `"Background"` — Background color
- `"Outline"` — Outline/border color

```cpp
Vector4 primary, background, outline;

manager.GetColor("Primary", primary);
manager.GetColor("Background", background);
manager.GetColor("Outline", outline);
```

## Binding Colors to Views

Color bindings associate a `UiColor` with a view, allowing the view to update automatically when the theme changes.

### Registering a Binding

Call `RegisterBinding()` to associate a callback with a binding identifier, then `SetBindingColor()` to assign the color:

```cpp
void OnColorChanged(const Vector4& color) {
  // Apply the resolved color to your view
}

// Register the binding
UiColorManager manager = UiColorManager::Get();
View view = View::New();

manager.RegisterBinding(view, "BackgroundColor",
                        ColorCallback::New(OnColorChanged));

// Set the bound color
UiColor color("Primary");
manager.SetBindingColor(view, "BackgroundColor", color);
```

The callback is invoked when the theme changes, or when a color override is set or cleared. If a binding with the same `bindingId` already exists, `RegisterBinding()` replaces it. The callback is not invoked immediately upon registration.

The `UiColor` passed to `SetBindingColor()` must have a color ID (constructed with a string identifier). If no binding with the given `bindingId` exists, `SetBindingColor()` is a no-op.

### Checking and Retrieving Bindings

Use `HasBinding()` to check if a binding exists:

```cpp
if (manager.HasBinding(view, "BackgroundColor")) {
  // Binding exists
}
```

Use `GetBindingColor()` to retrieve the `UiColor` associated with a binding:

```cpp
UiColor outColor;
if (manager.GetBindingColor(view, "BackgroundColor", outColor)) {
  String colorId = outColor.GetColorId();
  // Use colorId
}
```

### Clearing Bindings

Remove a single binding with `ClearBinding()`:

```cpp
manager.ClearBinding(view, "BackgroundColor");
```

Remove all bindings for a view with `ClearBindings()`:

```cpp
manager.ClearBindings(view);
```

## Overriding Theme Colors

`SetColorOverride()` registers a function that intercepts all color lookups before the theme loader. This enables runtime color customization for features like dark mode or accessibility.

### Defining an Override Function

The override function must match the `ColorOverrideFunc` signature:

```cpp
bool OverridePrimary(StringView colorId, Vector4& outColor) {
  if (colorId == "Primary") {
    outColor = Vector4(1.0f, 0.0f, 0.0f, 1.0f);  // Red
    return true;
  }
  return false;  // Fall through to theme
}
```

### Setting and Clearing Overrides

```cpp
UiColorManager manager = UiColorManager::Get();

// Set override
manager.SetColorOverride(OverridePrimary);

Vector4 color = manager.GetColor("Primary");  // Returns red

// Clear override
manager.ClearColorOverride();

// Alternatively, pass nullptr to clear
manager.SetColorOverride(nullptr);
```

When an override is set or cleared, all existing view bindings are immediately refreshed with the new color values.

### Override Behavior

- Return `true` to use `outColor` and skip the theme loader
- Return `false` to proceed with normal theme lookup
- Only free functions or stateless lambdas are accepted (no captures)

```cpp
// Stateless lambda is acceptable
manager.SetColorOverride([](StringView id, Vector4& out) -> bool {
  if (id == "Primary") {
    out = Vector4(0.0f, 1.0f, 0.0f, 1.0f);
    return true;
  }
  return false;
});
```

## Cache Invalidation

When an override function's behavior changes without calling `SetColorOverride()` again, invalidate the cache to ensure subsequent lookups re-resolve colors.

### Invalidating All Cached Colors

```cpp
manager.InvalidateCache();
```

### Invalidating a Specific Color

```cpp
UiColor color("Primary");
manager.InvalidateCache(color);
```

This is useful when the override function's internal state has changed and previously resolved colors may now be stale. If the `UiColor` holds a direct RGBA value (not a color ID), `InvalidateCache()` is a no-op.
