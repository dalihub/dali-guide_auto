---
title: Ui Color Manager
sidebar_label: Ui Color Manager
category: styling-theme-config
---

# Ui Color Manager

`Dali::Ui::UiColorManager` provides app-level access to theme colors and named color bindings for `Dali::Ui::View` based UI code.

## Table of Contents

- [Getting the Color Manager](#getting-the-color-manager)
- [Reading Theme Colors](#reading-theme-colors)
- [Binding Theme Colors to Views](#binding-theme-colors-to-views)
- [Updating and Clearing Bindings](#updating-and-clearing-bindings)
- [Overriding Theme Color Lookup](#overriding-theme-color-lookup)
- [Invalidating Cached Color Values](#invalidating-cached-color-values)

## Getting the Color Manager

Use `Dali::Ui::UiColorManager::Get` to access the singleton manager. A default-constructed `Dali::Ui::UiColorManager` is only an empty handle; application code should normally obtain the live manager through `Dali::Ui::UiColorManager::Get`.

```cpp
#include <dali-ui-foundation/public-api/ui-color-manager.h>

void ConfigureColors()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  if(colorManager)
  {
    // The singleton is ready for color lookup and binding operations.
  }
}
```

`Dali::Ui::UiColorManager::DownCast` is useful when code receives a generic `Dali::BaseHandle` and needs to recover a color manager handle.

```cpp
#include <dali/public-api/object/base-handle.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>

Dali::Ui::UiColorManager AsColorManager(Dali::BaseHandle handle)
{
  return Dali::Ui::UiColorManager::DownCast(handle);
}
```

## Reading Theme Colors

Call `Dali::Ui::UiColorManager::GetColor` with a color identifier to resolve a theme color to an RGBA `Dali::Vector4`.

```cpp
#include <dali/public-api/math/vector4.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>

Dali::Vector4 ReadPrimaryColor()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  return colorManager.GetColor("Primary");
}
```

A common pattern is to centralize color lookup before applying colors to dali-ui views. Keep the app-facing object model as `Dali::Ui::View`, and pass resolved colors into your own view update code.

```cpp
#include <dali/public-api/math/vector4.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>
#include <dali-ui-foundation/public-api/view.h>

void ApplyResolvedPalette(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  Dali::Vector4 primary    = colorManager.GetColor("Primary");
  Dali::Vector4 background = colorManager.GetColor("Background");
  Dali::Vector4 outline    = colorManager.GetColor("Outline");

  // Apply these values through the dali-ui view APIs used by your component.
  (void)view;
  (void)primary;
  (void)background;
  (void)outline;
}
```

## Binding Theme Colors to Views

A binding associates a caller-defined binding ID with a `Dali::Ui::View` and a callback that applies a resolved color. Register the binding with `Dali::Ui::UiColorManager::RegisterBinding`, then store the selected color with `Dali::Ui::UiColorManager::SetBindingColor`.

```cpp
#include <dali/public-api/math/vector4.h>
#include <dali-ui-foundation/public-api/callback.h>
#include <dali-ui-foundation/public-api/ui-color.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>
#include <dali-ui-foundation/public-api/view.h>

class PaletteApplier
{
public:
  void SetAccentColor(const Dali::Vector4& color)
  {
    mAccentColor = color;
  }

  void BindAccentColor(Dali::Ui::View view)
  {
    Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

    colorManager.RegisterBinding(
      view,
      "AccentColor",
      Dali::Ui::ColorCallback::New(this, &PaletteApplier::SetAccentColor));

    colorManager.SetBindingColor(
      view,
      "AccentColor",
      Dali::Ui::UiColor("Primary"));
  }

private:
  Dali::Vector4 mAccentColor;
};
```

`Dali::Ui::UiColorManager::RegisterBinding` does not apply the color immediately. The callback is used when bindings are refreshed, such as after theme lookup behavior changes. If your component needs an immediate value, read with `Dali::Ui::UiColorManager::GetColor` and apply it directly in your component code.

```cpp
#include <dali/public-api/math/vector4.h>
#include <dali-ui-foundation/public-api/callback.h>
#include <dali-ui-foundation/public-api/ui-color.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>
#include <dali-ui-foundation/public-api/view.h>

class HeaderStyle
{
public:
  void SetHeaderColor(const Dali::Vector4& color)
  {
    mHeaderColor = color;
  }

  void Configure(Dali::Ui::View headerView)
  {
    Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

    colorManager.RegisterBinding(
      headerView,
      "HeaderColor",
      Dali::Ui::ColorCallback::New(this, &HeaderStyle::SetHeaderColor));

    colorManager.SetBindingColor(
      headerView,
      "HeaderColor",
      Dali::Ui::UiColor("Primary"));

    SetHeaderColor(colorManager.GetColor("Primary"));
  }

private:
  Dali::Vector4 mHeaderColor;
};
```

## Updating and Clearing Bindings

Use `Dali::Ui::UiColorManager::HasBinding` to check whether a view already has a binding ID, and `Dali::Ui::UiColorManager::GetBindingColor` to retrieve the stored `Dali::Ui::UiColor`.

```cpp
#include <dali-ui-foundation/public-api/ui-color.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>
#include <dali-ui-foundation/public-api/view.h>

bool UsesAccentBinding(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  return colorManager.HasBinding(view, "AccentColor");
}

bool ReadAccentBinding(Dali::Ui::View view, Dali::Ui::UiColor& color)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  return colorManager.GetBindingColor(view, "AccentColor", color);
}
```

Calling `Dali::Ui::UiColorManager::SetBindingColor` again with the same view and binding ID replaces the stored color.

```cpp
#include <dali-ui-foundation/public-api/ui-color.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>
#include <dali-ui-foundation/public-api/view.h>

void SwitchAccentToBackground(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  if(colorManager.HasBinding(view, "AccentColor"))
  {
    colorManager.SetBindingColor(
      view,
      "AccentColor",
      Dali::Ui::UiColor("Background"));
  }
}
```

Use `Dali::Ui::UiColorManager::ClearBinding` to remove one named binding, or `Dali::Ui::UiColorManager::ClearBindings` to remove every binding associated with a view.

```cpp
#include <dali-ui-foundation/public-api/ui-color-manager.h>
#include <dali-ui-foundation/public-api/view.h>

void RemoveAccentBinding(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  colorManager.ClearBinding(view, "AccentColor");
}

void RemoveAllColorBindings(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  colorManager.ClearBindings(view);
}
```

## Overriding Theme Color Lookup

`Dali::Ui::UiColorManager::SetColorOverride` installs a function that is checked before normal theme color lookup. The function receives the color ID and writes the replacement color to the output parameter. Return `true` when the override handles the ID, or `false` to continue with normal theme lookup.

```cpp
#include <dali/public-api/common/dali-string-view.h>
#include <dali/public-api/math/vector4.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>

bool UseHighContrastPrimary(Dali::StringView colorId, Dali::Vector4& outColor)
{
  if(colorId == "Primary")
  {
    outColor = Dali::Vector4(1.0f, 1.0f, 1.0f, 1.0f);
    return true;
  }

  return false;
}

void EnableHighContrastColors()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  colorManager.SetColorOverride(UseHighContrastPrimary);
}
```

The override is a plain function pointer. Use a free function or a stateless lambda that can convert to that function pointer type.

```cpp
#include <dali/public-api/math/vector4.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>

void EnableBlackThemePreview()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.SetColorOverride(
    [](Dali::StringView, Dali::Vector4& outColor) -> bool
    {
      outColor = Dali::Vector4(0.0f, 0.0f, 0.0f, 1.0f);
      return true;
    });
}
```

Use `Dali::Ui::UiColorManager::ClearColorOverride` to return to normal theme lookup. Passing `nullptr` to `Dali::Ui::UiColorManager::SetColorOverride` also clears the override.

```cpp
#include <dali-ui-foundation/public-api/ui-color-manager.h>

void DisableColorOverride()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.ClearColorOverride();

  // Equivalent:
  colorManager.SetColorOverride(nullptr);
}
```

## Invalidating Cached Color Values

Use `Dali::Ui::UiColorManager::InvalidateCache` when color resolution should be recomputed. This is especially useful when an installed override function changes the values it returns.

```cpp
#include <dali-ui-foundation/public-api/ui-color-manager.h>

void RefreshAllResolvedColors()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  colorManager.InvalidateCache();
}
```

To invalidate one token-backed `Dali::Ui::UiColor`, pass that color to `Dali::Ui::UiColorManager::InvalidateCache`.

```cpp
#include <dali-ui-foundation/public-api/ui-color.h>
#include <dali-ui-foundation/public-api/ui-color-manager.h>

void RefreshPrimaryOnly()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  Dali::Ui::UiColor primary("Primary");

  colorManager.InvalidateCache(primary);
}
```
