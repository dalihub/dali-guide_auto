---
title: Label
sidebar_label: Label
category: views-components
---

# Label

Label is a non-editable View that displays text. It performs text layout and rendering using the text rendering backend, but does not support user interaction or text editing.

## Table of Contents

- [Creating a Label](#creating-a-label)
- [Text and Font Configuration](#text-and-font-configuration)
- [Text Alignment](#text-alignment)
- [Multi-Line Text and Line Wrapping](#multi-line-text-and-line-wrapping)
- [Line Height Control](#line-height-control)
- [Overflow Handling](#overflow-handling)
- [Marquee Animation](#marquee-animation)
- [Font Size Scaling](#font-size-scaling)
- [Asynchronous Rendering](#asynchronous-rendering)
- [Text Styling](#text-styling)
- [Markup and Anchors](#markup-and-anchors)
- [Cutout and Mask Effects](#cutout-and-mask-effects)

## Creating a Label

Create a Label using `Label::New()`. You can pass an initial text string or set it later using `SetText()`.

```cpp
// Create an empty label
Label label1 = Label::New();

// Create a label with initial text
Label label2 = Label::New("Hello World");

// Set text after creation
label1.SetText("Welcome to DALi");

// Get the current text
Dali::String currentText = label1.GetText();
```

Labels support fluent chaining for all setters, allowing you to configure multiple properties in a single statement:

```cpp
Label label = Label::New("Hello World")
  .SetFontSize(24.0f)
  .SetTextColor(UiColor(0x000000))
  .SetHorizontalTextAlignment(Text::Alignment::CENTER)
  .SetVerticalTextAlignment(Text::Alignment::CENTER);
```

## Text and Font Configuration

Configure the font appearance using font family, size, weight, width, and slant properties.

### Font Family and Size

```cpp
Label label = Label::New("Sample Text")
  .SetFontFamily("SamsungOneUI_400")
  .SetFontSize(20.0f);

// Get current font settings
Dali::String fontFamily = label.GetFontFamily();
float fontSize = label.GetFontSize();
```

### Font Weight, Width, and Slant

Control the font style using `SetFontWeight()`, `SetFontWidth()`, and `SetFontSlant()`:

```cpp
Label boldLabel = Label::New("Bold Text")
  .SetFontFamily("SamsungOneUI_700")
  .SetFontWeight(Text::FontWeight::BOLD);

Label italicLabel = Label::New("Italic Text")
  .SetFontSlant(Text::FontSlant::ITALIC);

Label condensedLabel = Label::New("Condensed Text")
  .SetFontWidth(Text::FontWidth::CONDENSED);
```

### Font Variations

For variable fonts, use `SetFontVariation()` to specify axis values:

```cpp
Dali::Vector<Text::FontVariationAxis> axes;
axes.PushBack(Text::FontVariationAxis("wght", 700));
axes.PushBack(Text::FontVariationAxis("wdth", 90));

Label label = Label::New("Variable Font")
  .SetFontVariation(axes);
```

You can also set font variations using a settings string:

```cpp
label.SetFontVariation("wght=700,wdth=90");
```

## Text Alignment

Control how text is positioned within the label bounds using horizontal and vertical alignment.

```cpp
Label label = Label::New("Aligned Text")
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(100.0f)
  .SetHorizontalTextAlignment(Text::Alignment::CENTER)
  .SetVerticalTextAlignment(Text::Alignment::CENTER);
```

Available alignment values from `Text::Alignment`:
- `START` - Align to the start (left for horizontal, top for vertical)
- `CENTER` - Align to the center
- `END` - Align to the end (right for horizontal, bottom for vertical)

## Multi-Line Text and Line Wrapping

Enable multi-line layout with `SetMultiLine(true)` and control how lines are wrapped using `SetLineWrapMode()`.

```cpp
Label multiLineLabel = Label::New("This is a long text that will wrap across multiple lines")
  .SetRequestedWidth(300.0f)
  .SetMultiLine(true)
  .SetLineWrapMode(Text::LineWrapMode::WORD);
```

Available line wrap modes from `Text::LineWrapMode`:
- `WORD` - Wraps at word boundaries
- `CHARACTER` - Wraps at individual characters
- `HYPHENATION` - Wraps using hyphenation when possible
- `MIXED` - Tries WORD first, then HYPHENATION, falls back to CHARACTER

## Line Height Control

Control the spacing between lines using `SetLineHeight()` and `SetLineHeightMode()`.

### Relative Line Height

When using `Text::LineHeightMode::RELATIVE`, the line height is calculated as a multiplier of the font size:

```cpp
Label label = Label::New("Multi-line text\nwith double spacing")
  .SetMultiLine(true)
  .SetLineHeight(2.0f)
  .SetLineHeightMode(Text::LineHeightMode::RELATIVE);
```

### Absolute Line Height

When using `Text::LineHeightMode::ABSOLUTE`, the value is treated as an exact pixel height:

```cpp
Label label = Label::New("Fixed line height")
  .SetMultiLine(true)
  .SetLineHeight(50.0f)
  .SetLineHeightMode(Text::LineHeightMode::ABSOLUTE);
```

To use the natural line height derived from font metrics, set the line height to `Text::LINE_HEIGHT_AUTO`:

```cpp
label.SetLineHeight(Text::LINE_HEIGHT_AUTO);
```

## Overflow Handling

Control how text is rendered when it exceeds the available layout bounds using `SetOverflowMode()`.

```cpp
Label clippedLabel = Label::New("This text is too long for the available space")
  .SetRequestedWidth(200.0f)
  .SetOverflowMode(Text::OverflowMode::CLIP);

Label ellipsisLabel = Label::New("This text will show ellipsis")
  .SetRequestedWidth(200.0f)
  .SetOverflowMode(Text::OverflowMode::ELLIPSIS);
```

Available overflow modes from `Text::OverflowMode`:
- `CLIP` - Clips the overflowing text
- `ELLIPSIS` - Truncates with an ellipsis (...)

## Marquee Animation

Use marquee animation to scroll text that exceeds the available space. Configure the trigger policy, speed, loop count, and orientation.

### Starting and Stopping Marquee

```cpp
Label marqueeLabel = Label::New("This is a long scrolling text for marquee animation")
  .SetRequestedWidth(300.0f)
  .SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy::MANUAL);

// Start the marquee animation
marqueeLabel.StartMarquee();

// Stop the marquee animation
marqueeLabel.StopMarquee();
```

### Trigger Policies

Available trigger policies from `Text::MarqueeTriggerPolicy`:
- `MANUAL` - Starts only when `StartMarquee()` is called
- `ON_OVERFLOW` - Starts automatically when text exceeds available space

```cpp
Label autoMarquee = Label::New("Auto-scrolling text")
  .SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy::ON_OVERFLOW);
```

### Marquee Configuration

```cpp
Label label = Label::New("Scrolling text")
  .SetMarqueeSpeed(100)                    // Pixels per second
  .SetMarqueeLoopCount(3)                   // Number of loops
  .SetMarqueeLoopDelay(1.0f)                // Delay between loops in seconds
  .SetMarqueeGap(50)                        // Gap before wrap in pixels
  .SetMarqueeOrientation(Text::MarqueeOrientation::HORIZONTAL)
  .SetMarqueeStopMode(Text::MarqueeStopMode::FINISH_LOOP);
```

Available orientations from `Text::MarqueeOrientation`:
- `HORIZONTAL` - Scrolls horizontally (for single-line text)
- `VERTICAL` - Scrolls vertically (for multi-line text)

Available stop modes from `Text::MarqueeStopMode`:
- `IMMEDIATE` - Stops immediately
- `FINISH_LOOP` - Completes the current loop before stopping

## Font Size Scaling

Apply font size scaling for accessibility and responsive design. Use `SetFontSizeScale()` with minimum and maximum constraints.

### Basic Font Size Scaling

```cpp
Label label = Label::New("Scalable text")
  .SetFontSize(20.0f)
  .SetFontSizeScale(1.5f);  // 150% of base font size

float currentScale = label.GetFontSizeScale();
```

### Scale Constraints

Set minimum and maximum scale limits:

```cpp
Label label = Label::New("Constrained scale")
  .SetFontSizeScale(1.5f)
  .SetMinimumFontSizeScale(0.5f)
  .SetMaximumFontSizeScale(2.0f);
```

### System Font Size Scale

Enable system font size scale to respond to system accessibility settings:

```cpp
Label label = Label::New("System-aware text")
  .SetSystemFontSizeScaleEnabled(true);

bool isEnabled = label.IsSystemFontSizeScaleEnabled();
```

### Adjusted Font Size Scale

Get the final adjusted scale after applying all constraints:

```cpp
float adjustedScale = label.GetAdjustedFontSizeScale();
```

## Asynchronous Rendering

For complex text layouts, enable asynchronous rendering to avoid blocking the main thread.

### Enabling Async Rendering

```cpp
Label label = Label::New("Complex text layout")
  .SetAsyncRendering(true);

bool isAsync = label.IsAsyncRendering();
```

### Async Rendering Signals

Connect to signals to be notified when async operations complete:

```cpp
// Called when async rendering finishes
label.AsyncRenderFinishedSignal().Connect(this, &MyClass::OnAsyncRenderFinished);

// Called when async natural size computation finishes
label.AsyncNaturalSizeComputedSignal().Connect(this, &MyClass::OnAsyncNaturalSize);

// Called when async height-for-width computation finishes
label.AsyncHeightForWidthComputedSignal().Connect(this, &MyClass::OnAsyncHeightForWidth);
```

Signal callback signatures:

```cpp
void OnAsyncRenderFinished(View view, float width, float height);
void OnAsyncNaturalSize(View view, float width, float height);
void OnAsyncHeightForWidth(View view, float width, float height);
```

### Requesting Async Size Computation

```cpp
// Request natural size computation
label.RequestAsyncNaturalSize();

// Request height for a specific width
label.RequestAsyncHeightForWidth(300.0f);
```

### Render Scale

Improve rendering quality when the view is visually scaled by rendering at a higher resolution:

```cpp
Label label = Label::New("High quality scaled text")
  .SetAsyncRendering(true)
  .SetRenderScale(2.0f);  // Render at 2x resolution

float scale = label.GetRenderScale();
```

## Text Styling

Apply text styles including underline, shadow, outline, line-through, and bevel effects.

### Text Color and Background

```cpp
Label label = Label::New("Styled text")
  .SetTextColor(UiColor(0x000000))
  .SetTextBackgroundColor(UiColor(0xFFFF00));

UiColor textColor = label.GetTextColor();
UiColor bgColor = label.GetTextBackgroundColor();
```

### Text Fit

Use text fit to automatically select the largest font size that fits the available space:

```cpp
// Using FitRange
Label fitLabel = Label::New("Auto-sized text")
  .SetTextFit(Text::FitRange(10.0f, 40.0f, 5.0f));  // min, max, step

// Using FitCandidate
Dali::Vector<Text::FitCandidate> candidates;
candidates.PushBack(Text::FitCandidate(14.0f, 28.0f));  // fontSize, lineHeight
candidates.PushBack(Text::FitCandidate(18.0f, 36.0f));

Label candidateLabel = Label::New("Candidate-sized text")
  .SetTextFit(candidates);
```

## Markup and Anchors

Enable markup processing to display rich text with embedded HTML-like tags.

### Enabling Markup

```cpp
Label label = Label::New("<b>Bold</b> and <i>italic</i> text")
  .SetMarkupEnabled(true);
```

### Anchor Links

Configure anchor colors and handle anchor click events:

```cpp
Label anchorLabel = Label::New("<a href='link1'>Click here</a>")
  .SetMarkupEnabled(true)
  .SetAnchorColor(UiColor(0x0000FF))
  .SetAnchorClickedColor(UiColor(0xFF0000));

// Connect to anchor click signal
anchorLabel.AnchorClickedSignal().Connect(this, &MyClass::OnAnchorClicked);
```

The anchor click callback receives the view and the href string:

```cpp
void OnAnchorClicked(View view, const Dali::String& href)
{
  // Handle anchor click
}
```

## Cutout and Mask Effects

Render text as a cutout or apply mask effects for creative text displays.

### Cutout Text

When cutout is enabled, the glyph shapes are cut out from the rendered content:

```cpp
Label cutoutLabel = Label::New("Cutout")
  .SetBackgroundColor(UiColor(0x1F2A36))
  .SetCutoutEnabled(true);
```

### Mask Effect

Apply a mask effect using another view as the mask source:

```cpp
Label maskSource = Label::New("Background pattern text...")
  .SetTextColor(UiColor(0x3A6D75))
  .SetFontSize(10.0f)
  .SetMultiLine(true);

Label maskedLabel = Label::New("Hello")
  .SetTextColor(UiColor(0xFF0000))
  .SetMaskEffect(maskSource);
```

To clear the mask effect:

```cpp
maskedLabel.ClearMaskEffect();
