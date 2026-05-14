---
title: Ui Color Manager
sidebar_label: Ui Color Manager
category: styling-theme-config
---

# Ui Color Manager

The `UiColorManager` provides centralized color management for dali-ui applications, enabling theme-based color resolution, view-to-color bindings, and runtime color overrides.

## Table of Contents

- [Getting Started](#getting-started)
- [Resolving Theme Colors](#resolving-theme-colors)
- [Binding Colors to Views](#binding-colors-to-views)
- [Overriding Colors at Runtime](#overriding-colors-at-runtime)

## Getting Started

`UiColorManager` is a singleton that manages color resolution for the application. Access it through the static `Get()` method.

```cpp
#include <dali-ui-foundation/public-api/ui-color-manager.h>

using namespace Dali::Ui;

// Get the singleton instance
UiColorManager manager = UiColorManager::Get();
```

The manager supports copy and move semantics, allowing you to pass it by value:

```cpp
UiColorManager manager1 = UiColorManager::Get();
UiColorManager manager2(manager1);  // Copy constructor

// Both refer to the same singleton
DALI_TEST_CHECK(manager1 == manager2);
```

You can also downcast from a `BaseHandle`:

```cpp
BaseHandle handle = UiColorManager::Get();
UiColorManager manager = UiColorManager::DownCast(handle);
```

## Resolving Theme Colors

The color manager resolves color identifiers to RGBA values using the current theme. Use `GetColor()` to retrieve colors by their string identifier.

### Basic Color Lookup

```cpp
UiColorManager manager = UiColorManager::Get();

// Get color by ID (returns Vector4::ZERO if not found)
Vector4 primary = manager.GetColor("Primary");
```

### Color Lookup with Success Check

Use the output parameter overload to check if a color exists:

```cpp
UiColorManager manager = UiColorManager::Get();

Vector4 color;
bool found = manager.GetColor("Primary", color);

if (found)
{
  // Use the resolved color
}
```

### Default Theme Colors

The default theme provides standard color identifiers:

```cpp
UiColorManager manager = UiColorManager::Get();

Vector4 primary = manager.GetColor("Primary");
Vector4 background = manager.GetColor("Background");
Vector4 outline = manager.GetColor("Outline");
```

If a color identifier does not exist, `GetColor()` returns `Vector4::ZERO`.

## Binding Colors to Views

Color bindings connect views to theme colors through callback functions. When the color changes (e.g., theme switch or override), the callback applies the new color to the view.

### Registering a Binding

Create a binding with `RegisterBinding()` and supply a callback that receives the resolved color:

```cpp
void OnColorChanged(const Vector4& color)
{
  // Apply the color to your view
}

UiColorManager manager = UiColorManager::Get();
View view = View::New();

// Register the binding with a callback
manager.RegisterBinding(view, "TextColor", ColorCallback::New(OnColorChanged));
```

### Setting the Binding Color

After registration, assign a `UiColor` to the binding:

```cpp
// Using a color identifier (token)
UiColor color("Primary");
manager.SetBindingColor(view, "TextColor", color);

// Or using a predefined constant
manager.SetBindingColor(view, "TextColor", UiColor::PRIMARY);
```

### Retrieving a Binding's Color

Check the current color assigned to a binding:

```cpp
UiColorManager manager = UiColorManager::Get();
View view = View::New();

UiColor outColor;
bool found = manager.GetBindingColor(view, "TextColor", outColor);

if (found)
{
  std::string colorId = outColor.GetColorId();
  Vector4 rgba = outColor.GetRgba();
}
```

### Checking if a Binding Exists

```cpp
UiColorManager manager = UiColorManager::Get();
View view = View::New();

if (manager.HasBinding(view, "TextColor"))
{
  // Binding exists
}
```

### Updating a Binding

Call `SetBindingColor()` again to change the color for an existing binding:

```cpp
// Initial color
manager.SetBindingColor(view, "TextColor", UiColor::PRIMARY);

// Update to a different color
manager.SetBindingColor(view, "TextColor", UiColor::BACKGROUND);
```

### Clearing Bindings

Remove a single binding:

```cpp
manager.ClearBinding(view, "TextColor");
```

Remove all bindings for a view:

```cpp
manager.ClearBindings(view);
```

## Overriding Colors at Runtime

Use color overrides to intercept color resolution for testing, accessibility, or dynamic theming.

### Setting a Color Override

Provide a callback that receives the color ID and returns the overridden color:

```cpp
bool OverridePrimary(StringView colorId, Vector4& outColor)
{
  if (colorId == "Primary")
  {
    outColor = Vector4(1.0f, 0.0f, 0.0f, 1.0f);  // Red
    return true;
  }
  return false;  // Let other colors resolve normally
}

UiColorManager manager = UiColorManager::Get();
manager.SetColorOverride(OverridePrimary);

// Now "Primary" resolves to red
Vector4 color = manager.GetColor("Primary");  // Returns red
```

### Override with Fallback

When the override callback returns `false`, the color resolves from the theme:

```cpp
bool OverrideSelective(StringView colorId, Vector4& outColor)
{
  if (colorId == "Primary")
  {
    outColor = Vector4(1.0f, 0.0f, 0.0f, 1.0f);
    return true;
  }
  // Return false to let other colors use theme values
  return false;
}
```

### Clearing the Override

Restore normal theme resolution:

```cpp
manager.ClearColorOverride();
```

You can also clear by passing `nullptr`:

```cpp
manager.SetColorOverride(nullptr);
```

### Override Refreshes Bindings

Setting or clearing an override automatically refreshes all registered bindings:

```cpp
// Bind a view to "Primary"
UiColor color("Primary");
manager.RegisterBinding(view, "TextColor", ColorCallback::New(OnColorChanged));
manager.SetBindingColor(view, "TextColor", color);

// Setting override triggers OnColorChanged callback
manager.SetColorOverride(OverridePrimary);

// Clearing override triggers OnColorChanged again with theme color
manager.ClearColorOverride();
```

### Replacing an Override

Calling `SetColorOverride()` replaces any existing override:

```cpp
bool OverrideAll(StringView colorId, Vector4& outColor)
{
  outColor = Vector4(0.0f, 0.0f, 0.0f, 1.0f);  // Black
  return true;
}

manager.SetColorOverride(OverridePrimary);  // First override
manager.SetColorOverride(OverrideAll);      // Replaces with new override