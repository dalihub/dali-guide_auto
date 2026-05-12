---
title: Label
sidebar_label: Label
category: views-components
---

# Label

Label is a non-editable View that displays text with comprehensive styling and layout options.

## Table of Contents

- [Creating a Label](#creating-a-label)
- [Text Content and Styling](#text-content-and-styling)
- [Text Alignment and Layout](#text-alignment-and-layout)
- [Multi-line Text and Wrapping](#multi-line-text-and-wrapping)
- [Font Size Scaling](#font-size-scaling)
- [Marquee Animation](#marquee-animation)
- [Asynchronous Rendering](#asynchronous-rendering)
- [Advanced Text Effects](#advanced-text-effects)

## Creating a Label

Create a Label using `Label::New()`. You can pass an initial text string or set it later using `SetText()`.

```cpp
// Create with initial text
Label label = Label::New("Hello World");

// Create empty and set text later
Label label = Label::New()
  .SetText("Hello World");
```

All setters return a reference to the Label, enabling fluent chaining for configuration.

## Text Content and Styling

### Basic Text Properties

Set the text content and color using `SetText()` and `SetTextColor()`.

```cpp
Label label = Label::New("Welcome")
  .SetText("Updated text")
  .SetTextColor(UiColor(0xFFFFFF));
```

Retrieve the current text with `GetText()` and color with `GetTextColor()`.

### Font Configuration

Configure the font family, size, weight, width, and slant.

```cpp
Label label = Label::New("Styled Text")
  .SetFontFamily("SamsungOneUI_700")
  .SetFontSize(24.0f)
  .SetFontWeight(Text::FontWeight::BOLD)
  .SetFontWidth(Text::FontWidth::NORMAL)
  .SetFontSlant(Text::FontSlant::ITALIC);
```

Available font weight values include `THIN`, `LIGHT`, `NORMAL`, `MEDIUM`, `SEMI_BOLD`, `BOLD`, `EXTRA_BOLD`, and `BLACK`. Font width options include `CONDENSED`, `NORMAL`, and `EXPANDED`. Font slant options are `NORMAL`, `ITALIC`, and `OBLIQUE`.

### Font Variations

For variable fonts, use `SetFontVariation()` to specify axis settings.

```cpp
Dali::Vector<Text::FontVariationAxis> axes;
axes.PushBack(Text::FontVariationAxis("wght", 700));
axes.PushBack(Text::FontVariationAxis("wdth", 90));

Label label = Label::New("Variable Font")
  .SetFontVariation(axes);
```

Alternatively, use a settings string format:

```cpp
label.SetFontVariation("wght=700,wdth=90");
```

## Text Alignment and Layout

### Horizontal and Vertical Alignment

Control text alignment within the Label bounds using `SetHorizontalTextAlignment()` and `SetVerticalTextAlignment()`.

```cpp
Label label = Label::New("Centered")
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(100.0f)
  .SetHorizontalTextAlignment(Text::Alignment::CENTER)
  .SetVerticalTextAlignment(Text::Alignment::CENTER);
```

Alignment options are `START`, `CENTER`, and `END`. Horizontal alignment controls left-right positioning, while vertical alignment controls top-bottom positioning.

### Line Height

Control line spacing using `SetLineHeight()` and `SetLineHeightMode()`.

```cpp
// Relative line height (multiplier of font size)
Label label = Label::New("Multi-line\ntext")
  .SetMultiLine(true)
  .SetLineHeight(1.5f)
  .SetLineHeightMode(Text::LineHeightMode::RELATIVE);

// Absolute line height (pixels)
Label label2 = Label::New("Fixed spacing")
  .SetMultiLine(true)
  .SetLineHeight(50.0f)
  .SetLineHeightMode(Text::LineHeightMode::ABSOLUTE);
```

Use `LINE_HEIGHT_AUTO` to revert to natural line height derived from font metrics.

### Layout Direction

Control how text direction is resolved using `SetLayoutDirectionMode()`.

```cpp
Label label = Label::New("مرحبا بالعالم")
  .SetLayoutDirectionMode(Text::LayoutDirectionMode::CONTENTS);
```

Options include `CONTENTS` (detect from text), `INHERIT` (from parent), and `LOCALE` (from system).

## Multi-line Text and Wrapping

Enable multi-line layout with `SetMultiLine()` and control wrapping behavior with `SetLineWrapMode()`.

```cpp
Label label = Label::New("This is a long text that will wrap across multiple lines")
  .SetMultiLine(true)
  .SetLineWrapMode(Text::LineWrapMode::WORD)
  .SetRequestedWidth(300.0f);
```

Line wrap modes include:
- `WORD` - wraps at word boundaries
- `CHARACTER` - wraps at individual characters
- `HYPHENATION` - uses hyphenation when possible
- `MIXED` - tries word, then hyphenation, then character

### Overflow Handling

Control how text that exceeds the available space is displayed using `SetOverflowMode()`.

```cpp
Label label = Label::New("Long text that exceeds bounds")
  .SetRequestedWidth(200.0f)
  .SetOverflowMode(Text::OverflowMode::ELLIPSIS);
```

Overflow modes are `CLIP` (truncate at bounds) and `ELLIPSIS` (show "..." at end).

## Font Size Scaling

Label supports font size scaling with minimum and maximum constraints.

### Setting Font Size Scale

```cpp
Label label = Label::New("Scalable Text")
  .SetFontSize(20.0f)
  .SetFontSizeScale(1.5f)
  .SetMinimumFontSizeScale(0.5f)
  .SetMaximumFontSizeScale(2.0f);
```

The effective font size is calculated as: `fontSize * fontSizeScale`, clamped by minimum and maximum scale values.

### System Font Size Scale

Enable system font size scale integration with `SetSystemFontSizeScaleEnabled()`.

```cpp
Label label = Label::New("System Scale")
  .SetFontSizeScale(1.0f)
  .SetSystemFontSizeScaleEnabled(true);
```

When enabled, the system scale combines with the label's scale before applying constraints.

### Getting Adjusted Scale

Retrieve the final adjusted scale after all constraints are applied:

```cpp
float adjustedScale = label.GetAdjustedFontSizeScale();
```

### Text Fit

Use text fit to automatically select the largest font size that fits the available space.

```cpp
// Using FitRange
Label label = Label::New("Fitted Text")
  .SetTextFit(Text::FitRange(10, 20, 2));

// Using FitCandidate with custom sizes
Dali::Vector<Text::FitCandidate> candidates;
candidates.PushBack(Text::FitCandidate(10.0f, 20.0f));
candidates.PushBack(Text::FitCandidate(14.0f, 28.0f));
candidates.PushBack(Text::FitCandidate(18.0f, 36.0f));

Label label2 = Label::New("Custom Fit")
  .SetTextFit(candidates);
```

## Marquee Animation

Label supports scrolling text via marquee animation for text that overflows its bounds.

### Basic Marquee Setup

```cpp
Label label = Label::New("This is a long text that will scroll")
  .SetRequestedWidth(200.0f)
  .SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy::MANUAL)
  .SetMarqueeSpeed(100)
  .SetMarqueeLoopCount(2)
  .SetMarqueeLoopDelay(1.0f)
  .SetMarqueeGap(50);
```

### Trigger Policies

- `MANUAL` - marquee starts only when `StartMarquee()` is called
- `ON_OVERFLOW` - marquee starts automatically when text exceeds bounds

```cpp
// Manual trigger
label.SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy::MANUAL);
label.StartMarquee();

// Automatic on overflow
label.SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy::ON_OVERFLOW);
```

### Marquee Orientation

Set horizontal or vertical scrolling with `SetMarqueeOrientation()`.

```cpp
// Horizontal for single-line
Label hLabel = Label::New("Scrolling horizontally")
  .SetMarqueeOrientation(Text::MarqueeOrientation::HORIZONTAL);

// Vertical for multi-line
Label vLabel = Label::New("Line 1\nLine 2\nLine 3")
  .SetMultiLine(true)
  .SetMarqueeOrientation(Text::MarqueeOrientation::VERTICAL);
```

### Stop Behavior

Control how marquee stops with `SetMarqueeStopMode()`.

```cpp
label.SetMarqueeStopMode(Text::MarqueeStopMode::IMMEDIATE);  // Stop immediately
label.SetMarqueeStopMode(Text::MarqueeStopMode::FINISH_LOOP); // Finish current loop
```

## Asynchronous Rendering

For complex text layouts, enable asynchronous rendering to avoid blocking the UI thread.

### Enabling Async Rendering

```cpp
Label label = Label::New("Complex text layout")
  .SetAsyncRendering(true);
```

### Async Signals

Connect to signals to handle async rendering completion:

```cpp
// Render finished
label.AsyncRenderFinishedSignal().Connect(this, &MyClass::OnAsyncRenderFinished);

// Natural size computed
label.AsyncNaturalSizeComputedSignal().Connect(this, &MyClass::OnAsyncNaturalSize);

// Height for width computed
label.AsyncHeightForWidthComputedSignal().Connect(this, &MyClass::OnAsyncHeightForWidth);
```

Signal callback signatures:

```cpp
void OnAsyncRenderFinished(View view, float width, float height);
void OnAsyncNaturalSize(View view, float width, float height);
void OnAsyncHeightForWidth(View view, float width, float height);
```

### Requesting Async Computation

Request size computation asynchronously:

```cpp
label.RequestAsyncNaturalSize();
label.RequestAsyncHeightForWidth(220.0f);
```

### Render Scale

When scaling a label visually, use `SetRenderScale()` to improve rendering quality by rasterizing at a larger scale.

```cpp
Label label = Label::New("High quality scaled text")
  .SetAsyncRendering(true)
  .SetRenderScale(1.5f);
```

Render scale requires async rendering to be enabled and must be 1.0 or greater.

## Advanced Text Effects

### Text Background Color

Set a background color behind the text glyphs:

```cpp
Label label = Label::New("Highlighted")
  .SetTextBackgroundColor(UiColor(0xFFFF00));
```

Clear with `ClearTextBackgroundColor()`.

### Cutout Effect

Render text as a cutout (transparent text showing background):

```cpp
Label label = Label::New("Cutout Text")
  .SetTextColor(UiColor(0x000000).WithAlpha(0.0f))
  .SetBackgroundColor(UiColor(0x1F2A36))
  .SetCutoutEnabled(true);
```

### Mask Effect

Use another view as a mask source:

```cpp
Label maskSource = Label::New(BACKGROUND_TEXT)
  .SetTextColor(UiColor(0x3A6D75))
  .SetFontSize(10.0f);

Label label = Label::New("Masked Text")
  .SetTextColor(UiColor(0xFF0000))
  .SetMaskEffect(maskSource);
```

Clear the mask with `ClearMaskEffect()`.

### Markup Support

Enable markup processing for rich text:

```cpp
Label label = Label::New("<b>Bold</b> and <i>italic</i>")
  .SetMarkupEnabled(true);
```

### Anchor Links

Handle anchor clicks in markup text:

```cpp
Label label = Label::New("<a href='link1'>Click me</a>")
  .SetMarkupEnabled(true)
  .SetAnchorColor(UiColor(0x0000FF))
  .SetAnchorClickedColor(UiColor(0xFF0000));

label.AnchorClickedSignal().Connect(this, &MyClass::OnAnchorClicked);
```

Callback signature:

```cpp
void OnAnchorClicked(View view, const Dali::String& href);
```

### Text Styles

Apply underline, shadow, outline, and other text styles:

```cpp
// Underline
label.SetUnderline(Text::Underline(UiColor(0xFFFFFF), 1.0f));

// Shadow
label.SetShadow(Text::Shadow(UiColor(0x000000), Vector2(2.0f, 2.0f), 2.0f));

// Outline
label.SetOutline(Text::Outline(UiColor(0xFF0000), 2.0f));

// Line-through
label.SetLineThrough(Text::LineThrough(UiColor(0xFF0000), 1.0f));

// Bevel
label.SetBevel(Text::Bevel());
```

Clear individual styles with `ClearUnderline()`, `ClearShadow()`, `ClearOutline()`, `ClearLineThrough()`, and `ClearBevel()`.
