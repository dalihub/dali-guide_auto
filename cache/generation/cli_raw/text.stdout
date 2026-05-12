---
title: Text
sidebar_label: Text
category: text
---

# Text

`Dali::Ui::Text` provides typed value objects and property indexes for configuring text presentation, fitting, font variation, and input filtering in dali-ui applications.

## Table of Contents

- [Text in a dali-ui View Tree](#text-in-a-dali-ui-view-tree)
- [Bevel Styling](#bevel-styling)
- [Text Fitting Values](#text-fitting-values)
- [Variable Font Settings](#variable-font-settings)
- [Input Filtering](#input-filtering)
- [Property Index Reference Points](#property-index-reference-points)

## Text in a dali-ui View Tree

In a dali-ui application, text controls are part of the `Dali::Ui::View` tree. Application code should treat text-capable UI objects as views and configure text behavior through dali-ui text value objects such as `Dali::Ui::Text::Bevel`, `Dali::Ui::Text::FitRange`, `Dali::Ui::Text::FitCandidate`, `Dali::Ui::Text::FontVariationAxis`, and `Dali::Ui::Text::InputFilter`.

The text namespace also owns style helper types such as `Dali::Ui::Text::Outline`, `Dali::Ui::Text::Shadow`, `Dali::Ui::Text::Underline`, and `Dali::Ui::Text::LineThrough`. These are text-specific configuration values that can be used by text-capable dali-ui views where the corresponding view API is available.

```cpp
Dali::Ui::Text::Bevel bevel;
bevel
  .SetDirection(Dali::Vector2(-1.0f, -1.0f))
  .SetIntensity(2.0f)
  .SetLightColor(Dali::Ui::UiColor(0x808080))
  .SetShadowColor(Dali::Ui::UiColor(0x0D0D0D));

Dali::Ui::Text::FitRange fitRange;
fitRange
  .SetMinimumFontSize(14.0f)
  .SetMaximumFontSize(24.0f)
  .SetFontSizeStep(1.0f);
```

## Bevel Styling

`Dali::Ui::Text::Bevel` describes a 3D-like text effect using a light direction, intensity, light color, and shadow color. The type is a value object, so it is convenient to build with fluent setters and then pass to the text-capable dali-ui view API that consumes bevel styling.

`SetDirection()` takes a `Dali::Vector2`. Common sample usage uses `Dali::Vector2(-1.0f, -1.0f)` for a top-left light direction. `SetIntensity()` controls the strength of the effect, while `SetLightColor()` and `SetShadowColor()` define the highlighted and shadowed sides.

```cpp
Dali::Ui::Text::Bevel embossed;
embossed
  .SetDirection(Dali::Vector2(-1.0f, -1.0f))
  .SetIntensity(2.0f)
  .SetLightColor(Dali::Ui::UiColor(0x808080))
  .SetShadowColor(Dali::Ui::UiColor(0x0D0D0D));

const Dali::Vector2& direction = embossed.GetDirection();
float intensity = embossed.GetIntensity();
const Dali::Ui::UiColor& lightColor = embossed.GetLightColor();
const Dali::Ui::UiColor& shadowColor = embossed.GetShadowColor();
```

For an engraved look, keep the same `SetDirection()` and swap the visual emphasis between `SetLightColor()` and `SetShadowColor()`:

```cpp
Dali::Ui::Text::Bevel engraved;
engraved
  .SetDirection(Dali::Vector2(-1.0f, -1.0f))
  .SetIntensity(2.0f)
  .SetLightColor(Dali::Ui::UiColor(0x0D0D0D))
  .SetShadowColor(Dali::Ui::UiColor(0x808080));
```

## Text Fitting Values

`Dali::Ui::Text::FitRange` describes range-based fitting. It stores a minimum font size, maximum font size, and step. The fitting behavior searches the configured range using the step to find a size that fits the available layout space.

```cpp
Dali::Ui::Text::FitRange bodyFit;
bodyFit
  .SetMinimumFontSize(12.0f)
  .SetMaximumFontSize(22.0f)
  .SetFontSizeStep(1.0f);

float minimum = bodyFit.GetMinimumFontSize();
float maximum = bodyFit.GetMaximumFontSize();
float step = bodyFit.GetFontSizeStep();
```

`Dali::Ui::Text::FitCandidate` describes candidate-based fitting. Each candidate stores a concrete font size and absolute line height. This is useful when your design system has a small number of approved text sizes rather than a continuous range.

```cpp
Dali::Ui::Text::FitCandidate compact;
compact
  .SetFontSize(14.0f)
  .SetLineHeight(18.0f);

Dali::Ui::Text::FitCandidate comfortable;
comfortable
  .SetFontSize(18.0f)
  .SetLineHeight(24.0f);

float compactSize = compact.GetFontSize();
float compactLineHeight = compact.GetLineHeight();
```

## Variable Font Settings

`Dali::Ui::Text::FontVariationAxis` represents one OpenType variable font axis. The axis has a four-character tag such as `"wght"` or `"wdth"` and a floating-point value.

```cpp
Dali::Ui::Text::FontVariationAxis weight("wght", 700.0f);

weight
  .SetTag("wght")
  .SetValue(600.0f);

const Dali::String& tag = weight.GetTag();
float value = weight.GetValue();
```

`Dali::Ui::Text::FontVariation` converts between a string form and a vector of `Dali::Ui::Text::FontVariationAxis` values. `FromString()` accepts supported settings strings such as `wght=700,wdth=90`. `ToString()` returns the canonical comma-separated form.

```cpp
Dali::Vector<Dali::Ui::Text::FontVariationAxis> axes =
  Dali::Ui::Text::FontVariation::FromString("wght=700,wdth=90");

Dali::String settings =
  Dali::Ui::Text::FontVariation::ToString(axes);
```

If parsing fails, `FromString()` returns an empty axis list. Duplicate axis tags are resolved by keeping the last value.

## Input Filtering

`Dali::Ui::Text::InputFilter` describes filtering rules for editable text. `SetAllowPattern()` defines input that may be inserted. The pattern uses `std::regex` ECMAScript grammar.

```cpp
Dali::Ui::Text::InputFilter digitsOnly;
digitsOnly.SetAllowPattern("[\\d]");
```

When input is rejected, the rejection reason is represented by `Dali::Ui::Text::InputFilter::RejectReason`. `Dali::Ui::Text::InputFilter::RejectReason::NOT_ALLOWED` means the text did not match the allow pattern. `Dali::Ui::Text::InputFilter::RejectReason::DENIED` means it matched a deny rule.

```cpp
void HandleRejectedInput(Dali::Ui::View view,
                         Dali::Ui::Text::InputFilter::RejectReason reason)
{
  if(reason == Dali::Ui::Text::InputFilter::RejectReason::NOT_ALLOWED)
  {
    return;
  }

  if(reason == Dali::Ui::Text::InputFilter::RejectReason::DENIED)
  {
    return;
  }
}
```

## Property Index Reference Points

`Dali::Ui::Text::LabelPropertyIndex` and `Dali::Ui::Text::InputFieldPropertyIndex` define the underlying text property indexes for dali-ui text controls. Prefer typed view APIs where available, and use these indexes as reference points when integrating with lower-level property handling or tooling.

For labels, `Dali::Ui::Text::LabelPropertyIndex` includes text display and layout-related indexes such as `FONT_SIZE`, `FONT_FAMILY`, `HORIZONTAL_ALIGNMENT`, `LAYOUT_DIRECTION_MODE`, and `MARKUP_ENABLED`.

```cpp
int labelFontSizeIndex =
  Dali::Ui::Text::LabelPropertyIndex::FONT_SIZE;

int labelFontFamilyIndex =
  Dali::Ui::Text::LabelPropertyIndex::FONT_FAMILY;

int labelMarkupIndex =
  Dali::Ui::Text::LabelPropertyIndex::MARKUP_ENABLED;
```

For editable input fields, `Dali::Ui::Text::InputFieldPropertyIndex` includes cursor, editability, and font indexes such as `CURSOR_WIDTH`, `CURSOR_COLOR`, `CURSOR_BLINK_ENABLED`, `CURSOR_BLINK_INTERVAL`, `CURSOR_POSITION`, `EDITABLE`, `FONT_SIZE`, `FONT_SIZE_SCALE`, and `MAXIMUM_FONT_SIZE_SCALE`.

```cpp
int cursorColorIndex =
  Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_COLOR;

int cursorPositionIndex =
  Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_POSITION;

int editableIndex =
  Dali::Ui::Text::InputFieldPropertyIndex::EDITABLE;

int maximumScaleIndex =
  Dali::Ui::Text::InputFieldPropertyIndex::MAXIMUM_FONT_SIZE_SCALE;
```
