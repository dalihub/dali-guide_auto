---
title: Ui Color Manager
sidebar_label: Ui Color Manager
category: styling-theme-config
---

# Ui Color Manager

`Dali::Ui::UiColorManager` gives a dali-ui app one place to resolve theme colors, keep identifier-based `Dali::Ui::UiColor` values attached to `Dali::Ui::View` objects, and apply temporary color overrides.

## Table of Contents

- [Getting the Color Manager](#getting-the-color-manager)
- [Looking Up Theme Colors](#looking-up-theme-colors)
- [Binding Colors to Views](#binding-colors-to-views)
- [Replacing or Clearing Bindings](#replacing-or-clearing-bindings)
- [Overriding Theme Colors](#overriding-theme-colors)
- [Refreshing Cached Colors](#refreshing-cached-colors)

## Getting the Color Manager

`Dali::Ui::UiColorManager` is a handle-based singleton. Use `Dali::Ui::UiColorManager::Get` when application code needs the current manager. A default-constructed `Dali::Ui::UiColorManager` is an uninitialized handle; it is useful only as a handle variable before assignment.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void PrepareThemeAccess()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  if(manager)
  {
    Dali::Vector4 primary = manager.GetColor("Primary");
  }
}
```

If a manager handle is carried through a generic `Dali::BaseHandle`, recover it with `Dali::Ui::UiColorManager::DownCast`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColorManager ResolveManager(Dali::BaseHandle handle)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::DownCast(handle);

  if(!manager)
  {
    manager = Dali::Ui::UiColorManager::Get();
  }

  return manager;
}
```

## Looking Up Theme Colors

Use `Dali::Ui::UiColorManager::GetColor` to resolve a color identifier from the active theme. The single-return-value overload returns a `Dali::Vector4` RGBA value, or `Dali::Vector4::ZERO` when the color identifier cannot be resolved.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Vector4 ResolvePanelColor()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  Dali::Vector4 background = manager.GetColor("Background");
  if(background == Dali::Vector4::ZERO)
  {
    return Dali::Vector4(0.08f, 0.08f, 0.08f, 1.0f);
  }

  return background;
}
```

For direct RGBA values that do not need theme lookup, construct `Dali::Ui::UiColor` with components.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColor MakeDimOverlayColor()
{
  return Dali::Ui::UiColor(0.0f, 0.0f, 0.0f, 0.45f);
}
```

## Binding Colors to Views

A binding connects a named color slot on an object to a callback. In app-facing code, the target object should usually be a `Dali::Ui::View` handle. Register the binding with `Dali::Ui::UiColorManager::RegisterBinding`, then store an identifier-based `Dali::Ui::UiColor` for that binding with `Dali::Ui::UiColorManager::SetBindingColor`.

`Dali::Ui::UiColorManager::RegisterBinding` does not invoke the callback immediately. The manager invokes registered callbacks when bindings are refreshed, such as after a theme change or after a theme override is set or cleared.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
Dali::Vector4 gLastNavigationColor;

void ApplyNavigationColor(const Dali::Vector4& color)
{
  gLastNavigationColor = color;
}
}

void BindNavigationColor(Dali::Ui::View navigationView)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.RegisterBinding(
    navigationView,
    "NavigationColor",
    Dali::Ui::ColorCallback::New(ApplyNavigationColor));

  manager.SetBindingColor(
    navigationView,
    "NavigationColor",
    Dali::Ui::UiColor("Primary"));
}
```

Use `Dali::Ui::UiColorManager::HasBinding` when setup code may run more than once.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
void ApplyContentColor(const Dali::Vector4& color)
{
}
}

void EnsureContentColorBinding(Dali::Ui::View contentView)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  if(!manager.HasBinding(contentView, "ContentColor"))
  {
    manager.RegisterBinding(
      contentView,
      "ContentColor",
      Dali::Ui::ColorCallback::New(ApplyContentColor));
  }

  manager.SetBindingColor(
    contentView,
    "ContentColor",
    Dali::Ui::UiColor("Background"));
}
```

## Replacing or Clearing Bindings

Calling `Dali::Ui::UiColorManager::SetBindingColor` again with the same `Dali::Ui::View` and binding identifier replaces the stored `Dali::Ui::UiColor` for that binding. If no binding with that identifier exists, `Dali::Ui::UiColorManager::SetBindingColor` has no effect.

Use `Dali::Ui::UiColorManager::GetBindingColor` to inspect the current stored color, `Dali::Ui::UiColorManager::ClearBinding` to remove one binding, and `Dali::Ui::UiColorManager::ClearBindings` to remove all bindings for a view.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
void ApplyAccentColor(const Dali::Vector4& color)
{
}
}

void ReplaceAccentBinding(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.RegisterBinding(
    view,
    "AccentColor",
    Dali::Ui::ColorCallback::New(ApplyAccentColor));

  manager.SetBindingColor(view, "AccentColor", Dali::Ui::UiColor("Primary"));
  manager.SetBindingColor(view, "AccentColor", Dali::Ui::UiColor("Outline"));

  Dali::Ui::UiColor storedColor;
  if(manager.GetBindingColor(view, "AccentColor", storedColor))
  {
    Dali::Vector4 resolvedColor = storedColor.GetRgba();
    ApplyAccentColor(resolvedColor);
  }
}

void ClearAccentBinding(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.ClearBinding(view, "AccentColor");
}

void ClearAllThemeBindings(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.ClearBindings(view);
}
```

## Overriding Theme Colors

`Dali::Ui::UiColorManager::SetColorOverride` installs a function that is called before normal theme lookup. The function receives the color identifier and an output `Dali::Vector4`. Return `true` after writing the output color to replace the theme value, or return `false` to let the normal theme lookup continue.

The override must match `Dali::Ui::ColorOverrideFunc`: a plain function pointer. Use a free function or a stateless lambda.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
bool HighContrastOverride(Dali::StringView colorId, Dali::Vector4& outColor)
{
  if(colorId == "Primary")
  {
    outColor = Dali::Vector4(1.0f, 1.0f, 1.0f, 1.0f);
    return true;
  }

  if(colorId == "Background")
  {
    outColor = Dali::Vector4(0.0f, 0.0f, 0.0f, 1.0f);
    return true;
  }

  return false;
}
}

void EnableHighContrastTheme()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.SetColorOverride(HighContrastOverride);
}

void DisableHighContrastTheme()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.ClearColorOverride();
}
```

Setting a new override replaces the previous override and refreshes existing view bindings. Passing `nullptr` to `Dali::Ui::UiColorManager::SetColorOverride` clears the override, with the same practical effect as calling `Dali::Ui::UiColorManager::ClearColorOverride`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ResetThemeOverride()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.SetColorOverride(nullptr);
}
```

## Refreshing Cached Colors

`Dali::Ui::UiColorManager::InvalidateCache` clears cached resolved color values. Call it when an installed override changes the data it uses internally, so later color resolution reads the current override result. This invalidates cached values only; it does not invoke binding callbacks by itself.

Use the overload that takes a `Dali::Ui::UiColor` when code has a specific color value to invalidate. Direct RGBA colors do not need theme resolution, so this is mainly useful for identifier-based `Dali::Ui::UiColor` values.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

namespace
{
bool gUseWarningPalette = false;

bool WarningOverride(Dali::StringView colorId, Dali::Vector4& outColor)
{
  if(gUseWarningPalette && colorId == "Primary")
  {
    outColor = Dali::Vector4(1.0f, 0.32f, 0.0f, 1.0f);
    return true;
  }

  return false;
}
}

void InstallWarningPaletteOverride()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();

  manager.SetColorOverride(WarningOverride);
}

void SetWarningPaletteEnabled(bool enabled)
{
  gUseWarningPalette = enabled;

  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();
  manager.InvalidateCache();
}

void RefreshOneStoredColor()
{
  Dali::Ui::UiColorManager manager = Dali::Ui::UiColorManager::Get();
  Dali::Ui::UiColor color("Primary");

  manager.InvalidateCache(color);
}
```
