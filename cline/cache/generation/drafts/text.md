---
title: Text
sidebar_label: Text
category: text
---

# Text

The Text feature provides text styling and input filtering capabilities for dali-ui applications. It includes style objects for visual effects like shadows, outlines, and bevels, as well as input filtering and font variation configuration.

## Table of Contents

- [Text Style Objects](#text-style-objects)
  - [Underline Style](#underline-style)
  - [Shadow Style](#shadow-style)
  - [Outline Style](#outline-style)
  - [LineThrough Style](#linethrough-style)
  - [Bevel Style](#bevel-style)
- [Input Filtering](#input-filtering)
- [Text Fit Configuration](#text-fit-configuration)
- [Font Variation Axes](#font-variation-axes)

## Text Style Objects

Text style objects are value-type classes that configure the visual appearance of text in `Label` and `InputField` controls. All style objects support fluent chaining for convenient configuration.

### Underline Style

The `Dali::Ui::Text::Underline` class configures underline appearance for text. It supports solid, dashed, and double underline types.

```cpp
// Create a solid underline with default settings
Label label = Label::New("Underlined Text")
  .SetUnderline(Text::Underline());

// Create a dashed underline with custom styling
Label dashedLabel = Label::New("Dashed Underline")
  .SetUnderline(
    Text::Underline()
      .SetColor(UiColor(0x0088FF))
      .SetThickness(2.0f)
      .SetType(Text::Underline::Type::DASHED)
      .SetDashLength(4.0f)
      .SetDashGap(4.0f));
```

The `Underline::Type` enum defines the rendering style:

- `Type::SOLID` - Draws a solid underline
- `Type::DASHED` - Draws a dashed underline using dash length and gap settings
- `Type::DOUBLE` - Draws a double underline

### Shadow Style

The `Dali::Ui::Text::Shadow` class configures drop shadow effects for text.

```cpp
// Create a shadow with offset only
Label shadowLabel = Label::New("Shadow Text")
  .SetShadow(
    Text::Shadow()
      .SetOffset(Vector2(1.0f, 1.0f)));

// Create a shadow with full configuration
Label styledShadow = Label::New("Styled Shadow")
  .SetShadow(
    Text::Shadow()
      .SetColor(UiColor(0xFF5500))
      .SetOffset(Vector2(3.0f, 3.0f))
      .SetBlurRadius(2.0f));
```

Use `SetOffset()` to position the shadow relative to the text. Positive values offset right and down. Use `SetBlurRadius()` to create a softer shadow edge.

### Outline Style

The `Dali::Ui::Text::Outline` class configures outline effects around text characters.

```cpp
// Create a simple outline
Label outlineLabel = Label::New("Outlined Text")
  .SetOutline(
    Text::Outline()
      .SetWidth(2.0f));

// Create an outline with all properties
Label styledOutline = Label::New("Styled Outline")
  .SetOutline(
    Text::Outline()
      .SetColor(UiColor(0x0066FF))
      .SetOffset(Vector2(1.0f, 1.0f))
      .SetWidth(2.0f)
      .SetBlurRadius(1.0f));
```

The `SetWidth()` method controls the outline thickness in pixels. Use `SetOffset()` to shift the outline position relative to the text.

### LineThrough Style

The `Dali::Ui::Text::LineThrough` class configures strikethrough effects for text.

```cpp
// Create a default line-through
Label strikeLabel = Label::New("Struck Through")
  .SetLineThrough(Text::LineThrough());

// Create a styled line-through
Label styledStrike = Label::New("Styled Strikethrough")
  .SetLineThrough(
    Text::LineThrough()
      .SetColor(UiColor(0xFF00FF))
      .SetThickness(3.0f));
```

### Bevel Style

The `Dali::Ui::Text::Bevel` class creates 3D-like embossed or engraved effects on text using light and shadow colors.

```cpp
// Create an embossed effect (raised text)
Label embossed = Label::New("Embossed")
  .SetTextColor(UiColor(0x333333))
  .SetBevel(
    Text::Bevel()
      .SetDirection(Vector2(-1.0f, -1.0f))
      .SetIntensity(2.0f)
      .SetLightColor(UiColor(0x808080))
      .SetShadowColor(UiColor(0x0D0D0D)));

// Create an engraved effect (sunken text)
Label engraved = Label::New("Engraved")
  .SetTextColor(UiColor(0x212121))
  .SetBevel(
    Text::Bevel()
      .SetDirection(Vector2(-1.0f, -1.0f))
      .SetIntensity(2.0f)
      .SetLightColor(UiColor(0x0D0D0D))
      .SetShadowColor(UiColor(0x808080)));
```

The `SetDirection()` method sets the light source direction. A direction of `(-1.0f, -1.0f)` simulates light from the top-left. Swap light and shadow colors to switch between embossed and engraved effects.

## Input Filtering

The `Dali::Ui::Text::InputFilter` class restricts the characters that can be entered in an `InputField`. It uses regular expression patterns to define allowed and denied input.

```cpp
// Create an input filter that allows only digits but denies 0-5
Text::InputFilter inputFilter;
inputFilter.SetAllowPattern("[\\d]");
inputFilter.SetDenyPattern("[0-5]");

InputField inputField = InputField::New()
  .SetText("Enter digits 6-9 only")
  .SetInputFilter(inputFilter);
```

The filter evaluates input as follows:

1. If an allow pattern is set, input must match the pattern
2. If a deny pattern is set, input must not match the pattern
3. If both are set, both conditions must be satisfied

Patterns follow `std::regex` ECMAScript grammar. Connect to `InputRejectedSignal` to handle rejected input:

```cpp
inputField.InputRejectedSignal().Connect(this, &MyClass::OnInputRejected);

void OnInputRejected(View view, Text::InputFilter::RejectReason reason)
{
  // reason is NOT_ALLOWED or DENIED
}
```

Clear the input filter to remove restrictions:

```cpp
inputField.ClearInputFilter();
```

## Text Fit Configuration

The `Dali::Ui::Text::FitRange` class configures automatic font size adjustment to fit text within available space.

```cpp
// Create a fit range for automatic font sizing
Text::FitRange fitRange = Text::FitRange()
  .SetMinimumFontSize(12.0f)
  .SetMaximumFontSize(48.0f)
  .SetFontSizeStep(2.0f);
```

The text fit algorithm searches from the maximum font size downward by the specified step until finding a size that fits the layout space.

The `Dali::Ui::Text::FitCandidate` class represents a candidate font size during the fit process:

```cpp
Text::FitCandidate candidate = Text::FitCandidate()
  .SetFontSize(24.0f)
  .SetLineHeight(28.0f);
```

## Font Variation Axes

The `Dali::Ui::Text::FontVariationAxis` class configures OpenType variable font axes for fine-grained font control.

```cpp
// Create a font variation axis for weight
Text::FontVariationAxis weightAxis = Text::FontVariationAxis()
  .SetTag("wght")
  .SetValue(450.0f);

// Create a font variation axis for width
Text::FontVariationAxis widthAxis = Text::FontVariationAxis()
  .SetTag("wdth")
  .SetValue(75.0f);
```

Common OpenType axis tags include:

- `"wght"` - Font weight
- `"wdth"` - Font width
- `"slnt"` - Slant
- `"opsz"` - Optical size

Use `Dali::Ui::Text::FontVariation::ToString()` to serialize a vector of axes, and `FromString()` to parse a settings string:

```cpp
Dali::Vector<Text::FontVariationAxis> axes;
axes.PushBack(Text::FontVariationAxis("wght", 450.0f));

Dali::String settings = Text::FontVariation::ToString(axes);
// Parse settings back to axes
Dali::Vector<Text::FontVariationAxis> parsedAxes = Text::FontVariation::FromString(settings);