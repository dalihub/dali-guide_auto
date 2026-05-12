---
title: Input Field
sidebar_label: Input Field
category: uncategorized
---

# Input Field

`InputField` is a dali-ui control for text input, providing cursor management, text selection, password mode, and rich styling options for building interactive forms and text editors.

## Table of Contents

- [Creating an InputField](#creating-an-inputfield)
- [Text and Placeholder](#text-and-placeholder)
- [Cursor Configuration](#cursor-configuration)
- [Text Selection](#text-selection)
- [Font Styling](#font-styling)
- [Font Size Scaling](#font-size-scaling)
- [Typing Style](#typing-style)
- [Password Mode](#password-mode)
- [Signals](#signals)

## Creating an InputField

Create an `InputField` using `InputField::New()` and configure it using fluent setter chaining:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

InputField inputField = InputField::New()
  .SetPlaceholder("Enter your text here")
  .SetPlaceholderColor(UiColor(0x808080))
  .SetFontSize(20.0f)
  .SetCursorWidth(2)
  .SetCursorColor(UiColor(0x222222))
  .SetSelectionColor(UiColor(0xADD8E6))
  .SetMaximumLength(50)
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(60.0f)
  .SetBackgroundColor(UiColor(0xFFFFFF))
  .SetTextColor(UiColor(0x222222))
  .SetPadding(Extents(12, 12, 12, 12))
  .SetVerticalTextAlignment(Text::Alignment::CENTER)
  .SetFocusable(true);
```

Add the `InputField` to your view hierarchy:

```cpp
StackLayout rootLayout = StackLayout::New(StackOrientation::VERTICAL)
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT)
  .SetPadding(Extents(20, 20, 20, 20));

rootLayout.Add(inputField);
window.Add(rootLayout);
```

## Text and Placeholder

### Setting and Getting Text

Use `SetText()` to set the text content and `GetText()` to retrieve it:

```cpp
inputField.SetText("Hello world");

Dali::String currentText = inputField.GetText();
```

### Placeholder Configuration

The placeholder text appears when the field is empty. Configure its appearance and behavior:

```cpp
inputField.SetPlaceholder("Type here");
inputField.SetPlaceholderColor(UiColor(0x404040));
inputField.SetShowPlaceholderOnFocus(true);
```

Retrieve placeholder settings:

```cpp
Dali::String placeholder = inputField.GetPlaceholder();
UiColor placeholderColor = inputField.GetPlaceholderColor();
bool showOnFocus = inputField.IsPlaceholderShownOnFocus();
```

### Editable State

Control whether the user can edit the text:

```cpp
inputField.SetEditable(false);  // Make read-only

if (inputField.IsEditable()) {
  // Field accepts input
}
```

### Maximum Length

Limit the number of characters the user can enter:

```cpp
inputField.SetMaximumLength(100);

int maxLen = inputField.GetMaximumLength();
```

## Cursor Configuration

### Cursor Appearance

Customize the cursor width and color:

```cpp
inputField.SetCursorWidth(2);
inputField.SetCursorColor(UiColor(0x000000));
```

Retrieve cursor settings:

```cpp
int width = inputField.GetCursorWidth();
UiColor color = inputField.GetCursorColor();
```

### Cursor Blink

Enable or disable cursor blinking and set the blink interval:

```cpp
inputField.SetCursorBlinkEnabled(true);
inputField.SetCursorBlinkInterval(0.5f);  // 0.5 seconds

bool blinkEnabled = inputField.IsCursorBlinkEnabled();
float interval = inputField.GetCursorBlinkInterval();
```

### Cursor Position

Control the cursor position programmatically:

```cpp
inputField.SetCursorPosition(5u);

uint32_t position = inputField.GetCursorPosition();
```

The cursor position is clamped to valid positions within the text.

### Cursor Handle Images

Provide custom images for the cursor grab handle:

```cpp
inputField.SetCursorHandleImage("cursor_handle.png");
inputField.SetCursorHandlePressedImage("cursor_handle_pressed.png");
```

Retrieve the image paths:

```cpp
Dali::String handleImage = inputField.GetCursorHandleImage();
Dali::String pressedImage = inputField.GetCursorHandlePressedImage();
```

## Text Selection

### Enabling Selection

Enable or disable text selection:

```cpp
inputField.SetSelectionEnabled(true);

if (inputField.IsSelectionEnabled()) {
  // Selection is allowed
}
```

### Selection Color

Set the highlight color for selected text:

```cpp
inputField.SetSelectionColor(UiColor(0xADD8E6));

UiColor selColor = inputField.GetSelectionColor();
```

### Programmatic Selection

Select text programmatically using `SelectText()`:

```cpp
// Select characters from position 0 to 5
inputField.SelectText(0u, 5u);
```

Select all text with `SelectWholeText()`:

```cpp
inputField.SelectWholeText();
```

Clear the selection with `ClearSelection()`:

```cpp
inputField.ClearSelection();
```

### Retrieving Selection

Get the selected text range and content:

```cpp
uint32_t start = inputField.GetSelectedTextStart();
uint32_t end = inputField.GetSelectedTextEnd();
Dali::String selectedText = inputField.GetSelectedText();
```

### Selection Handle Images

Customize the selection handle images for left and right handles:

```cpp
inputField.SetSelectionHandleImageLeft("selection_handle_left.png");
inputField.SetSelectionHandleImageRight("selection_handle_right.png");
inputField.SetSelectionHandlePressedImageLeft("selection_handle_left_pressed.png");
inputField.SetSelectionHandlePressedImageRight("selection_handle_right_pressed.png");
```

### Text Handle

Enable text handles for cursor and selection manipulation:

```cpp
inputField.SetTextHandleEnabled(true);
inputField.SetTextHandleColor(UiColor(0x000080));

bool handlesEnabled = inputField.IsTextHandleEnabled();
UiColor handleColor = inputField.GetTextHandleColor();
```

## Font Styling

### Font Family and Size

Set the font family and size for the text:

```cpp
inputField.SetFontFamily("SamsungOneUI_400");
inputField.SetFontSize(24.0f);

Dali::String fontFamily = inputField.GetFontFamily();
float fontSize = inputField.GetFontSize();
```

### Font Weight, Width, and Slant

Configure detailed font properties:

```cpp
inputField.SetFontWeight(Text::FontWeight::BOLD);
inputField.SetFontWidth(Text::FontWidth::NORMAL);
inputField.SetFontSlant(Text::FontSlant::ITALIC);

Text::FontWeight weight = inputField.GetFontWeight();
Text::FontWidth width = inputField.GetFontWidth();
Text::FontSlant slant = inputField.GetFontSlant();
```

### Text Color

Set the text color:

```cpp
inputField.SetTextColor(UiColor(0x222222));

UiColor textColor = inputField.GetTextColor();
```

### Text Alignment

Control horizontal and vertical text alignment:

```cpp
inputField.SetHorizontalTextAlignment(Text::Alignment::CENTER);
inputField.SetVerticalTextAlignment(Text::Alignment::CENTER);

Text::Alignment hAlign = inputField.GetHorizontalTextAlignment();
Text::Alignment vAlign = inputField.GetVerticalTextAlignment();
```

### Text Background Color

Set a background color specifically for the text area:

```cpp
inputField.SetTextBackgroundColor(UiColor(0xFFFF00));

UiColor textBgColor = inputField.GetTextBackgroundColor();

// Clear the text background color
inputField.ClearTextBackgroundColor();
```

### Overflow Mode

Control how text that exceeds the bounds is displayed:

```cpp
inputField.SetOverflowMode(Text::OverflowMode::ELLIPSIS);
// or
inputField.SetOverflowMode(Text::OverflowMode::CLIP);

Text::OverflowMode mode = inputField.GetOverflowMode();
```

### Markup Support

Enable markup tags in the text content:

```cpp
inputField.SetMarkupEnabled(true);

if (inputField.IsMarkupEnabled()) {
  // Markup tags are processed
}
```

With markup enabled, you can use HTML-like tags:

```cpp
inputField.SetText("<font family='DejaVu Sans'><color value='#FF00FF'>Magenta</color> text</font>");
```

### Font Variation

Set OpenType font variation axes:

```cpp
Dali::Vector<Text::FontVariationAxis> axes;
axes.PushBack(Text::FontVariationAxis("wght", 700.0f));
axes.PushBack(Text::FontVariationAxis("wdth", 90.0f));
inputField.SetFontVariation(axes);

// Or use string format
inputField.SetFontVariation("wght=500,wdth=80");

// Clear font variation
inputField.ClearFontVariation();
```

Retrieve the current font variation settings:

```cpp
Dali::Vector<Text::FontVariationAxis> result = inputField.GetFontVariation();
```

## Font Size Scaling

`InputField` supports dynamic font size scaling for accessibility and responsive design.

### Basic Font Size Scale

Set a scale factor for the font size:

```cpp
inputField.SetFontSizeScale(1.5f);

float scale = inputField.GetFontSizeScale();
```

### Scale Range Limits

Set minimum and maximum scale limits:

```cpp
inputField.SetMinimumFontSizeScale(0.5f);
inputField.SetMaximumFontSizeScale(2.0f);

float minScale = inputField.GetMinimumFontSizeScale();
float maxScale = inputField.GetMaximumFontSizeScale();
```

### System Font Size Scale

Enable or disable system font size scale integration:

```cpp
inputField.SetSystemFontSizeScaleEnabled(true);

bool systemEnabled = inputField.IsSystemFontSizeScaleEnabled();
```

### Adjusted Font Size Scale

Get the final adjusted scale after applying limits and system settings:

```cpp
float adjustedScale = inputField.GetAdjustedFontSizeScale();
```

This value is clamped to the minimum and maximum scale range.

## Typing Style

Typing style allows you to set the font style for newly typed text, independent of the existing text style. This is useful for rich text editors.

### Typing Text Color

```cpp
inputField.SetTypingTextColor(UiColor(0xFF00FF));

UiColor typingColor = inputField.GetTypingTextColor();
```

### Typing Font Family

```cpp
inputField.SetTypingFontFamily("DejaVu Sans");

Dali::String typingFamily = inputField.GetTypingFontFamily();
```

### Typing Font Size

```cpp
inputField.SetTypingFontSize(20.0f);

float typingSize = inputField.GetTypingFontSize();
```

### Typing Font Weight, Width, and Slant

```cpp
inputField.SetTypingFontWeight(Text::FontWeight::BOLD);
inputField.SetTypingFontWidth(Text::FontWidth::CONDENSED);
inputField.SetTypingFontSlant(Text::FontSlant::ITALIC);

Text::FontWeight typingWeight = inputField.GetTypingFontWeight();
Text::FontWidth typingWidth = inputField.GetTypingFontWidth();
Text::FontSlant typingSlant = inputField.GetTypingFontSlant();
```

## Password Mode

`InputField` provides password input functionality with masking options.

### Password Mode

Set the password mode to control how characters are displayed:

```cpp
// Hide all characters
inputField.SetPasswordMode(Text::PasswordMode::HIDE_ALL);

// Show the last typed character briefly
inputField.SetPasswordMode(Text::PasswordMode::REVEAL_LAST_CHARACTER);

// Disable password mode
inputField.SetPasswordMode(Text::PasswordMode::NONE);

Text::PasswordMode mode = inputField.GetPasswordMode();
```

### Password Mask Character

Customize the character used to mask the password:

```cpp
inputField.SetPasswordMaskCharacter(0x2022);  // '•' BULLET

uint32_t maskChar = inputField.GetPasswordMaskCharacter();
```

### Password Reveal Duration

Set how long the last character is visible in `REVEAL_LAST_CHARACTER` mode:

```cpp
inputField.SetPasswordRevealDuration(1000u);  // 1000 milliseconds

uint32_t duration = inputField.GetPasswordRevealDuration();
```

## Signals

Connect to signals to respond to user interactions and text changes.

### Text Changed Signal

Emitted when the text content changes:

```cpp
inputField.TextChangedSignal().Connect(this, &MyClass::OnTextChanged);

void OnTextChanged(View view) {
  InputField field = InputField::DownCast(view);
  if (field) {
    Dali::String newText = field.GetText();
    // Handle text change
  }
}
```

### Maximum Length Reached Signal

Emitted when the user tries to exceed the maximum length:

```cpp
inputField.MaximumLengthReachedSignal().Connect(this, &MyClass::OnMaximumLengthReached);

void OnMaximumLengthReached(View view) {
  // Notify user that maximum length is reached
}
```

### Cursor Position Changed Signal

Emitted when the cursor position changes:

```cpp
inputField.CursorPositionChangedSignal().Connect(this, &MyClass::OnCursorPositionChanged);

void OnCursorPositionChanged(View view, uint32_t position) {
  // Handle cursor position change
}
```

### Selection Signals

Connect to selection-related signals:

```cpp
inputField.SelectionStartedSignal().Connect(this, &MyClass::OnSelectionStarted);
inputField.SelectionChangedSignal().Connect(this, &MyClass::OnSelectionChanged);
inputField.SelectionClearedSignal().Connect(this, &MyClass::OnSelectionCleared);

void OnSelectionStarted(View view) {
  // Selection started
}

void OnSelectionChanged(View view, uint32_t start, uint32_t end) {
  // Selection range changed
}

void OnSelectionCleared(View view) {
  // Selection was cleared
}
```

### Input Rejected Signal

Emitted when input is rejected by an input filter:

```cpp
inputField.InputRejectedSignal().Connect(this, &MyClass::OnInputRejected);

void OnInputRejected(View view, Text::InputFilter::RejectReason reason) {
  // Handle rejected input
}
```

### Typing Style Changed Signal

Emitted when the typing style changes:

```cpp
inputField.TypingStyleChangedSignal().Connect(this, &MyClass::OnTypingStyleChanged);

void OnTypingStyleChanged(View view, Text::TypingStyle::Mask mask) {
  if (mask & Text::TypingStyle::TEXT_COLOR) {
    // Text color changed
  }
  if (mask & Text::TypingStyle::FONT_FAMILY) {
    // Font family changed
  }
  // Check other mask flags as needed
}
```

## Layout Direction

Control the text layout direction for RTL language support:

```cpp
inputField.SetLayoutDirectionMode(Text::LayoutDirectionMode::LOCALE);
// or
inputField.SetLayoutDirectionMode(Text::LayoutDirectionMode::CONTENTS);

Text::LayoutDirectionMode mode = inputField.GetLayoutDirectionMode();
