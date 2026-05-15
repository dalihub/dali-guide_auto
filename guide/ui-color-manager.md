---
title: Ui Color Manager
sidebar_label: Ui Color Manager
category: styling-theme-config
---

# Ui Color Manager

`Dali::Ui::UiColorManager` resolves named theme colors, stores view color bindings, and supports application-level color lookup overrides.

## Table of Contents

- [Getting the Color Manager](#getting-the-color-manager)
- [Looking Up Theme Colors](#looking-up-theme-colors)
- [Binding Colors to Views](#binding-colors-to-views)
- [Removing View Bindings](#removing-view-bindings)
- [Overriding Theme Colors](#overriding-theme-colors)
- [Refreshing Cached Colors](#refreshing-cached-colors)

## Getting the Color Manager

`Dali::Ui::UiColorManager` is a handle-based singleton. A default-constructed `Dali::Ui::UiColorManager` is uninitialized; application code should normally use `Dali::Ui::UiColorManager::Get()`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ConfigureColors()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  if(colorManager)
  {
    Dali::Vector4 primary = colorManager.GetColor("Primary");
  }
}
```

Use `Dali::Ui::UiColorManager::DownCast()` when a `Dali::BaseHandle` is known to refer to the color manager.

```cpp
void UseManagerHandle(Dali::BaseHandle handle)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::DownCast(handle);

  if(colorManager)
  {
    Dali::Vector4 background = colorManager.GetColor("Background");
  }
}
```

## Looking Up Theme Colors

Use `Dali::Ui::UiColorManager::GetColor()` to resolve a color identifier from the active theme. The returned `Dali::Vector4` contains RGBA components, or `Dali::Vector4::ZERO` when the identifier is not found.

```cpp
Dali::Vector4 ResolvePrimaryColor()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  return colorManager.GetColor("Primary");
}
```

`Dali::Ui::UiColorManager::GetColor()` is useful when application logic needs a concrete RGBA value, while `Dali::Ui::UiColor` is the value type used to store either direct RGBA values or theme color identifiers.

```cpp
Dali::Ui::UiColor MakePressedColor()
{
  Dali::Ui::UiColor pressedColor(0.0f, 0.45f, 1.0f, 1.0f);

  return pressedColor;
}
```

## Binding Colors to Views

A binding associates a caller-defined binding id with a view object and a color callback. `Dali::Ui::UiColorManager::RegisterBinding()` registers the callback, and `Dali::Ui::UiColorManager::SetBindingColor()` stores the `Dali::Ui::UiColor` used by that binding. The callback is not invoked immediately when the binding is registered or updated.

Prefer `Dali::Ui::View` as the app-facing object passed to `Dali::Ui::UiColorManager::RegisterBinding()`, `Dali::Ui::UiColorManager::SetBindingColor()`, and the binding query methods. Use a `Dali::Ui::UiColor` constructed from a color id for bindings that should be reapplied when theme colors or color overrides change.

```cpp
void RegisterViewColorBinding(
  Dali::Ui::View view,
  Dali::Ui::ColorCallback callback,
  const Dali::Ui::UiColor& color)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.RegisterBinding(view, "BackgroundColor", callback);
  colorManager.SetBindingColor(view, "BackgroundColor", color);
}
```

Use `Dali::Ui::UiColorManager::HasBinding()` when setup code may be called more than once for the same view and binding id.

```cpp
void EnsureViewColorBinding(
  Dali::Ui::View view,
  Dali::Ui::ColorCallback callback,
  const Dali::Ui::UiColor& color)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  if(!colorManager.HasBinding(view, "BackgroundColor"))
  {
    colorManager.RegisterBinding(view, "BackgroundColor", callback);
  }

  colorManager.SetBindingColor(view, "BackgroundColor", color);
}
```

Use `Dali::Ui::UiColorManager::GetBindingColor()` to read back the `Dali::Ui::UiColor` stored for a binding.

```cpp
bool ReadViewColorBinding(Dali::Ui::View view, Dali::Ui::UiColor& outColor)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  return colorManager.GetBindingColor(view, "BackgroundColor", outColor);
}
```

## Removing View Bindings

Use `Dali::Ui::UiColorManager::ClearBinding()` to remove one named binding from a view.

```cpp
void RemoveBackgroundBinding(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.ClearBinding(view, "BackgroundColor");
}
```

Use `Dali::Ui::UiColorManager::ClearBindings()` when a view should no longer participate in any color-manager binding owned by that view.

```cpp
void RemoveAllColorBindings(Dali::Ui::View view)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.ClearBindings(view);
}
```

## Overriding Theme Colors

Use `Dali::Ui::UiColorManager::SetColorOverride()` to install an application-level color lookup override. The override is a plain function pointer. It receives the requested color id and writes a resolved RGBA value when it handles that id.

```cpp
bool OverrideApplicationColors(Dali::StringView colorId, Dali::Vector4& outColor)
{
  if(colorId == "Primary")
  {
    outColor = Dali::Vector4(1.0f, 0.0f, 0.0f, 1.0f);
    return true;
  }

  return false;
}

void EnableApplicationColorOverride()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.SetColorOverride(OverrideApplicationColors);
}
```

When the override returns `true`, that `Dali::Vector4` is used for the lookup. When it returns `false`, normal theme lookup continues. Setting or clearing an override also refreshes existing view bindings.

```cpp
Dali::Vector4 ReadOverriddenPrimary()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.SetColorOverride(OverrideApplicationColors);

  return colorManager.GetColor("Primary");
}
```

Use `Dali::Ui::UiColorManager::ClearColorOverride()` to return to theme-provided colors.

```cpp
void DisableApplicationColorOverride()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.ClearColorOverride();
}
```

Passing `nullptr` to `Dali::Ui::UiColorManager::SetColorOverride()` also clears the override.

```cpp
void ResetApplicationColorOverride()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.SetColorOverride(nullptr);
}
```

## Refreshing Cached Colors

`Dali::Ui::UiColorManager::InvalidateCache()` clears cached resolved color values. Use it after changing state that affects a registered override function without replacing the override function itself.

```cpp
void ThemeOverrideStateChanged()
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.InvalidateCache();
}
```

Use `Dali::Ui::UiColorManager::InvalidateCache(const Dali::Ui::UiColor&)` when the application has a token-backed `Dali::Ui::UiColor` whose cached resolution should be refreshed. The call is a no-op for direct RGBA colors.

```cpp
void RefreshOneColor(const Dali::Ui::UiColor& color)
{
  Dali::Ui::UiColorManager colorManager = Dali::Ui::UiColorManager::Get();

  colorManager.InvalidateCache(color);
}
```
