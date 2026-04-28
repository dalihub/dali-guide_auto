---
id: label
title: "label"
sidebar_label: "label"
---
## Overview

The Label component is a high-performance [text](./text.md) [rendering](./rendering.md) node used to display static or dynamic content in your UI, offering extensive support for styling, layout control, and interactive [text](./text.md) behaviors. It acts as an optimized container for [text](./text.md), providing a robust set of methods to handle visual presentation and layout behavior within the application hierarchy.

## Text Content and Markup

The primary function of a [label](./label.md) is to render [text](./text.md), which is managed via character string setters. When rich formatting is required within a single [label](./label.md), markup parsing can be toggled to interpret embedded tags.

The [text](./text.md) content is updated using the `SetText(const Dali::String &)`, while the current string can be retrieved via `GetText()`. To allow the interpretation of rich [text](./text.md) formatting, the `SetMarkupEnabled(bool)` should be used. The status of this feature can be verified by calling `IsMarkupEnabled()`.

```cpp
Label label = Label::New().SetMarkupEnabled(true).SetText("<b>Bold Text</b>");
```

## Typography and Font Styling

The visual representation of text is determined by font properties, which include family, size, weight, width, and slant. Advanced font rendering is supported through the use of OpenType font variations for dynamic typeface adjustments.

Font attributes are managed through specific setters: `SetFontFamily(const Dali::String &)`, `SetFontSize(float)`, `SetFontWeight(Text::FontWeight)`, `SetFontWidth(Text::FontWidth)`, and `SetFontSlant(Text::FontSlant)`. Corresponding getter methods such as `GetFontFamily()` and `GetFontSize()` allow for state verification. For specialized fonts that support OpenType axes, custom adjustments can be applied using `SetFontVariation(const Dali::String &)` or cleared with `ClearFontVariation()`.

```cpp
Label label = Label::New();
label.SetFontFamily("Arial").SetFontSize(24.0f).SetFontWeight(Text::FontWeight::BOLD);
```

## Layout, Alignment, and Sizing

Text layout is governed by multi-line settings, wrap modes, and alignment constraints. The framework determines how text fills the available container space by analyzing these parameters during the layout phase.

To enable text to span multiple rows, use `SetMultiLine(bool)` and verify with `IsMultiLine()`. Control over word breaks is provided by `SetLineWrapMode(Text::LineWrapMode)` using various options like `Text::LineWrapMode::WORD` or `Text::LineWrapMode::CHARACTER`. Horizontal and vertical positioning is adjusted via `SetHorizontalTextAlignment(Text::Alignment)` and `SetVerticalTextAlignment(Text::Alignment)` respectively, using alignments such as `Text::Alignment::CENTER`. Line density can be finely tuned using `SetLineHeight(float)` in combination with `SetLineHeightMode(Text::LineHeightMode)`, where the mode can be defined as `Text::LineHeightMode::RELATIVE` or `Text::LineHeightMode::ABSOLUTE`.

```cpp
Label label = Label::New();
label.SetMultiLine(true);
label.SetHorizontalTextAlignment(Text::Alignment::CENTER);
label.SetLineHeight(1.5f);
label.SetLineHeightMode(Text::LineHeightMode::RELATIVE);
```

## Advanced Visual Effects

Beyond basic font styling, the Label module supports decorative effects that enhance legibility or provide specific artistic designs, such as backgrounds, shadows, and strokes.

The text background color is defined by `SetTextBackgroundColor(const UiColor &)` and can be removed with `ClearTextBackgroundColor()`. Decorative strokes and depth are managed through `SetShadow(const Text::Shadow &)`, `SetOutline(const Text::Outline &)`, `SetUnderline(const Text::Underline &)`, `SetLineThrough(const Text::LineThrough &)`, and `SetBevel(const Text::Bevel &)`. Each of these has a corresponding clear method, such as `ClearShadow()` or `ClearOutline()`, to revert the visual state.

```cpp
Label label = Label::New();
label.SetShadow(Text::Shadow());
label.SetUnderline(Text::Underline());
```

## Dynamic Text and Marquee Behavior

The Label component supports automatic scaling and marquee-style scrolling for content that exceeds its layout bounds. These features ensure that text remains accessible even when space is constrained.

Marquee behavior is configured using parameters like `SetMarqueeSpeed(int)`, `SetMarqueeLoopCount(int)`, and `SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy)`. The animation can be initiated using `StartMarquee()` and terminated via `StopMarquee()`. For font scaling, the framework allows developers to enable system-level scaling with `SetSystemFontSizeScaleEnabled(bool)`, or enforce boundaries using `SetMinimumFontSizeScale(float)` and `SetMaximumFontSizeScale(float)`.

```cpp
Label label = Label::New();
label.SetMarqueeSpeed(50);
label.SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy::ON_OVERFLOW);
label.StartMarquee();
```

## Interactivity and Signaling

Interactivity within a label primarily involves monitoring text events or layout completion. This allows the application to respond to user input or asynchronous processing updates.

Click events on text anchors are monitored via the `AnchorClickedSignal()`. When performing asynchronous rendering or size calculations, the framework emits signals such as `AsyncRenderFinishedSignal()`, `AsyncNaturalSizeComputedSignal()`, or `AsyncHeightForWidthComputedSignal()` upon completion. To interact with specific anchor colors, use `SetAnchorColor(const UiColor &)` and `SetAnchorClickedColor(const UiColor &)`.

```cpp
class MyHandler {
public:
  void OnAnchorClicked(View view, const String &anchorId) {}
};
MyHandler handler;
Label label = Label::New();
label.AnchorClickedSignal().Connect(&handler, &MyHandler::OnAnchorClicked);
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/label)
