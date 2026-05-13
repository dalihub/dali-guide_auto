---
title: Input Field
sidebar_label: Input Field
category: views-components
---

# Input Field

`Dali::Ui::InputField` is the dali-ui single-line editable text view for app text entry, cursor handling, text selection, and input-related signals.

## Table of Contents

- [Create an Input Field](#create-an-input-field)
- [Control Cursor Behavior](#control-cursor-behavior)
- [Read Text, Font, and Scale State](#read-text-font-and-scale-state)
- [React to Editing Signals](#react-to-editing-signals)
- [Selection and Password State](#selection-and-password-state)
- [Property Constants](#property-constants)

## Create an Input Field

Create an input field with `Dali::Ui::InputField::New`. In dali-ui application code, treat the control as a `Dali::Ui::View`-derived handle and configure it with typed setters when they are available.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

InputField CreateNameInput()
{
  return InputField::New()
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(20.0f)
    .SetFontSizeScale(1.0f)
    .SetFontSlant(Text::FontSlant::NORMAL)
    .SetEditable(true);
}
```

`Dali::Ui::InputField` derives from `Dali::Ui::View`, so it can be stored or passed through app code as `Dali::Ui::View` when the surrounding UI only needs the common view object model.

```cpp
View CreateInputView()
{
  InputField input = InputField::New()
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(18.0f)
    .SetEditable(true);

  return input;
}
```

Use `Dali::Ui::InputField::SetEditable` when an input field should display text-related state but stop accepting user edits.

```cpp
void SetReadOnly(InputField input)
{
  input.SetEditable(false);
}
```

## Control Cursor Behavior

The cursor APIs are owned by `Dali::Ui::InputField`. Use them to control cursor width, color, blink timing, handle images, and the current insertion position. Cursor position is a character index in the current text.

```cpp
void ConfigureCursor(InputField input)
{
  input
    .SetCursorWidth(2)
    .SetCursorColor(UiColor(0x222222))
    .SetCursorBlinkEnabled(true)
    .SetCursorBlinkInterval(0.5f)
    .SetCursorPosition(0u);
}
```

Cursor handle images are set separately for the normal and pressed states.

```cpp
void ConfigureCursorHandle(InputField input)
{
  input
    .SetCursorHandleImage("/opt/apps/example/res/cursor_handle.png")
    .SetCursorHandlePressedImage("/opt/apps/example/res/cursor_handle_pressed.png");
}
```

You can read the same cursor state back through the matching getters.

```cpp
void SnapshotCursorState(InputField input)
{
  const uint32_t position = input.GetCursorPosition();
  const int width = input.GetCursorWidth();
  const float blinkInterval = input.GetCursorBlinkInterval();
  const UiColor color = input.GetCursorColor();

  (void)position;
  (void)width;
  (void)blinkInterval;
  (void)color;
}
```

## Read Text, Font, and Scale State

`Dali::Ui::InputField` exposes typed getters for text, font family, size, scale, slant, weight, width, overflow, alignment, placeholder, and layout direction. Use these getters when your app needs to synchronize surrounding UI with the current input configuration.

```cpp
void ReadTypography(InputField input)
{
  const Dali::String text = input.GetText();
  const Dali::String family = input.GetFontFamily();
  const float fontSize = input.GetFontSize();
  const float requestedScale = input.GetFontSizeScale();
  const float adjustedScale = input.GetAdjustedFontSizeScale();
  const Text::FontSlant slant = input.GetFontSlant();
  const Text::FontWeight weight = input.GetFontWeight();
  const Text::FontWidth width = input.GetFontWidth();

  (void)text;
  (void)family;
  (void)fontSize;
  (void)requestedScale;
  (void)adjustedScale;
  (void)slant;
  (void)weight;
  (void)width;
}
```

The adjusted scale returned by `Dali::Ui::InputField::GetAdjustedFontSizeScale` is the scale used for rendering after minimum, maximum, and system scale settings are resolved.

```cpp
float GetRenderedFontSize(InputField input)
{
  return input.GetFontSize() * input.GetAdjustedFontSizeScale();
}
```

For layout-sensitive UI, read the text alignment and overflow policy from the input field.

```cpp
void ReadLayoutPolicy(InputField input)
{
  const Text::Alignment horizontal = input.GetHorizontalTextAlignment();
  const Text::Alignment vertical = input.GetVerticalTextAlignment();
  const Text::OverflowMode overflow = input.GetOverflowMode();
  const Text::LayoutDirectionMode direction = input.GetLayoutDirectionMode();

  (void)horizontal;
  (void)vertical;
  (void)overflow;
  (void)direction;
}
```

## React to Editing Signals

Connect to `Dali::Ui::InputField` signals from the field instance. Signal callbacks receive a `Dali::Ui::View`, keeping handlers aligned with the dali-ui view object model.

```cpp
class InputFieldHandler
{
public:
  void Attach(InputField input)
  {
    mInput = input;

    mInput.TextChangedSignal().Connect(this, &InputFieldHandler::OnTextChanged);
    mInput.MaximumLengthReachedSignal().Connect(this, &InputFieldHandler::OnMaximumLengthReached);
    mInput.CursorPositionChangedSignal().Connect(this, &InputFieldHandler::OnCursorPositionChanged);
  }

private:
  void OnTextChanged(View view)
  {
    const uint32_t cursor = mInput.GetCursorPosition();
    (void)view;
    (void)cursor;
  }

  void OnMaximumLengthReached(View view)
  {
    const int maximumLength = mInput.GetMaximumLength();
    (void)view;
    (void)maximumLength;
  }

  void OnCursorPositionChanged(View view, uint32_t position)
  {
    (void)view;
    (void)position;
  }

  InputField mInput;
};
```

Selection signals report when selection starts, changes, or is cleared.

```cpp
class SelectionHandler
{
public:
  void Attach(InputField input)
  {
    mInput = input;

    mInput.SelectionStartedSignal().Connect(this, &SelectionHandler::OnSelectionStarted);
    mInput.SelectionChangedSignal().Connect(this, &SelectionHandler::OnSelectionChanged);
    mInput.SelectionClearedSignal().Connect(this, &SelectionHandler::OnSelectionCleared);
  }

private:
  void OnSelectionStarted(View view)
  {
    (void)view;
  }

  void OnSelectionChanged(View view, uint32_t start, uint32_t end)
  {
    const Dali::String selected = mInput.GetSelectedText();
    (void)view;
    (void)start;
    (void)end;
    (void)selected;
  }

  void OnSelectionCleared(View view)
  {
    (void)view;
  }

  InputField mInput;
};
```

`Dali::Ui::InputField::InputRejectedSignal` reports input rejected by the configured `Dali::Ui::Text::InputFilter`, and `Dali::Ui::InputField::TypingStyleChangedSignal` reports detected typing-style changes at the current cursor position or selected text range.

```cpp
class InputPolicyHandler
{
public:
  void Attach(InputField input)
  {
    mInput = input;

    mInput.InputRejectedSignal().Connect(this, &InputPolicyHandler::OnInputRejected);
    mInput.TypingStyleChangedSignal().Connect(this, &InputPolicyHandler::OnTypingStyleChanged);
  }

private:
  void OnInputRejected(View view, Text::InputFilter::RejectReason reason)
  {
    (void)view;
    (void)reason;
  }

  void OnTypingStyleChanged(View view, Text::TypingStyle::Mask mask)
  {
    (void)view;
    (void)mask;
  }

  InputField mInput;
};
```

## Selection and Password State

Use `Dali::Ui::InputField::GetSelectedText` to read the selected text, and use the selection signals to know when that value may have changed.

```cpp
Dali::String CurrentSelection(InputField input)
{
  return input.GetSelectedText();
}
```

Password-related state is exposed through `Dali::Ui::InputField` getters. `Dali::Ui::InputField::GetPasswordMode` returns the current display mode, `Dali::Ui::InputField::GetPasswordMaskCharacter` returns the Unicode code point used for masking, and `Dali::Ui::InputField::GetPasswordRevealDuration` returns the reveal duration in milliseconds.

```cpp
struct PasswordDisplayState
{
  Text::PasswordMode mode;
  uint32_t maskCharacter;
  uint32_t revealDurationMilliseconds;
};

PasswordDisplayState ReadPasswordDisplayState(InputField input)
{
  return PasswordDisplayState{
    input.GetPasswordMode(),
    input.GetPasswordMaskCharacter(),
    input.GetPasswordRevealDuration()
  };
}
```

## Property Constants

`Dali::Ui::InputField::Property` owns the property constants for input-field configuration. Prefer typed setters and getters in application code when they exist, and use these property identifiers when integrating with property-driven code paths.

| Area | Owned property constants |
| --- | --- |
| Text content and color | `Dali::Ui::InputField::Property::TEXT`, `Dali::Ui::InputField::Property::TEXT_COLOR`, `Dali::Ui::InputField::Property::TEXT_BACKGROUND_COLOR` |
| Font | `Dali::Ui::InputField::Property::FONT_FAMILY`, `Dali::Ui::InputField::Property::FONT_SIZE`, `Dali::Ui::InputField::Property::FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::FONT_WEIGHT`, `Dali::Ui::InputField::Property::FONT_WIDTH`, `Dali::Ui::InputField::Property::FONT_SLANT` |
| Font scaling | `Dali::Ui::InputField::Property::MINIMUM_FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::MAXIMUM_FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::SYSTEM_FONT_SIZE_SCALE_ENABLED` |
| Placeholder | `Dali::Ui::InputField::Property::PLACEHOLDER`, `Dali::Ui::InputField::Property::PLACEHOLDER_COLOR`, `Dali::Ui::InputField::Property::SHOW_PLACEHOLDER_ON_FOCUS` |
| Cursor | `Dali::Ui::InputField::Property::CURSOR_WIDTH`, `Dali::Ui::InputField::Property::CURSOR_COLOR`, `Dali::Ui::InputField::Property::CURSOR_BLINK_ENABLED`, `Dali::Ui::InputField::Property::CURSOR_BLINK_INTERVAL`, `Dali::Ui::InputField::Property::CURSOR_POSITION`, `Dali::Ui::InputField::Property::CURSOR_HANDLE_IMAGE`, `Dali::Ui::InputField::Property::CURSOR_HANDLE_PRESSED_IMAGE` |
| Selection | `Dali::Ui::InputField::Property::SELECTION_ENABLED`, `Dali::Ui::InputField::Property::SELECTION_COLOR`, `Dali::Ui::InputField::Property::SELECTED_TEXT`, `Dali::Ui::InputField::Property::SELECTED_TEXT_START`, `Dali::Ui::InputField::Property::SELECTED_TEXT_END` |
| Selection and text handles | `Dali::Ui::InputField::Property::TEXT_HANDLE_ENABLED`, `Dali::Ui::InputField::Property::TEXT_HANDLE_COLOR`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_IMAGE_LEFT`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_IMAGE_RIGHT`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_PRESSED_IMAGE_LEFT`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_PRESSED_IMAGE_RIGHT` |
| Password and editing | `Dali::Ui::InputField::Property::PASSWORD_MODE`, `Dali::Ui::InputField::Property::PASSWORD_MASK_CHARACTER`, `Dali::Ui::InputField::Property::PASSWORD_REVEAL_DURATION`, `Dali::Ui::InputField::Property::EDITABLE`, `Dali::Ui::InputField::Property::MAXIMUM_LENGTH` |
| Layout and parsing | `Dali::Ui::InputField::Property::HORIZONTAL_ALIGNMENT`, `Dali::Ui::InputField::Property::VERTICAL_ALIGNMENT`, `Dali::Ui::InputField::Property::OVERFLOW_MODE`, `Dali::Ui::InputField::Property::LAYOUT_DIRECTION_MODE`, `Dali::Ui::InputField::Property::MARKUP_ENABLED` |
| Typing style | `Dali::Ui::InputField::Property::TYPING_TEXT_COLOR`, `Dali::Ui::InputField::Property::TYPING_FONT_FAMILY`, `Dali::Ui::InputField::Property::TYPING_FONT_SIZE`, `Dali::Ui::InputField::Property::TYPING_FONT_WEIGHT`, `Dali::Ui::InputField::Property::TYPING_FONT_WIDTH`, `Dali::Ui::InputField::Property::TYPING_FONT_SLANT` |
