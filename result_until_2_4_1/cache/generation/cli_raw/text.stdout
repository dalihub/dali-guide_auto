---
title: Text
sidebar_label: Text
category: text
---

# Text

Use `Dali::Ui::Text` value objects to configure text styling, fitting, font variation, and input behavior in a dali-ui `Dali::Ui::View` based interface.

## Table of Contents

- [Text Styling Values](#text-styling-values)
- [Text Fit Configuration](#text-fit-configuration)
- [Variable Font Settings](#variable-font-settings)
- [Input Field Property Indices](#input-field-property-indices)
- [Input Filtering](#input-filtering)

## Text Styling Values

`Dali::Ui::Text::Bevel` is an owned text style value. It describes a light-and-shadow bevel effect that can be prepared independently and then used by a dali-ui text view that supports bevel styling.

Configure `Dali::Ui::Text::Bevel` with typed setters. The setters return `Dali::Ui::Text::Bevel&`, so fluent chaining is the preferred form.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::Text::Bevel CreateRaisedTextBevel()
{
  return Dali::Ui::Text::Bevel()
    .SetDirection(Dali::Vector2(-1.0f, -1.0f))
    .SetIntensity(2.0f)
    .SetLightColor(Dali::Ui::UiColor(0x808080))
    .SetShadowColor(Dali::Ui::UiColor(0x0D0D0D));
}
```

Use the direction vector to choose the apparent light direction. For example, `Dali::Vector2(-1.0f, -1.0f)` represents light from the top-left in the public sample style setup. Swap the light and shadow colors to create a sunken-looking variant.

```cpp
Dali::Ui::Text::Bevel CreateSunkenTextBevel()
{
  return Dali::Ui::Text::Bevel()
    .SetDirection(Dali::Vector2(-1.0f, -1.0f))
    .SetIntensity(2.0f)
    .SetLightColor(Dali::Ui::UiColor(0x0D0D0D))
    .SetShadowColor(Dali::Ui::UiColor(0x808080));
}
```

`Dali::Ui::Text::Bevel` also exposes getters for reading back the configured value. This is useful when a `Dali::Ui::View`-based screen keeps style presets in application state.

```cpp
float ReadBevelIntensity(const Dali::Ui::Text::Bevel& bevel)
{
  return bevel.GetIntensity();
}

const Dali::Vector2& ReadBevelDirection(const Dali::Ui::Text::Bevel& bevel)
{
  return bevel.GetDirection();
}

const Dali::Ui::UiColor& ReadBevelLightColor(const Dali::Ui::Text::Bevel& bevel)
{
  return bevel.GetLightColor();
}

const Dali::Ui::UiColor& ReadBevelShadowColor(const Dali::Ui::Text::Bevel& bevel)
{
  return bevel.GetShadowColor();
}
```

## Text Fit Configuration

`Dali::Ui::Text::FitRange` and `Dali::Ui::Text::FitCandidate` are owned text fit configuration values.

Use `Dali::Ui::Text::FitRange` when the text system should search across a continuous font-size range. The range stores a minimum font size, a maximum font size, and a font-size step, all in pixels.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::Text::FitRange CreateTitleFitRange()
{
  return Dali::Ui::Text::FitRange()
    .SetMinimumFontSize(18.0f)
    .SetMaximumFontSize(36.0f)
    .SetFontSizeStep(1.0f);
}
```

Use the getters when application logic needs to inspect or copy a preset before applying it to a text view.

```cpp
bool HasUsableFitRange(const Dali::Ui::Text::FitRange& range)
{
  return range.GetMinimumFontSize() <= range.GetMaximumFontSize() &&
         range.GetFontSizeStep() > 0.0f;
}
```

Use `Dali::Ui::Text::FitCandidate` when your design has explicit approved text sizes. Each candidate stores a font size and an absolute line height.

```cpp
Dali::Ui::Text::FitCandidate CreateCompactCandidate()
{
  return Dali::Ui::Text::FitCandidate()
    .SetFontSize(16.0f)
    .SetLineHeight(20.0f);
}

Dali::Ui::Text::FitCandidate CreateComfortableCandidate()
{
  return Dali::Ui::Text::FitCandidate()
    .SetFontSize(20.0f)
    .SetLineHeight(26.0f);
}
```

`Dali::Ui::Text::FitCandidate::GetFontSize` and `Dali::Ui::Text::FitCandidate::GetLineHeight` return the configured pixel values.

```cpp
float CalculateCandidateLineExtra(const Dali::Ui::Text::FitCandidate& candidate)
{
  return candidate.GetLineHeight() - candidate.GetFontSize();
}
```

## Variable Font Settings

`Dali::Ui::Text::FontVariationAxis` represents one OpenType variable font axis. It owns a four-character axis tag such as `"wght"` or `"wdth"` and a numeric value.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::Text::FontVariationAxis CreateWeightAxis()
{
  return Dali::Ui::Text::FontVariationAxis("wght", 700.0f);
}

Dali::Ui::Text::FontVariationAxis CreateWidthAxis()
{
  return Dali::Ui::Text::FontVariationAxis()
    .SetTag("wdth")
    .SetValue(90.0f);
}
```

Read back the axis with `Dali::Ui::Text::FontVariationAxis::GetTag` and `Dali::Ui::Text::FontVariationAxis::GetValue`.

```cpp
bool IsWeightAxis(const Dali::Ui::Text::FontVariationAxis& axis)
{
  return axis.GetTag() == "wght";
}

float ReadAxisValue(const Dali::Ui::Text::FontVariationAxis& axis)
{
  return axis.GetValue();
}
```

`Dali::Ui::Text::FontVariation` provides conversion helpers. Use `Dali::Ui::Text::FontVariation::FromString` to parse a settings string into axes, and `Dali::Ui::Text::FontVariation::ToString` to serialize axes back to the canonical string format.

```cpp
Dali::Vector<Dali::Ui::Text::FontVariationAxis> ParseFontVariation()
{
  return Dali::Ui::Text::FontVariation::FromString("wght=700,wdth=90");
}

Dali::String BuildFontVariationString()
{
  Dali::Vector<Dali::Ui::Text::FontVariationAxis> axes;
  axes.PushBack(Dali::Ui::Text::FontVariationAxis("wght", 700.0f));
  axes.PushBack(Dali::Ui::Text::FontVariationAxis("wdth", 90.0f));

  return Dali::Ui::Text::FontVariation::ToString(axes);
}
```

## Input Field Property Indices

`Dali::Ui::Text::InputFieldPropertyIndex` owns the property index constants that describe dali-ui input field text properties. In application code, prefer typed dali-ui input field APIs when they are available. Use these property indices when you need stable property identifiers for integration with a property-oriented layer.

The following constants identify common font and layout properties:

- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_FAMILY`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_SIZE`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_SIZE_SCALE`
- `Dali::Ui::Text::InputFieldPropertyIndex::MAXIMUM_FONT_SIZE_SCALE`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_WEIGHT`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_WIDTH`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_SLANT`
- `Dali::Ui::Text::InputFieldPropertyIndex::HORIZONTAL_ALIGNMENT`
- `Dali::Ui::Text::InputFieldPropertyIndex::LAYOUT_DIRECTION_MODE`
- `Dali::Ui::Text::InputFieldPropertyIndex::MARKUP_ENABLED`

```cpp
int FontSizePropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::FONT_SIZE;
}

int FontFamilyPropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::FONT_FAMILY;
}

int HorizontalAlignmentPropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::HORIZONTAL_ALIGNMENT;
}
```

The cursor-related constants identify editable text cursor configuration:

- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_BLINK_ENABLED`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_BLINK_INTERVAL`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_COLOR`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_WIDTH`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_POSITION`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_HANDLE_IMAGE`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_HANDLE_PRESSED_IMAGE`
- `Dali::Ui::Text::InputFieldPropertyIndex::EDITABLE`

```cpp
int CursorColorPropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_COLOR;
}

int CursorBlinkIntervalPropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_BLINK_INTERVAL;
}

int EditablePropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::EDITABLE;
}
```

## Input Filtering

`Dali::Ui::Text::InputFilter` is an owned text input rule object. It stores regular expression patterns that describe which input may be inserted.

Use `Dali::Ui::Text::InputFilter::SetAllowPattern` for positive filtering. If an allow pattern is set, input that does not match it is rejected.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::Text::InputFilter CreateDigitsOnlyFilter()
{
  return Dali::Ui::Text::InputFilter()
    .SetAllowPattern("[0-9]+");
}
```

Keep filters as part of the state for a `Dali::Ui::View` based input screen, then pass the configured value to the dali-ui input component that owns text entry.

```cpp
class TextEntryState
{
public:
  TextEntryState()
  : mFilter(CreateDigitsOnlyFilter())
  {
  }

  const Dali::Ui::Text::InputFilter& GetFilter() const
  {
    return mFilter;
  }

private:
  Dali::Ui::Text::InputFilter mFilter;
};
```
