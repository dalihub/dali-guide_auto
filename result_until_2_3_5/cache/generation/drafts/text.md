---
title: Text
sidebar_label: Text
category: text
---

# Text

Use `Dali::Ui::Text` value objects to configure text styling, fitting, font variation, and input behavior in a dali-ui view tree.

## Table of Contents

- [Bevel Styling](#bevel-styling)
- [Text Fit Ranges](#text-fit-ranges)
- [Text Fit Candidates](#text-fit-candidates)
- [Variable Font Axes](#variable-font-axes)
- [Input Filtering](#input-filtering)
- [Input Field Property Indices](#input-field-property-indices)
- [Other Text Style Value Objects](#other-text-style-value-objects)

## Bevel Styling

`Dali::Ui::Text::Bevel` is a value object for text bevel appearance. It stores the bevel direction, intensity, light color, and shadow color. In a dali-ui application, configure the value object with typed setters and pass it to the text view or text-capable control that owns the visible text.

```cpp
Dali::Ui::Text::Bevel raisedBevel;

raisedBevel
  .SetDirection(Dali::Vector2(-1.0f, -1.0f))
  .SetIntensity(2.0f)
  .SetLightColor(Dali::Ui::UiColor(0x808080))
  .SetShadowColor(Dali::Ui::UiColor(0x0D0D0D));
```

Use `Dali::Ui::Text::Bevel::GetDirection`, `Dali::Ui::Text::Bevel::GetIntensity`, `Dali::Ui::Text::Bevel::GetLightColor`, and `Dali::Ui::Text::Bevel::GetShadowColor` when you need to inspect an existing bevel configuration before applying or reusing it.

```cpp
const Dali::Vector2& direction = raisedBevel.GetDirection();
float intensity = raisedBevel.GetIntensity();
const Dali::Ui::UiColor& lightColor = raisedBevel.GetLightColor();
const Dali::Ui::UiColor& shadowColor = raisedBevel.GetShadowColor();
```

## Text Fit Ranges

`Dali::Ui::Text::FitRange` describes range-based text fitting. It contains a minimum font size, maximum font size, and step size. The text fit logic searches the configured range and selects the largest fitting size for the available layout space.

```cpp
Dali::Ui::Text::FitRange titleFit;

titleFit
  .SetMinimumFontSize(16.0f)
  .SetMaximumFontSize(32.0f)
  .SetFontSizeStep(4.0f);
```

The same values can be read back with `Dali::Ui::Text::FitRange::GetMinimumFontSize`, `Dali::Ui::Text::FitRange::GetMaximumFontSize`, and `Dali::Ui::Text::FitRange::GetFontSizeStep`.

```cpp
float minimumSize = titleFit.GetMinimumFontSize();
float maximumSize = titleFit.GetMaximumFontSize();
float step = titleFit.GetFontSizeStep();
```

Use `Dali::Ui::Text::FitRange` when you want the text system to choose a size from a continuous range while preserving the line-height behavior already configured on the text view.

## Text Fit Candidates

`Dali::Ui::Text::FitCandidate` describes candidate-based fitting. Each candidate contains a font size and an absolute line height. Candidate-based fitting is useful when your design system has a fixed set of approved type sizes.

```cpp
Dali::Ui::Text::FitCandidate compact;

compact
  .SetFontSize(16.0f)
  .SetLineHeight(32.0f);

Dali::Ui::Text::FitCandidate regular;

regular
  .SetFontSize(20.0f)
  .SetLineHeight(40.0f);
```

Use `Dali::Ui::Text::FitCandidate::GetFontSize` and `Dali::Ui::Text::FitCandidate::GetLineHeight` to inspect a candidate.

```cpp
float fontSize = regular.GetFontSize();
float lineHeight = regular.GetLineHeight();
```

When a set of candidates is supplied to a text view, the fitting behavior can choose the largest candidate that fits the available layout space.

## Variable Font Axes

`Dali::Ui::Text::FontVariationAxis` represents one variable font axis. It stores a four-character OpenType axis tag, such as `"wght"` or `"wdth"`, and a numeric value.

```cpp
Dali::Ui::Text::FontVariationAxis weightAxis("wght", 700.0f);
Dali::Ui::Text::FontVariationAxis widthAxis;

widthAxis
  .SetTag("wdth")
  .SetValue(100.0f);
```

Use `Dali::Ui::Text::FontVariationAxis::GetTag` and `Dali::Ui::Text::FontVariationAxis::GetValue` when you need to inspect or serialize axis values.

```cpp
const Dali::String& tag = weightAxis.GetTag();
float value = weightAxis.GetValue();
```

`Dali::Ui::Text::FontVariation` provides conversion helpers between the string form and the axis-vector form. `Dali::Ui::Text::FontVariation::FromString` parses a variation string, and `Dali::Ui::Text::FontVariation::ToString` writes axes back to the canonical string form.

```cpp
Dali::Vector<Dali::Ui::Text::FontVariationAxis> axes =
  Dali::Ui::Text::FontVariation::FromString("wght=700,wdth=100");

Dali::String settings =
  Dali::Ui::Text::FontVariation::ToString(axes);
```

## Input Filtering

`Dali::Ui::Text::InputFilter` is a value object for text-entry filtering. An allow pattern limits inserted input to text that matches the configured regular expression.

```cpp
Dali::Ui::Text::InputFilter digitsOnly;

digitsOnly.SetAllowPattern("[0-9]+");
```

Use `Dali::Ui::Text::InputFilter::GetAllowPattern` to read the configured allow pattern.

```cpp
const Dali::String& allowPattern = digitsOnly.GetAllowPattern();
```

The pattern grammar follows ECMAScript regular-expression syntax, so keep application filters explicit and small. For example, a numeric field can use an allow pattern for digits, while a product code field can use a pattern that matches the exact character classes your app accepts.

## Input Field Property Indices

`Dali::Ui::Text::InputFieldPropertyIndex` names property indices for text input fields. These constants are mainly useful when code needs property-index lookup instead of typed setters, for example in shared tooling or generic property code.

```cpp
auto fontFamilyProperty =
  Dali::Ui::Text::InputFieldPropertyIndex::FONT_FAMILY;

auto fontSizeProperty =
  Dali::Ui::Text::InputFieldPropertyIndex::FONT_SIZE;

auto cursorColorProperty =
  Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_COLOR;
```

Cursor-related indices include `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_WIDTH`, `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_COLOR`, `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_BLINK_ENABLED`, `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_BLINK_INTERVAL`, `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_POSITION`, `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_HANDLE_IMAGE`, and `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_HANDLE_PRESSED_IMAGE`.

```cpp
auto cursorWidthProperty =
  Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_WIDTH;

auto cursorBlinkIntervalProperty =
  Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_BLINK_INTERVAL;

auto cursorPositionProperty =
  Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_POSITION;
```

Text-style and behavior indices include `Dali::Ui::Text::InputFieldPropertyIndex::EDITABLE`, `Dali::Ui::Text::InputFieldPropertyIndex::MARKUP_ENABLED`, `Dali::Ui::Text::InputFieldPropertyIndex::HORIZONTAL_ALIGNMENT`, `Dali::Ui::Text::InputFieldPropertyIndex::LAYOUT_DIRECTION_MODE`, `Dali::Ui::Text::InputFieldPropertyIndex::FONT_WEIGHT`, `Dali::Ui::Text::InputFieldPropertyIndex::FONT_WIDTH`, `Dali::Ui::Text::InputFieldPropertyIndex::FONT_SLANT`, `Dali::Ui::Text::InputFieldPropertyIndex::FONT_SIZE_SCALE`, and `Dali::Ui::Text::InputFieldPropertyIndex::MAXIMUM_FONT_SIZE_SCALE`.

```cpp
auto editableProperty =
  Dali::Ui::Text::InputFieldPropertyIndex::EDITABLE;

auto markupEnabledProperty =
  Dali::Ui::Text::InputFieldPropertyIndex::MARKUP_ENABLED;

auto maximumScaleProperty =
  Dali::Ui::Text::InputFieldPropertyIndex::MAXIMUM_FONT_SIZE_SCALE;
```

## Other Text Style Value Objects

`Dali::Ui::Text::Outline`, `Dali::Ui::Text::Shadow`, `Dali::Ui::Text::Underline`, and `Dali::Ui::Text::LineThrough` are text style value objects. They are part of the same dali-ui text configuration model as `Dali::Ui::Text::Bevel`: create a style value, configure it with the relevant public API, and apply it through the text view or control that renders the text.

```cpp
Dali::Ui::Text::Outline outline;
Dali::Ui::Text::Shadow shadow;
Dali::Ui::Text::Underline underline;
Dali::Ui::Text::LineThrough lineThrough;
```

Keep these values close to the `Dali::Ui::View` construction code that uses them. That makes the view tree easier to review and keeps text styling in the application layer rather than spreading text configuration through lower-level rendering code.
