---
title: Ui Color
sidebar_label: Ui Color
category: styling-theme-config
---

# Ui Color

`Dali::Ui::UiColor` represents colors for dali-ui views as either direct RGBA values or theme-resolved color IDs.

## Table of Contents

- [Use `UiColor` with dali-ui views](#use-uicolor-with-dali-ui-views)
- [Choose theme colors or direct RGBA colors](#choose-theme-colors-or-direct-rgba-colors)
- [Inspect and resolve colors](#inspect-and-resolve-colors)
- [Adjust alpha without losing the original color intent](#adjust-alpha-without-losing-the-original-color-intent)

## Use `UiColor` with dali-ui views

In a dali-ui application, use `Dali::Ui::UiColor` with `Dali::Ui::View` color APIs instead of setting raw actor properties. A view can receive a predefined theme color such as `Dali::Ui::UiColor::BACKGROUND` or `Dali::Ui::UiColor::PRIMARY`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyPanelColors(Dali::Ui::View& panel, Dali::Ui::View& action)
{
  panel.SetBackgroundColor(Dali::Ui::UiColor::BACKGROUND);
  action.SetBackgroundColor(Dali::Ui::UiColor::PRIMARY);
}
```

`Dali::Ui::View::SetBackgroundColor` accepts a `const Dali::Ui::UiColor&` and returns the view, so it fits normal dali-ui fluent chaining.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::View MakePrimaryTile()
{
  return Dali::Ui::View::New()
    .SetBackgroundColor(Dali::Ui::UiColor::PRIMARY);
}
```

When a `Dali::Ui::UiColor` contains a color ID, `Dali::Ui::View::SetBackgroundColor` registers an ID-based color binding for that background. When a direct RGBA `Dali::Ui::UiColor` is applied, the direct value is used and any previous ID-based binding for that view background is removed.

## Choose theme colors or direct RGBA colors

Use the predefined ID colors when the color should follow the active theme:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyThemeRoleColors(Dali::Ui::View& root, Dali::Ui::View& primarySurface, Dali::Ui::View& outlineSurface)
{
  root.SetBackgroundColor(Dali::Ui::UiColor::BACKGROUND);
  primarySurface.SetBackgroundColor(Dali::Ui::UiColor::PRIMARY);
  outlineSurface.SetBackgroundColor(Dali::Ui::UiColor::OUTLINE);
}
```

Use the RGBA constructor `Dali::Ui::UiColor(float r, float g, float b, float a = 1.0f)` when a color should be fixed by the application rather than resolved from the theme.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyFixedStateColors(Dali::Ui::View& success, Dali::Ui::View& warning)
{
  const Dali::Ui::UiColor successGreen(0.0f, 0.70f, 0.25f, 1.0f);
  const Dali::Ui::UiColor warningAmber(1.0f, 0.65f, 0.0f);

  success.SetBackgroundColor(successGreen);
  warning.SetBackgroundColor(warningAmber);
}
```

The four RGBA components are floating-point values. The alpha argument defaults to `1.0f`, so `Dali::Ui::UiColor(1.0f, 0.0f, 0.0f)` creates an opaque red color.

## Inspect and resolve colors

`Dali::Ui::UiColor::HasColorId` tells you whether the color is ID-based. `Dali::Ui::UiColor::GetColorId` returns the ID string for ID-based colors, or an empty string for direct RGBA colors.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

bool IsPrimaryRole(const Dali::Ui::UiColor& color)
{
  return color.HasColorId() && color.GetColorId() == "Primary";
}
```

`Dali::Ui::UiColor::GetRgba` resolves the color to a `Dali::Vector4`. Direct RGBA colors return their stored components. ID-based colors are looked up from the current theme when they are resolved.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

float ResolvedAlpha(const Dali::Ui::UiColor& color)
{
  const Dali::Vector4 rgba = color.GetRgba();
  return rgba.a;
}
```

`Dali::Ui::UiColor` also provides `Dali::Ui::UiColor::operator Vector4`, which is equivalent to resolving with `Dali::Ui::UiColor::GetRgba`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Vector4 ResolveForComparison(const Dali::Ui::UiColor& color)
{
  Dali::Vector4 rgba = color;
  return rgba;
}
```

For normal view styling, pass `Dali::Ui::UiColor` to dali-ui view APIs and let the view keep the theme-aware value. Use `Dali::Ui::UiColor::GetRgba` when you specifically need a resolved numeric color, such as for comparison or custom calculations.

## Adjust alpha without losing the original color intent

Use `Dali::Ui::UiColor::WithAlpha` to replace the alpha value and return a new `Dali::Ui::UiColor`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyDimmedPrimary(Dali::Ui::View& view)
{
  const Dali::Ui::UiColor dimmedPrimary = Dali::Ui::UiColor::PRIMARY.WithAlpha(0.40f);
  view.SetBackgroundColor(dimmedPrimary);
}
```

Use `Dali::Ui::UiColor::ScaleAlpha` to multiply the current alpha and return a new `Dali::Ui::UiColor`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyPressedState(Dali::Ui::View& view)
{
  const Dali::Ui::UiColor pressed = Dali::Ui::UiColor::PRIMARY.ScaleAlpha(0.75f);
  view.SetBackgroundColor(pressed);
}
```

Both alpha helpers preserve the distinction between direct RGBA colors and ID-based colors. For example, a color derived from `Dali::Ui::UiColor::PRIMARY` still has the `"Primary"` color ID after alpha adjustment.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

bool KeepsThemeRoleAfterAlphaChange()
{
  const Dali::Ui::UiColor color = Dali::Ui::UiColor::PRIMARY.WithAlpha(0.5f).ScaleAlpha(0.5f);

  return color.HasColorId() && color.GetColorId() == "Primary";
}
```

Alpha values are clamped to the valid range when using `Dali::Ui::UiColor::WithAlpha` or `Dali::Ui::UiColor::ScaleAlpha`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColor MakeVisibleOverlay()
{
  return Dali::Ui::UiColor(0.0f, 0.0f, 0.0f, 0.80f)
    .ScaleAlpha(0.5f);
}
```
