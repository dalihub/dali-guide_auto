---
title: Text
sidebar_label: Text
category: uncategorized
---

# Text

The Text feature provides text styling capabilities for dali-ui applications, including decorative effects like shadows, outlines, underlines, and bevels, as well as input filtering and font variation controls.

## Table of Contents

- [Text Style Effects](#text-style-effects)
  - [Underline Style](#underline-style)
  - [Shadow Style](#shadow-style)
  - [Outline Style](#outline-style)
  - [Line-Through Style](#line-through-style)
  - [Bevel Style](#bevel-style)
- [Input Filtering](#input-filtering)
- [Font Variations](#font-variations)
- [Text Fit Range](#text-fit-range)

---

## Text Style Effects

Text style effects enhance text appearance on `Label` and `InputField` components. All style classes support fluent chaining for configuration.

### Underline Style

The `Dali::Ui::Text::Underline` class configures underline appearance. Three underline types are available: solid, dashed, and double.

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

// Create a double underline
Label doubleLabel = Label::New("Double Underline")
  .SetUnderline(
    Text::Underline()
      .SetColor(UiColor(0xFF0000))
      .SetType(Text::Underline::Type::DOUBLE)
      .SetThickness(1.5f));
```

To remove an underline from a label:

```cpp
label.ClearUnderline();
```

### Shadow Style

The `Dali::Ui::Text::Shadow` class adds drop shadow effects to text.

```cpp
// Create a simple shadow with offset
Label shadowLabel = Label::New("Shadow Text")
  .SetShadow(
    Text::Shadow()
      .SetOffset(Vector2(1.0f, 1.0f)));

// Create a shadow with color and blur
Label blurredShadow = Label::New("Blurred Shadow")
  .SetShadow(
    Text::Shadow()
      .SetColor(UiColor(0xFF5500))
      .SetOffset(Vector2(3.0f, 3.0f))
      .SetBlurRadius(2.0f));
```

To clear a shadow effect:

```cpp
label.ClearShadow();
```

### Outline Style

The `Dali::Ui::Text::Outline` class creates outline effects around text characters.

```cpp
// Create a basic outline
Label outlineLabel = Label::New("Outlined Text")
  .SetOutline(
    Text::Outline()
      .SetWidth(2.0f));

// Create an outline with color, offset, and blur
Label styledOutline = Label::New("Styled Outline")
  .SetOutline(
    Text::Outline()
      .SetColor(UiColor(0x0066FF))
      .SetOffset(Vector2(1.0f, 1.0f))
      .SetWidth(2.0f)
      .SetBlurRadius(1.0f));
```

To remove an outline:

```cpp
label.ClearOutline();
```

### Line-Through Style

The `Dali::Ui::Text::LineThrough` class adds strikethrough effects to text.

```cpp
// Create a basic line-through
Label struckLabel = Label::New("Struck Through")
  .SetLineThrough(Text::LineThrough());

// Create a styled line-through
Label styledStruck = Label::New("Styled Strikethrough")
  .SetLineThrough(
    Text::LineThrough()
      .SetColor(UiColor(0xFF00FF))
      .SetThickness(3.0f));
```

To clear a line-through effect:

```cpp
label.ClearLineThrough();
```

### Bevel Style

The `Dali::Ui::Text::Bevel` class creates 3D-like embossed or engraved effects using light and shadow colors.

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

The `SetDirection()` method accepts a `Vector2` that determines the light source angle. Typical values:
- `Vector2(-1.0f, -1.0f)` for top-left light source
- `Vector2(1.0f, -1.0f)` for top-right light source

To clear a bevel effect:

```cpp
label.ClearBevel();
```

---

## Input Filtering

The `Dali::Ui::Text::InputFilter` class restricts input characters in `InputField` components using regular expression patterns.

```cpp
// Create an input filter that allows only digits but denies 0-5
Text::InputFilter inputFilter;
inputFilter.SetAllowPattern("[\\d]");
inputFilter.SetDenyPattern("[0-5]");

InputField inputField = InputField::New()
  .SetPlaceholder("Enter digits 6-9")
  .SetInputFilter(inputFilter);
```

The filter uses ECMAScript regular expression grammar. When both patterns are set:
- Input must match the allow pattern
- Input must not match the deny pattern

To clear an input filter:

```cpp
inputField.ClearInputFilter();
```

Connect to the `InputRejectedSignal` to handle rejected input:

```cpp
inputField.InputRejectedSignal().Connect(this, &MyClass::OnInputRejected);

// Handler signature
void OnInputRejected(View view, Text::InputFilter::RejectReason reason)
{
  // reason is either NOT_ALLOWED or DENIED
}
```

---

## Font Variations

The `Dali::Ui::Text::FontVariationAxis` class configures variable font settings using OpenType axis tags.

```cpp
// Create a font variation axis for weight
Text::FontVariationAxis weightAxis;
weightAxis.SetTag("wght");
weightAxis.SetValue(700.0f);

// Create using the constructor
Text::FontVariationAxis widthAxis("wdth", 75.0f);
```

Use `Dali::Ui::Text::FontVariation` to convert between string and vector representations:

```cpp
// Convert from settings string
Dali::Vector<Text::FontVariationAxis> axes =
  Text::FontVariation::FromString(Dali::String("wght=700;wdth=75"));

// Convert to settings string
Dali::String settings = Text::FontVariation::ToString(axes);
```

Common OpenType axis tags include:
- `"wght"` - Weight
- `"wdth"` - Width
- `"slnt"` - Slant
- `"opsz"` - Optical size

---

## Text Fit Range

The `Dali::Ui::Text::FitRange` class defines a font size range for automatic text fitting. The text fit algorithm selects the largest font size within the range that fits the available space.

```cpp
// Create a fit range with constructor
Text::FitRange fitRange(12.0f, 48.0f, 2.0f);

// Or configure using setters
Text::FitRange fitRange;
fitRange.SetMinimumFontSize(12.0f)
         .SetMaximumFontSize(48.0f)
         .SetFontSizeStep(2.0f);
```

The `SetFontSizeStep()` method defines the decrement used when searching for the largest fitting size. Smaller steps provide more precise fitting but require more processing.