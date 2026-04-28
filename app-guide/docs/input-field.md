---
id: input-field
title: "input-field"
sidebar_label: "input-field"
---
## Overview

The InputField component provides a high-performance, customizable [text](./text.md) entry interface designed to handle user character input and editing within DALi applications. It serves as a specialized view for single-line [text](./text.md) input, offering a robust set of properties to manage styling, cursor behavior, and input validation.

## Initialization and Basic Configuration

To begin using the component, you instantiate it through the provided factory method. Once created, you can configure the initial content and basic editing permissions for the user.

```cpp
InputField inputField = InputField::New();
inputField.SetText("Hello World").SetEditable(true);
```

The state of the field can be retrieved at any time to process user input. You can restrict the length of the string to ensure the input fits within application-defined boundaries.`GetText()`. You can verify if the user is currently permitted to interact with the field by checking the status.`IsEditable()`. The character limit can be defined to prevent excessive data entry.`SetMaximumLength(int)`.

## Typography and Visual Styling

You have full control over the visual presentation of the text to ensure it matches the application design. This includes standard properties like typeface and size, as well as specialized decorative features.

```cpp
InputField inputField = InputField::New();
inputField.SetFontFamily("Arial")
          .SetFontSize(24.0f)
          .SetFontWeight(Text::FontWeight::BOLD)
          .SetFontSlant(Text::FontSlant::ITALIC)
          .SetTextColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));
```

The framework supports advanced text decorations to emphasize content. You can apply visual enhancements such as underlines, shadows, outlines, or strikethrough effects.`SetUnderline(Text::Underline)`. These can be removed at any time to revert to plain text.`ClearUnderline()`. Additionally, font size scaling allows the UI to adapt to accessibility settings or dynamic layout requirements.`SetFontSizeScale(float)`. Font variations can be applied to support variable fonts.`SetFontVariation(const Dali::String &)`.

## Layout and Alignment

The layout engine ensures that text is positioned correctly within the bounds of the component, regardless of the reading direction or alignment requirements.

```cpp
InputField inputField = InputField::New();
inputField.SetHorizontalTextAlignment(Text::Alignment::CENTER)
          .SetVerticalTextAlignment(Text::Alignment::CENTER)
          .SetOverflowMode(Text::OverflowMode::ELLIPSIS);
```

Text alignment defines how characters sit within the available space. You can align content to the start, center, or end of the container.`SetHorizontalTextAlignment(Text::Alignment)`. If the text exceeds the container width, the overflow mode determines whether it is clipped or truncated with an ellipsis.`SetOverflowMode(Text::OverflowMode)`. Furthermore, the layout direction can be explicitly configured to support different language requirements.`SetLayoutDirectionMode(Text::LayoutDirectionMode)`.

## Cursor and Selection Management

Interaction and navigation within the text are handled by the cursor, which provides visual feedback to the user. You can customize its appearance and manage how text is selected programmatically.

```cpp
InputField inputField = InputField::New();
inputField.SetCursorWidth(2)
          .SetCursorBlinkEnabled(true)
          .SelectText(0, 5);
```

The cursor's width and color are fully configurable to ensure visibility against various backgrounds.`SetCursorWidth(int)`. You can toggle the blinking animation or adjust the interval to suit specific user preferences.`SetCursorBlinkEnabled(bool)`. When text selection is enabled, you can provide a unique highlight color.`SetSelectionColor(const UiColor &)`. You can also programmatically manipulate the selection range or clear it entirely when an action is complete.`SelectText(uint32_t, uint32_t)`.

## Input Interaction and Placeholders

Placeholder text provides context to the user before any input is provided. Coupled with input filters, you can ensure that only valid characters are accepted during the editing process.

```cpp
InputField inputField = InputField::New();
inputField.SetPlaceholder("Enter username...");
inputField.SetPlaceholderColor(UiColor(0.7f, 0.7f, 0.7f, 1.0f));

Text::InputFilter filter;
inputField.SetInputFilter(filter);
```

The placeholder text appears when the input field is empty and disappears when the user begins typing. You can configure whether this placeholder remains visible when the field gains focus.`SetShowPlaceholderOnFocus(bool)`. Input filters allow you to restrict the types of characters accepted, such as numeric-only or specific character sets.`SetInputFilter(const Text::InputFilter &)`. Filters can be cleared when the input constraints are no longer required.`ClearInputFilter()`.

## Responding to User Events

The InputField component emits signals to notify the application of significant changes, such as text updates, cursor movement, or input restrictions.

```cpp
InputField inputField = InputField::New();
inputField.TextChangedSignal().Connect([](View view) {
    // Handle text change
});
```

You can track user progress by monitoring the text content as it is modified.`TextChangedSignal()`. Applications can perform specific logic when a user hits the input length ceiling.`MaximumLengthReachedSignal()`. When an input filter rejects a character, the application is notified with a reason.`InputRejectedSignal()`. Additionally, changes to the selection or cursor position can be used to update secondary UI elements in real-time.`CursorPositionChangedSignal()`.

---

> 🔗 **API Reference**: [View Original Documentation](https://dummy-doxygen.tizen.org/dali/input-field)
