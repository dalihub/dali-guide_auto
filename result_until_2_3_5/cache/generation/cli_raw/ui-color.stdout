---
title: Ui Color
sidebar_label: Ui Color
category: styling-theme-config
---

# Ui Color

`Dali::Ui::UiColor` represents colors for dali-ui styling, either as direct RGBA values or as theme-linked color tokens.

## Table of Contents

- [Choosing Theme Colors for Views](#choosing-theme-colors-for-views)
- [Creating Direct RGBA Colors](#creating-direct-rgba-colors)
- [Adjusting Alpha](#adjusting-alpha)
- [Reading and Reusing Colors](#reading-and-reusing-colors)
- [Recommended Usage](#recommended-usage)

## Choosing Theme Colors for Views

Use predefined `Dali::Ui::UiColor` values when a view should follow the active dali-ui theme. `Dali::Ui::UiColor::PRIMARY`, `Dali::Ui::UiColor::BACKGROUND`, and `Dali::Ui::UiColor::OUTLINE` are color tokens, so dali-ui can resolve them from the current theme instead of treating them as fixed RGBA values.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

View CreateThemedPanel()
{
  return View::New()
    .SetBackgroundColor(UiColor::BACKGROUND)
    .SetBorderlineWidth(2.0f)
    .SetBorderlineColor(UiColor::OUTLINE);
}

View CreatePrimaryActionSurface()
{
  return View::New()
    .SetBackgroundColor(UiColor::PRIMARY)
    .SetRequestedWidth(160_spx)
    .SetRequestedHeight(56_spx);
}
```

`Dali::Ui::UiColor::HasColorId()` tells you whether a color is token-based. `Dali::Ui::UiColor::GetColorId()` returns the token name for token-based colors and an empty string for direct RGBA colors.

```cpp
UiColor color = UiColor::PRIMARY;

if(color.HasColorId())
{
  String tokenName = color.GetColorId();
}
```

## Creating Direct RGBA Colors

Use `Dali::Ui::UiColor::UiColor(float r, float g, float b, float a)` when a color should be fixed and independent of theme changes. The alpha argument defaults to `1.0f`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

View CreateFixedColorBadge()
{
  UiColor badgeColor(0.2f, 0.48f, 1.0f, 1.0f);

  return View::New()
    .SetBackgroundColor(badgeColor)
    .SetRequestedWidth(96_spx)
    .SetRequestedHeight(32_spx);
}

View CreateOpaqueNeutralSurface()
{
  UiColor neutral(0.92f, 0.92f, 0.92f);

  return View::New()
    .SetBackgroundColor(neutral);
}
```

A default-constructed `Dali::Ui::UiColor` has no color ID and resolves to transparent black.

```cpp
UiColor emptyColor;

bool hasToken = emptyColor.HasColorId(); // false
Vector4 rgba = emptyColor.GetRgba();     // transparent black
```

## Adjusting Alpha

Use `Dali::Ui::UiColor::WithAlpha()` when you want a specific alpha value. This is useful for overlays, outlines, disabled states, and layered surfaces.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

View CreateScrim()
{
  UiColor scrimColor = UiColor::BACKGROUND.WithAlpha(0.72f);

  return View::New()
    .SetBackgroundColor(scrimColor);
}

View CreateSubtleOutline()
{
  return View::New()
    .SetBackgroundColor(UiColor::BACKGROUND)
    .SetBorderlineWidth(1.0f)
    .SetBorderlineColor(UiColor::OUTLINE.WithAlpha(0.35f));
}
```

Use `Dali::Ui::UiColor::ScaleAlpha()` when you want to preserve the original alpha relationship and multiply it by a factor.

```cpp
UiColor base = UiColor::PRIMARY.WithAlpha(0.8f);
UiColor pressed = base.ScaleAlpha(0.6f);

View button = View::New()
  .SetBackgroundColor(base);

button.SetBackgroundColor(pressed);
```

Both `Dali::Ui::UiColor::WithAlpha()` and `Dali::Ui::UiColor::ScaleAlpha()` return a new `Dali::Ui::UiColor`, so they work naturally with fluent view setup.

## Reading and Reusing Colors

Use `Dali::Ui::UiColor::GetRgba()` when you need the resolved RGBA value as a `Vector4`. For token colors, resolution uses the current theme. For direct RGBA colors, the stored value is returned.

```cpp
UiColor themePrimary = UiColor::PRIMARY;
Vector4 resolvedPrimary = themePrimary.GetRgba();

UiColor fixed(1.0f, 0.0f, 0.0f, 0.5f);
Vector4 fixedRgba = fixed.GetRgba();
```

`Dali::Ui::UiColor` also provides `Dali::Ui::UiColor::operator Vector4()`, which is equivalent to resolving the color with `Dali::Ui::UiColor::GetRgba()`.

```cpp
UiColor color = UiColor::OUTLINE.WithAlpha(0.5f);
Vector4 rgba = color;
```

You can copy and assign `Dali::Ui::UiColor` values directly.

```cpp
UiColor normal = UiColor::PRIMARY;
UiColor highlighted;

highlighted = normal.WithAlpha(0.85f);

View view = View::New()
  .SetBackgroundColor(highlighted);
```

## Recommended Usage

Prefer theme-linked colors such as `Dali::Ui::UiColor::PRIMARY`, `Dali::Ui::UiColor::BACKGROUND`, and `Dali::Ui::UiColor::OUTLINE` for app surfaces that should adapt with the active dali-ui theme.

Use direct RGBA construction only for colors that are intentionally fixed, such as diagnostic states, generated palettes, or artwork-specific colors.

```cpp
View CreateCard()
{
  const UiColor cardBackground = UiColor::BACKGROUND;
  const UiColor cardOutline = UiColor::OUTLINE.WithAlpha(0.24f);

  return View::New()
    .SetBackgroundColor(cardBackground)
    .SetBorderlineWidth(1.0f)
    .SetBorderlineColor(cardOutline)
    .SetRequestedWidth(240_spx)
    .SetRequestedHeight(160_spx);
}
```

Keep `Dali::Ui::UiColor` at the app-facing styling layer and apply it through `Dali::Ui::View` APIs. This keeps color usage aligned with dali-ui theme behavior and avoids raw actor property code.
