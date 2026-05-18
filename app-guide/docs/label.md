---
id: label
title: "label"
sidebar_label: "label"
---
## Overview

The Label component is a high-performance [text](./text.md) [rendering](./rendering.md) node used to display static or dynamic content within a DALi interface, providing advanced typography and styling capabilities. As an extension of the view hierarchy, it serves as the primary mechanism for presenting textual information to the user.

## Text Content and Markup

Displaying [text](./text.md) begins with initializing a [label](./label.md) instance and setting the primary string content. For developers requiring advanced formatting, the module supports rich [text](./text.md) parsing, which allows for embedded styling within the string content itself.

The primary content is managed by specifying a UTF-8 string.`SetText(const String &)` Developers can retrieve the current content for verification or logging purposes.`GetText()` When dynamic content includes formatting tags, it is necessary to explicitly enable the parsing engine.`SetMarkupEnabled(bool)` You can check the current state of this feature at any time.`IsMarkupEnabled()`

```cpp
Label label = Label::New("<b>Hello</b> <i>World</i>");
label.SetMarkupEnabled(true);
```

## Typography and Font Styling

The visual representation of text is highly customizable, allowing for precise control over the rendering of characters. You can modify font families, weights, widths, and slants to match your application's design language.

Individual attributes such as the font family, weight, width, and slant are configured through dedicated setter methods.`SetFontFamily(const String &), SetFontWeight(Text::FontWeight), SetFontWidth(Text::FontWidth), SetFontSlant(Text::FontSlant)` These settings can also be retrieved.`GetFontFamily(), GetFontWeight(), GetFontWidth(), GetFontSlant()` For variable fonts, specific axis adjustments can be applied to achieve custom typographic styles.`SetFontVariation(const Vector<Text::FontVariationAxis> &)` These settings can be cleared to return to default font behaviors.`ClearFontVariation()` Text size can be scaled relative to system settings, and you can define specific minimum and maximum limits for this scaling.`SetFontSizeScale(float), SetMinimumFontSizeScale(float), SetMaximumFontSizeScale(float)`

```cpp
Label label = Label::New("Custom Styled Text");
label.SetFontFamily("Arial");
label.SetFontWeight(Text::FontWeight::BOLD);
label.SetFontSizeScale(1.5f);
```

## Layout and Sizing Controls

Managing how text occupies space is essential for responsive interfaces, especially when dealing with content that exceeds the available bounds. Alignment and wrapping logic ensure the text remains legible within its container.

The horizontal and vertical placement of text relative to the label's frame is determined by alignment settings.`SetHorizontalTextAlignment(Text::Alignment), SetVerticalTextAlignment(Text::Alignment)` You can switch between single-line and multi-line modes to suit your layout requirements.`SetMultiLine(bool)` When text spans multiple lines, the wrapping behavior can be adjusted based on word, character, or hyphenation rules.`SetLineWrapMode(Text::LineWrapMode)` The distance between lines is controllable, and you can choose between absolute pixel values or relative multipliers.`SetLineHeight(float), SetLineHeightMode(Text::LineHeightMode)` In scenarios where the text is too large for the container, you can define an overflow policy to either clip the content or append an ellipsis.`SetOverflowMode(Text::OverflowMode)`

```cpp
Label label = Label::New("Multi-line text content here.");
label.SetMultiLine(true);
label.SetHorizontalTextAlignment(Text::Alignment::CENTER);
label.SetOverflowMode(Text::OverflowMode::ELLIPSIS);
label.SetLineHeight(1.2f);
label.SetLineHeightMode(Text::LineHeightMode::RELATIVE);
```

## Visual Effects and Decoration

Labels support a variety of visual decorations to improve readability or provide aesthetic impact. These effects are applied as distinct layers on top of or behind the rendered text.

Common decorations include underlines, line-throughs, drop shadows, outlines, and bevel effects.`SetUnderline(const Text::Underline &), SetLineThrough(const Text::LineThrough &), SetShadow(const Text::Shadow &), SetOutline(const Text::Outline &), SetBevel(const Text::Bevel &)` Each of these effects can be removed individually to reset the appearance.`ClearUnderline(), ClearLineThrough(), ClearShadow(), ClearOutline(), ClearBevel()` Additionally, you can apply a background color directly to the text block.`SetTextBackgroundColor(const UiColor &)` Graphical masking can be applied to the label to create custom rendering shapes.`SetMaskEffect(View)`

```cpp
Label label = Label::New("Highlighted Text");
label.SetShadow(Text::Shadow(UiColor(0.0f, 0.0f, 0.0f, 0.5f), Vector2(2.0f, 2.0f), 1));
label.SetUnderline(Text::Underline(true, UiColor::PRIMARY, 1.0f));
```

## Interactive Text and Marquee

Beyond static rendering, the label component can handle user interactions and automated motion for long-form content. This is particularly useful for interactive elements or narrow UI components.

Anchors within markup text can be made interactive, allowing the application to respond when a user taps them.`SetAnchorColor(const UiColor &), SetAnchorClickedColor(const UiColor &)` For content that is too wide or tall to fit in the available space, an automatic marquee animation can be used to scroll the text.`StartMarquee(), StopMarquee()` The behavior of this animation is governed by speed, loop counts, and trigger policies.`SetMarqueeSpeed(int), SetMarqueeLoopCount(int), SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy)`

```cpp
Label label = Label::New("Long text that will scroll.");
label.SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy::ON_OVERFLOW);
label.SetMarqueeSpeed(50);
label.StartMarquee();
```

## Signals and Asynchronous Handling

To maintain a smooth user experience, expensive text operations can be processed in the background. Signals are provided to notify the application when these operations conclude.

Asynchronous rendering is enabled by toggling the rendering flag.`SetAsyncRendering(bool)` Applications can monitor the completion of natural size requests, height-for-width computations, and general rendering passes through specific signals.`AsyncNaturalSizeComputedSignal(), AsyncHeightForWidthComputedSignal(), AsyncRenderFinishedSignal()` Anchor interaction is similarly handled via a signal.`AnchorClickedSignal()`

```cpp
class MyHandler {
public:
  void OnAnchorClicked(View view, const String &anchorId) {
    // Handle link interaction
  }
};
// Inside setup:
MyHandler* handler = new MyHandler();
label.AnchorClickedSignal().Connect(handler, &MyHandler::OnAnchorClicked);
```

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/label)
