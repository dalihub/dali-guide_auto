---
id: label
title: "label"
sidebar_label: "label"
---
## Overview

The Label component is a high-performance [text](./text.md) [rendering](./rendering.md) engine designed to display static and dynamic [text](./text.md) strings within your application's UI hierarchy. It serves as a non-editable container for [text](./text.md), providing developers with extensive control over typography, layout, and visual presentation.

```cpp
Label label = Label::New().SetText("Hello DALi");
```

## Text Content and Markup

The core content of a label is defined by the text string it displays. Developers can toggle rich-text capabilities to enable or disable markup processing, which allows for formatted text rendering within the same label.

The text content is managed through the `SetText(const Dali::String &)` and the `GetText()`. To enable advanced formatting, use the `SetMarkupEnabled(bool)`, which informs the engine whether to parse markup tags within the string. When using markup, developers can designate specific text segments as interactive anchors. The visual behavior of these anchors is controlled by the `SetAnchorColor(const UiColor &)` for the default state and the `SetAnchorClickedColor(const UiColor &)` for when a user interacts with the element.

```cpp
Label label = Label::New();
label.SetMarkupEnabled(true);
label.SetAnchorColor(UiColor(0.0f, 0.0f, 1.0f, 1.0f));
label.SetAnchorClickedColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));
```

## Typography and Font Styling

Text appearance is highly configurable, allowing for precise control over font attributes. These settings ensure that the text aligns with the overall design language of the application.

Developers can specify the font family using the `SetFontFamily(const Dali::String &)` and adjust the size using the `SetFontSize(float)`. Further refinements can be applied through weights, widths, and slants via the `SetFontWeight(Text::FontWeight)`, `SetFontWidth(Text::FontWidth)`, and `SetFontSlant(Text::FontSlant)` respectively. For modern typography, advanced font features are supported through font variations, which can be configured using the `SetFontVariation(const Dali::Vector< Text::FontVariationAxis > &)` and cleared when necessary with the `ClearFontVariation()`.

```cpp
Label label = Label::New();
label.SetFontFamily("SamsungSans");
label.SetFontSize(24.0f);
label.SetFontWeight(Text::FontWeight::BOLD);
Dali::Vector<Text::FontVariationAxis> axes;
label.SetFontVariation(axes);
```

## Layout and Sizing

The framework provides robust mechanisms to handle how text flows within a container, especially when the text length varies or exceeds the allocated screen real estate.

Whether text wraps to multiple lines is controlled by the `SetMultiLine(bool)`. When multi-line support is active, the wrapping logic is determined by the `SetLineWrapMode(Text::LineWrapMode)`, which supports modes such as word, character, and hyphenation wrapping. Alignment within the assigned bounds is handled by the `SetHorizontalTextAlignment(Text::Alignment)` and `SetVerticalTextAlignment(Text::Alignment)`. Additionally, the spacing between lines can be adjusted using the `SetLineHeight(float)`, with the interpretation mode set by the `SetLineHeightMode(Text::LineHeightMode)`. If content exceeds the bounds, the `SetOverflowMode(Text::OverflowMode)` dictates whether the text clips or uses an ellipsis.

```cpp
Label label = Label::New();
label.SetMultiLine(true);
label.SetLineWrapMode(Text::LineWrapMode::WORD);
label.SetHorizontalTextAlignment(Text::Alignment::CENTER);
label.SetVerticalTextAlignment(Text::Alignment::CENTER);
label.SetLineHeight(1.5f);
label.SetLineHeightMode(Text::LineHeightMode::RELATIVE);
```

## Adaptive Text and Scaling

Responsive applications require text to scale gracefully across different device resolutions and user-defined accessibility settings. 

The baseline scale for text can be set using the `SetFontSizeScale(float)`. To ensure text remains readable, developers can enforce constraints using the `SetMinimumFontSizeScale(float)` and the `SetMaximumFontSizeScale(float)`. Integration with system-wide text settings is enabled via the `SetSystemFontSizeScaleEnabled(bool)`, which allows the label to respect user preferences automatically.

```cpp
Label label = Label::New();
label.SetSystemFontSizeScaleEnabled(true);
label.SetMinimumFontSizeScale(0.8f);
label.SetMaximumFontSizeScale(1.2f);
```

## Visual Effects and Decoration

Visual enhancements improve the aesthetic quality and legibility of the displayed text. These decorations are applied as stylistic layers on the base text.

Stylistic additions such as shadows, underlines, and outlines are managed through dedicated setter methods including `SetShadow(const Text::Shadow &)`, `SetUnderline(const Text::Underline &)`, and `SetOutline(const Text::Outline &)`. The framework also supports strike-through text via the `SetLineThrough(const Text::LineThrough &)` and text bevels via the `SetBevel(const Text::Bevel &)`. Background coloring for specific text regions can be applied using the `SetTextBackgroundColor(const UiColor &)`. Each of these styles has a corresponding clearing method, such as `ClearShadow()` or `ClearUnderline()`, to revert to default styling.

```cpp
Label label = Label::New();
Text::Shadow shadow;
label.SetShadow(shadow);
Text::Underline underline;
label.SetUnderline(underline);
```

## Marquee and Motion

For strings that are too long to fit within their container, a marquee animation provides an automated scrolling mechanism to reveal the full content.

The behavior of the marquee is governed by the `SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy)`, which determines if the animation starts manually or automatically upon overflow. Once triggered, the speed and loop behavior are defined by the `SetMarqueeSpeed(int)`, `SetMarqueeLoopCount(int)`, and `SetMarqueeLoopDelay(float)`. The animation can be further customized by setting the gap between scrolls with the `SetMarqueeGap(int)`, the orientation with the `SetMarqueeOrientation(Text::MarqueeOrientation)`, and the stop behavior with the `SetMarqueeStopMode(Text::MarqueeStopMode)`. The marquee cycle is initiated by calling `StartMarquee()` and can be halted using `StopMarquee()`.

```cpp
Label label = Label::New();
label.SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy::ON_OVERFLOW);
label.SetMarqueeSpeed(50);
label.SetMarqueeLoopCount(3);
label.StartMarquee();
```

## Signal Handling and State

The Label component notifies the application of critical lifecycle and interaction events, enabling real-time UI synchronization and event-driven logic.

Interactive feedback is captured via the `AnchorClickedSignal()`, which fires when a user taps a defined anchor. Performance-sensitive applications may utilize asynchronous rendering to prevent UI thread blocking; status updates regarding these processes are provided by the `AsyncRenderFinishedSignal()`, `AsyncNaturalSizeComputedSignal()`, and `AsyncHeightForWidthComputedSignal()`. Monitoring these signals allows the application to react to changes in text geometry or rendering readiness dynamically.

```cpp
class MyHandler {
public:
  void OnAnchorClicked(View view, const Dali::String& anchorId) {
    // Handle link click
  }
};

// Inside initialization:
MyHandler* handler = new MyHandler();
label.AnchorClickedSignal().Connect(handler, &MyHandler::OnAnchorClicked);
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/label)
