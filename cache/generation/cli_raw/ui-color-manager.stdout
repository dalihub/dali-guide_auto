---
title: Ui Color Manager
sidebar_label: Ui Color Manager
category: styling-theme-config
---

# Ui Color Manager

`Dali::Ui::UiColorManager` lets a dali-ui app resolve theme colors, override color lookup, and keep `Dali::Ui::View` color bindings synchronized with theme changes.

## Table of Contents

- [Resolving Theme Colors](#resolving-theme-colors)
- [Overriding Theme Color Lookup](#overriding-theme-color-lookup)
- [Binding Colors to Views](#binding-colors-to-views)
- [Clearing and Inspecting Bindings](#clearing-and-inspecting-bindings)
- [Cache Invalidation](#cache-invalidation)

## Resolving Theme Colors

Use the singleton returned by `Dali::Ui::UiColorManager::Get()` when application code needs to resolve a color ID from the active theme. `Dali::Ui::UiColorManager::GetColor()` has a direct-return form and a checked out-parameter form.

```cpp
#include <dali-ui/dali-ui.h>

Dali::Vector4 ResolvePrimaryColor()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  return colorManager.GetColor("Primary");
}

bool TryResolveBackgroundColor(Dali::Vector4& outBackground)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  return colorManager.GetColor("Background", outBackground);
}
```

The direct-return `Dali::Ui::UiColorManager::GetColor()` overload returns `Dali::Vector4::ZERO` when the ID is not found. Use the checked overload when fallback behavior matters.

```cpp
#include <dali-ui/dali-ui.h>

Dali::Vector4 ResolvePanelColor()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  Dali::Vector4 panel;
  if(colorManager.GetColor("Panel", panel))
  {
    return panel;
  }

  return Dali::Ui::UiColor(0.08f, 0.08f, 0.08f, 1.0f).GetRgba();
}
```

A default-constructed `Dali::Ui::UiColorManager` is an uninitialized handle. Application code should use `Dali::Ui::UiColorManager::Get()` for the active global manager.

```cpp
#include <dali-ui/dali-ui.h>

Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
```

## Overriding Theme Color Lookup

Use `Dali::Ui::UiColorManager::SetColorOverride()` when the app needs runtime color substitution on top of the current theme. The override function is called before normal theme lookup. Return `true` to provide the color, or `false` to let normal theme lookup continue.

```cpp
#include <dali-ui/dali-ui.h>

bool AppColorOverride(Dali::StringView colorId, Dali::Vector4& outColor)
{
  if(colorId == "Primary")
  {
    outColor = Dali::Vector4(1.0f, 0.0f, 0.0f, 1.0f);
    return true;
  }

  return false;
}

void EnableHighContrastPrimaryColor()
{
  Dali::Ui::UiColorManager::Get().SetColorOverride(AppColorOverride);
}

void DisableHighContrastPrimaryColor()
{
  Dali::Ui::UiColorManager::Get().ClearColorOverride();
}
```

`Dali::Ui::UiColorManager::SetColorOverride(nullptr)` also clears the override. `Dali::Ui::UiColorManager::ClearColorOverride()` is clearer when the intent is explicit.

```cpp
#include <dali-ui/dali-ui.h>

void ResetColorOverride()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  colorManager.SetColorOverride(nullptr);
}
```

Setting or clearing an override refreshes registered view bindings, so bound colors can be re-applied without waiting for a separate theme change.

## Binding Colors to Views

For app-facing UI, keep the target object as `Dali::Ui::View`. `Dali::Ui::UiColorManager::RegisterBinding()` associates a binding ID with a callback. `Dali::Ui::UiColorManager::SetBindingColor()` stores the `Dali::Ui::UiColor` for that binding.

```cpp
#include <dali-ui/dali-ui.h>

void ApplyResolvedAccentColor(const Dali::Vector4& color)
{
  // Apply the resolved RGBA value to app-owned view state here.
}

void BindAccentColor(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.RegisterBinding(
    view,
    "AccentColor",
    Dali::Ui::ColorCallback::New(ApplyResolvedAccentColor));

  colorManager.SetBindingColor(
    view,
    "AccentColor",
    Dali::Ui::UiColor(0.20f, 0.45f, 0.90f, 1.0f));
}
```

`Dali::Ui::UiColorManager::RegisterBinding()` does not immediately invoke the callback. It records how the binding should be refreshed later. `Dali::Ui::UiColorManager::SetBindingColor()` updates the stored color for a binding that already exists.

```cpp
#include <dali-ui/dali-ui.h>

void ApplyResolvedStatusColor(const Dali::Vector4& color)
{
}

void RebindStatusColor(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.RegisterBinding(
    view,
    "StatusColor",
    Dali::Ui::ColorCallback::New(ApplyResolvedStatusColor));

  colorManager.SetBindingColor(
    view,
    "StatusColor",
    Dali::Ui::UiColor(0.0f, 0.6f, 0.2f, 1.0f));

  colorManager.SetBindingColor(
    view,
    "StatusColor",
    Dali::Ui::UiColor(0.8f, 0.2f, 0.1f, 1.0f));
}
```

## Clearing and Inspecting Bindings

Use `Dali::Ui::UiColorManager::HasBinding()` to test whether a named binding exists for a `Dali::Ui::View`. Use `Dali::Ui::UiColorManager::GetBindingColor()` when the app needs the stored `Dali::Ui::UiColor`.

```cpp
#include <dali-ui/dali-ui.h>

bool HasAccentBinding(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  return colorManager.HasBinding(view, "AccentColor");
}

bool ReadAccentBinding(Dali::Ui::View view, Dali::Ui::UiColor& outColor)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  return colorManager.GetBindingColor(view, "AccentColor", outColor);
}
```

Use `Dali::Ui::UiColorManager::ClearBinding()` to remove one binding. Use `Dali::Ui::UiColorManager::ClearBindings()` when the view should no longer participate in any color-manager refreshes.

```cpp
#include <dali-ui/dali-ui.h>

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

Clearing a missing binding is safe for cleanup paths. A common pattern is to clear all bindings when a view-owned component is being disconnected from app styling.

## Cache Invalidation

`Dali::Ui::UiColorManager::InvalidateCache()` clears cached resolved colors. Use it after the data used by the override function changes while the function pointer itself remains installed.

```cpp
#include <dali-ui/dali-ui.h>

namespace
{
bool gUseAlertColor = false;

bool DynamicColorOverride(Dali::StringView colorId, Dali::Vector4& outColor)
{
  if(colorId == "Primary" && gUseAlertColor)
  {
    outColor = Dali::Vector4(1.0f, 0.1f, 0.1f, 1.0f);
    return true;
  }

  return false;
}
}

void InstallDynamicColorOverride()
{
  Dali::Ui::UiColorManager::Get().SetColorOverride(DynamicColorOverride);
}

void SetAlertColorMode(bool enabled)
{
  gUseAlertColor = enabled;
  Dali::Ui::UiColorManager::Get().InvalidateCache();
}
```

When only one `Dali::Ui::UiColor` needs to be refreshed, use `Dali::Ui::UiColorManager::InvalidateCache(const Dali::Ui::UiColor&)`.

```cpp
#include <dali-ui/dali-ui.h>

void RefreshOneCachedColor()
{
  Dali::Ui::UiColor accent(0.2f, 0.4f, 0.9f, 1.0f);

  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();
  colorManager.InvalidateCache(accent);
}
```
