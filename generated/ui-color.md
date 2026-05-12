---
title: Ui Color
sidebar_label: Ui Color
category: styling-theme-config
---

# Ui Color

`Dali::Ui::UiColor` lets dali-ui applications use direct RGBA colors or theme-resolved color IDs through `Dali::Ui::View` styling APIs.

## Table of Contents

- [Choosing Theme Colors for Views](#choosing-theme-colors-for-views)
- [Creating Direct RGBA Colors](#creating-direct-rgba-colors)
- [Working with Alpha](#working-with-alpha)
- [Inspecting and Resolving Colors](#inspecting-and-resolving-colors)
- [Copying and Reusing Colors](#copying-and-reusing-colors)

## Choosing Theme Colors for Views

Use `Dali::Ui::UiColor` as the color value type when styling dali-ui views. The built-in theme color constants `Dali::Ui::UiColor::PRIMARY`, `Dali::Ui::UiColor::BACKGROUND`, and `Dali::Ui::UiColor::OUTLINE` are ID-based colors. They keep their color ID and resolve through the current theme when their RGBA value is needed.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyThemeColors(Dali::Ui::View rootView, Dali::Ui::View contentView)
{
  rootView.SetBackgroundColor(Dali::Ui::UiColor::BACKGROUND);

  contentView
    .SetBackgroundColor(Dali::Ui::UiColor::PRIMARY)
    .SetBorderlineColor(Dali::Ui::UiColor::OUTLINE);
}
```

The predefined color IDs are stable application-facing names:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ReadBuiltInColorIds()
{
  Dali::String primaryId    = Dali::Ui::UiColor::PRIMARY.GetColorId();
  Dali::String backgroundId = Dali::Ui::UiColor::BACKGROUND.GetColorId();
  Dali::String outlineId    = Dali::Ui::UiColor::OUTLINE.GetColorId();

  bool primaryIsThemed = Dali::Ui::UiColor::PRIMARY.HasColorId();
  bool backgroundIsThemed = Dali::Ui::UiColor::BACKGROUND.HasColorId();
  bool outlineIsThemed = Dali::Ui::UiColor::OUTLINE.HasColorId();

  (void)primaryId;
  (void)backgroundId;
  (void)outlineId;
  (void)primaryIsThemed;
  (void)backgroundIsThemed;
  (void)outlineIsThemed;
}
```

## Creating Direct RGBA Colors

For fixed colors that should not follow a theme ID, construct `Dali::Ui::UiColor` with red, green, blue, and optional alpha components. Component values use the usual normalized float range.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyDirectColors(Dali::Ui::View warningView, Dali::Ui::View overlayView)
{
  Dali::Ui::UiColor warningRed(1.0f, 0.0f, 0.0f, 1.0f);
  Dali::Ui::UiColor translucentBlack(0.0f, 0.0f, 0.0f, 0.4f);

  warningView.SetBackgroundColor(warningRed);
  overlayView.SetBackgroundColor(translucentBlack);
}
```

When alpha is omitted, `Dali::Ui::UiColor::UiColor(float r, float g, float b, float a=1.0f)` uses an alpha value of `1.0f`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColor MakeOpaqueAccent()
{
  return Dali::Ui::UiColor(0.2f, 0.4f, 0.9f);
}
```

## Working with Alpha

`Dali::Ui::UiColor::WithAlpha` returns a new `Dali::Ui::UiColor` with alpha replaced. `Dali::Ui::UiColor::ScaleAlpha` returns a new color with alpha multiplied by the given factor. The original color is not modified.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplyLayerColors(Dali::Ui::View panelView, Dali::Ui::View pressedView)
{
  Dali::Ui::UiColor panelColor = Dali::Ui::UiColor::BACKGROUND.WithAlpha(0.95f);
  Dali::Ui::UiColor pressedColor = Dali::Ui::UiColor::PRIMARY.ScaleAlpha(0.35f);

  panelView.SetBackgroundColor(panelColor);
  pressedView.SetBackgroundColor(pressedColor);
}
```

Alpha operations can be chained when building a derived state color.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColor MakePressedOutlineColor()
{
  return Dali::Ui::UiColor::OUTLINE
    .WithAlpha(0.8f)
    .ScaleAlpha(0.5f);
}
```

For direct RGBA colors, `Dali::Ui::UiColor::WithAlpha` replaces the stored alpha and `Dali::Ui::UiColor::ScaleAlpha` multiplies it.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColor MakeDisabledColor()
{
  Dali::Ui::UiColor enabledColor(0.3f, 0.5f, 0.7f, 1.0f);
  return enabledColor.ScaleAlpha(0.4f);
}
```

## Inspecting and Resolving Colors

Use `Dali::Ui::UiColor::HasColorId` to distinguish theme ID colors from direct RGBA colors. Use `Dali::Ui::UiColor::GetColorId` when you need the ID string, and `Dali::Ui::UiColor::GetRgba` when you need the resolved RGBA value.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void InspectColor(const Dali::Ui::UiColor& color)
{
  if(color.HasColorId())
  {
    Dali::String colorId = color.GetColorId();
    (void)colorId;
  }

  Dali::Vector4 rgba = color.GetRgba();
  (void)rgba;
}
```

`Dali::Ui::UiColor::operator Vector4` is equivalent to resolving the color as RGBA. Prefer `Dali::Ui::UiColor::GetRgba` when readability matters.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Vector4 ResolveForCustomMath(const Dali::Ui::UiColor& color)
{
  Dali::Vector4 rgba = color;
  return rgba;
}
```

## Copying and Reusing Colors

`Dali::Ui::UiColor` is a lightweight value type. You can copy it, assign it, pass it by value when convenient, and reuse derived colors across multiple `Dali::Ui::View` objects.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ApplySharedPalette(Dali::Ui::View titleView, Dali::Ui::View subtitleView)
{
  Dali::Ui::UiColor titleColor = Dali::Ui::UiColor::PRIMARY;
  Dali::Ui::UiColor subtitleColor;

  subtitleColor = titleColor.ScaleAlpha(0.7f);

  titleView.SetColor(titleColor);
  subtitleView.SetColor(subtitleColor);
}
```

A default-constructed `Dali::Ui::UiColor` has no color ID and resolves as transparent black.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::UiColor MakeInitialColor()
{
  Dali::Ui::UiColor color;

  bool hasColorId = color.HasColorId();
  Dali::Vector4 rgba = color.GetRgba();

  (void)hasColorId;
  (void)rgba;

  return color;
}
```
