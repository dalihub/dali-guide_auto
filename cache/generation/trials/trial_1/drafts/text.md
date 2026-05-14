---
title: Text
sidebar_label: Text
category: text
---

# Text

The Text feature provides text styling capabilities for dali-ui applications, including visual effects like shadows, outlines, underlines, and bevel effects for `Label` and `InputField` components.

## Table of Contents

- [Text Style Effects](#text-style-effects)
  - [Underline](#underline)
  - [Shadow](#shadow)
  - [Outline](#outline)
  - [LineThrough](#linethrough)
  - [Bevel](#bevel)
- [Font Styling](#font-styling)
  - [Font Weight, Width, and Slant](#font-weight-width-and-slant)
- [Input Filtering](#input-filtering)
- [Font Variations](#font-variations)
- [Dynamic Font Sizing](#dynamic-font-sizing)

---

## Text Style Effects

Text style effects enhance text visibility and visual appeal. All style objects support fluent chaining for configuration.

### Underline

`Text::Underline` adds a line beneath text. Use it for emphasis or links.

Create a basic underline:

```cpp
Label label = Label::New("Underlined Text")
  .SetUnderline(Text::Underline());
```

Configure underline properties with fluent chaining:

```cpp
Label label = Label::New("Dashed Underline")
  .SetUnderline(
    Text::Underline()
      .SetColor(UiColor(0x0088FF))
      .SetThickness(2.0f)
      .SetType(Text::Underline::Type::DASHED)
      .SetDashLength(4.0f)
      .SetDashGap(4.0f));
```

Clear an underline from a label:

```cpp
label.ClearUnderline();
```

`InputField` also supports underline styling:

```cpp
InputField inputField = InputField::New()
  .SetText("Input with underline")
  .SetUnderline(
    Text::Underline()
      .SetColor(UiColor(0x0088FF))
      .SetThickness(2.0f));
```

### Shadow

`Text::Shadow` adds a drop shadow effect to text, creating depth and improving readability on complex backgrounds.

Create a shadow with an offset:

```cpp
Label label = Label::New("Shadow Text")
  .SetShadow(
    Text::Shadow()
      .SetOffset(Vector2(1.0f, 1.0f)));
```

Configure shadow properties:

```cpp
Label label = Label::New("Shadow with Blur")
  .SetShadow(
    Text::Shadow()
      .SetColor(UiColor(0xFF5500))
      .SetOffset(Vector2(3.0f, 3.0f))
      .SetBlurRadius(2.0f));
```

Clear a shadow:

```cpp
label.ClearShadow();
```

`InputField` also supports shadow styling:

```cpp
InputField inputField = InputField::New()
  .SetText("Input with shadow")
  .SetShadow(
    Text::Shadow()
      .SetColor(UiColor(0xFF5500))
      .SetOffset(Vector2(2.0f, 2.0f)));
```

### Outline

`Text::Outline` draws a border around text characters for emphasis or special effects.

Create a basic outline:

```cpp
Label label = Label::New("Outlined Text")
  .SetOutline(
    Text::Outline()
      .SetWidth(2.0f));
```

Configure outline properties:

```cpp
Label label = Label::New("Styled Outline")
  .SetOutline(
    Text::Outline()
      .SetColor(UiColor(0x0066FF))
      .SetOffset(Vector2(1.0f, 1.0f))
      .SetWidth(2.0f)
      .SetBlurRadius(1.0f));
```

Clear an outline:

```cpp
label.ClearOutline();
```

`InputField` also supports outline styling:

```cpp
InputField inputField = InputField::New()
  .SetText("Input with outline")
  .SetOutline(
    Text::Outline()
      .SetColor(UiColor(0x0066FF))
      .SetWidth(2.0f));
```

### LineThrough

`Text::LineThrough` draws a horizontal line through text, commonly used to indicate deleted or completed items.

Create a basic line-through:

```cpp
Label label = Label::New("Struck Through")
  .SetLineThrough(Text::LineThrough());
```

Configure line-through properties:

```cpp
Label label = Label::New("Styled LineThrough")
  .SetLineThrough(
    Text::LineThrough()
      .SetColor(UiColor(0xFF00FF))
      .SetThickness(3.0f));
```

Clear a line-through:

```cpp
label.ClearLineThrough();
```

`InputField` also supports line-through styling:

```cpp
InputField inputField = InputField::New()
  .SetText("Input with line-through")
  .SetLineThrough(
    Text::LineThrough()
      .SetColor(UiColor(0xFF00FF))
      .SetThickness(2.0f));
```

### Bevel

`Text::Bevel` creates 3D embossed or engraved text effects by simulating light and shadow on character edges.

Create an embossed effect (raised text):

```cpp
Label label = Label::New("Embossed")
  .SetTextColor(UiColor(0x333333))
  .SetBevel(
    Text::Bevel()
      .SetDirection(Vector2(-1.0f, -1.0f))
      .SetIntensity(2.0f)
      .SetLightColor(UiColor(0x808080))
      .SetShadowColor(UiColor(0x0D0D0D)));
```

Create an engraved effect (sunken text):

```cpp
Label label = Label::New("Engraved")
  .SetTextColor(UiColor(0x212121))
  .SetBevel(
    Text::Bevel()
      .SetDirection(Vector2(-1.0f, -1.0f))
      .SetIntensity(2.0f)
      .SetLightColor(UiColor(0x0D0D0D))
      .SetShadowColor(UiColor(0x808080)));
```

Clear a bevel effect:

```cpp
label.ClearBevel();
```

#### Bevel Properties

| Property | Method | Description |
|----------|--------|-------------|
| Direction | `SetDirection(Vector2)` | Light source direction. Negative values for top-left light. |
| Intensity | `SetIntensity(float)` | Strength of the bevel effect. |
| LightColor | `SetLightColor(UiColor)` | Color for the highlighted edge. |
| ShadowColor | `SetShadowColor(UiColor)` | Color for the shadow edge. |

---

## Font Styling

### Font Weight, Width, and Slant

Control font appearance using `FontWeight`, `FontWidth`, and `FontSlant` enumerations.

Set font weight:

```cpp
Label label = Label::New("Bold Text")
  .SetFontWeight(Text::FontWeight::BOLD);
```

Set font width:

```cpp
Label label = Label::New("Condensed Text")
  .SetFontWidth(Text::FontWidth::SEMI_CONDENSED);
```

Set font slant:

```cpp
Label label = Label::New("Italic Text")
  .SetFontSlant(Text::FontSlant::ITALIC);
```

#### Available Font Weights

- `THIN`, `EXTRA_LIGHT`, `LIGHT`, `DEMI_LIGHT`, `BOOK`
- `NORMAL`, `MEDIUM`, `SEMI_BOLD`, `BOLD`, `EXTRA_BOLD`, `BLACK`

#### Available Font Widths

- `ULTRA_CONDENSED`, `EXTRA_CONDENSED`, `CONDENSED`, `SEMI_CONDENSED`
- `NORMAL`
- `SEMI_EXPANDED`, `EXPANDED`, `EXTRA_EXPANDED`, `ULTRA_EXPANDED`

#### Available Font Slants

- `NORMAL` - Upright text
- `ITALIC` - Italic style
- `OBLIQUE` - Oblique style

---

## Input Filtering

`Text::InputFilter` restricts user input in `InputField` components using regular expression patterns.

Create and apply an input filter:

```cpp
Text::InputFilter inputFilter;
inputFilter.SetAllowPattern("[\\d]");  // Allow only digits
inputFilter.SetDenyPattern("[0-5]");    // But deny 0-5

InputField inputField = InputField::New()
  .SetText("Digits 6-9 only")
  .SetInputFilter(inputFilter);
```

Clear an input filter:

```cpp
inputField.ClearInputFilter();
```

Handle rejected input through the `InputRejectedSignal`:

```cpp
void OnInputRejected(View view, Text::InputFilter::RejectReason reason)
{
  if(reason == Text::InputFilter::RejectReason::NOT_ALLOWED)
  {
    // Handle rejected character
  }
}

// Connect the signal
inputField.InputRejectedSignal().Connect(this, &YourClass::OnInputRejected);
```

---

## Font Variations

`Text::FontVariationAxis` configures OpenType font variation axes for variable fonts.

Create a font variation axis:

```cpp
Text::FontVariationAxis axis = Text::FontVariationAxis("wght", 450.0f);
```

Set axis properties:

```cpp
axis.SetTag("wdth");
axis.SetValue(75.0f);
```

Convert font variations to and from string format:

```cpp
// Parse from settings string
Dali::Vector<Text::FontVariationAxis> axes = Text::FontVariation::FromString(settings);

// Convert to string
Dali::String settings = Text::FontVariation::ToString(axes);
```

---

## Dynamic Font Sizing

### FitRange

`Text::FitRange` defines a range for automatic font size fitting within a text control.

Create a fit range:

```cpp
Text::FitRange fitRange = Text::FitRange()
  .SetMinimumFontSize(12.0f)
  .SetMaximumFontSize(48.0f)
  .SetFontSizeStep(2.0f);
```

### FitCandidate

`Text::FitCandidate` represents a candidate font size during the fitting process.

Create and configure a fit candidate:

```cpp
Text::FitCandidate candidate = Text::FitCandidate()
  .SetFontSize(24.0f)
  .SetLineHeight(28.0f);
```

Retrieve candidate properties:

```cpp
float fontSize = candidate.GetFontSize();
float lineHeight = candidate.GetLineHeight();
```
