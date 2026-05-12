---
title: Input Field
sidebar_label: Input Field
category: views-components
---

# Input Field

`Dali::Ui::InputField` is the dali-ui single-line editable text view for application text entry.

## Table of Contents

- [Create and Configure an Input Field](#create-and-configure-an-input-field)
- [Cursor Behavior](#cursor-behavior)
- [Text Appearance](#text-appearance)
- [Selection and Readback](#selection-and-readback)
- [Input Limits and Password Display](#input-limits-and-password-display)
- [Respond to Editing Signals](#respond-to-editing-signals)
- [Property Reference for App Code](#property-reference-for-app-code)

## Create and Configure an Input Field

Create an input field with `Dali::Ui::InputField::New`. In dali-ui application code, treat the field as a `Dali::Ui::View`-based object and configure it with typed `Dali::Ui::InputField` setters when they are available.

```cpp
Dali::Ui::InputField field = Dali::Ui::InputField::New();

field
  .SetEditable(true)
  .SetFontFamily("SamsungOne")
  .SetFontSize(20.0f)
  .SetFontSizeScale(1.0f);
```

Use getters when application logic needs to inspect the current configuration.

```cpp
Dali::String family = field.GetFontFamily();
float fontSize = field.GetFontSize();
float scale = field.GetFontSizeScale();
```

`Dali::Ui::InputField::Property::TEXT` and `Dali::Ui::InputField::Property::EDITABLE` are the owned property constants for the entered text and editability. Prefer the typed setter form for app-facing code when a typed setter exists.

## Cursor Behavior

The cursor can be configured with width, color, blink behavior, blink interval, handle image, pressed handle image, and position. `SetCursorPosition` uses a character index in the current text.

```cpp
Dali::Ui::InputField field = Dali::Ui::InputField::New();

field
  .SetCursorWidth(2)
  .SetCursorColor(Dali::Ui::UiColor(0x222222))
  .SetCursorBlinkEnabled(true)
  .SetCursorBlinkInterval(0.5f)
  .SetCursorPosition(0u);
```

Cursor configuration can be queried directly from the same `Dali::Ui::InputField`.

```cpp
int cursorWidth = field.GetCursorWidth();
Dali::Ui::UiColor cursorColor = field.GetCursorColor();
float blinkInterval = field.GetCursorBlinkInterval();
uint32_t cursorPosition = field.GetCursorPosition();
```

Use handle images when your application provides custom cursor handle artwork.

```cpp
Dali::Ui::InputField field = Dali::Ui::InputField::New();

field
  .SetCursorHandleImage("images/cursor_handle.png")
  .SetCursorHandlePressedImage("images/cursor_handle_pressed.png");

Dali::String image = field.GetCursorHandleImage();
Dali::String pressedImage = field.GetCursorHandlePressedImage();
```

The matching owned property constants are `Dali::Ui::InputField::Property::CURSOR_WIDTH`, `Dali::Ui::InputField::Property::CURSOR_COLOR`, `Dali::Ui::InputField::Property::CURSOR_BLINK_ENABLED`, `Dali::Ui::InputField::Property::CURSOR_BLINK_INTERVAL`, `Dali::Ui::InputField::Property::CURSOR_POSITION`, `Dali::Ui::InputField::Property::CURSOR_HANDLE_IMAGE`, and `Dali::Ui::InputField::Property::CURSOR_HANDLE_PRESSED_IMAGE`.

## Text Appearance

`Dali::Ui::InputField` owns font family, font size, font scale, font slant, font weight, font width, alignment, overflow, layout direction, text color, text background color, and placeholder color properties. Use the typed setters that are available for common font setup.

```cpp
Dali::Ui::InputField field = Dali::Ui::InputField::New();

field
  .SetFontFamily("SamsungOne")
  .SetFontSize(18.0f)
  .SetFontSizeScale(1.15f)
  .SetFontSlant(Dali::Ui::Text::FontSlant::ITALIC);
```

Read the resolved font configuration from the field when synchronizing UI state.

```cpp
Dali::String family = field.GetFontFamily();
float size = field.GetFontSize();
float requestedScale = field.GetFontSizeScale();
float adjustedScale = field.GetAdjustedFontSizeScale();
Dali::Ui::Text::FontSlant slant = field.GetFontSlant();
```

For scaling constraints, `GetMinimumFontSizeScale`, `GetMaximumFontSizeScale`, and `GetAdjustedFontSizeScale` let an application inspect the effective range used for rendering.

```cpp
float minimumScale = field.GetMinimumFontSizeScale();
float maximumScale = field.GetMaximumFontSizeScale();
float adjustedScale = field.GetAdjustedFontSizeScale();
```

Owned appearance properties include `Dali::Ui::InputField::Property::FONT_FAMILY`, `Dali::Ui::InputField::Property::FONT_SIZE`, `Dali::Ui::InputField::Property::FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::MINIMUM_FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::MAXIMUM_FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::SYSTEM_FONT_SIZE_SCALE_ENABLED`, `Dali::Ui::InputField::Property::FONT_SLANT`, `Dali::Ui::InputField::Property::FONT_WEIGHT`, `Dali::Ui::InputField::Property::FONT_WIDTH`, `Dali::Ui::InputField::Property::TEXT_COLOR`, `Dali::Ui::InputField::Property::TEXT_BACKGROUND_COLOR`, `Dali::Ui::InputField::Property::PLACEHOLDER_COLOR`, `Dali::Ui::InputField::Property::HORIZONTAL_ALIGNMENT`, `Dali::Ui::InputField::Property::VERTICAL_ALIGNMENT`, `Dali::Ui::InputField::Property::OVERFLOW_MODE`, and `Dali::Ui::InputField::Property::LAYOUT_DIRECTION_MODE`.

## Selection and Readback

Selection state is exposed through `GetSelectedText`. Cursor and selection-related property constants are owned by `Dali::Ui::InputField::Property`.

```cpp
Dali::Ui::InputField field = Dali::Ui::InputField::New();

Dali::String selected = field.GetSelectedText();
uint32_t cursor = field.GetCursorPosition();
```

Use `Dali::Ui::InputField::Property::SELECTION_ENABLED` to identify whether selection is enabled, `Dali::Ui::InputField::Property::SELECTION_COLOR` for the highlight color, and `Dali::Ui::InputField::Property::SELECTED_TEXT`, `Dali::Ui::InputField::Property::SELECTED_TEXT_START`, and `Dali::Ui::InputField::Property::SELECTED_TEXT_END` for selected text readback.

Custom selection handle assets are represented by `Dali::Ui::InputField::Property::SELECTION_HANDLE_IMAGE_LEFT`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_IMAGE_RIGHT`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_PRESSED_IMAGE_LEFT`, and `Dali::Ui::InputField::Property::SELECTION_HANDLE_PRESSED_IMAGE_RIGHT`.

## Input Limits and Password Display

Use the owned properties to describe maximum length and password display policy in application configuration. `Dali::Ui::InputField::Property::MAXIMUM_LENGTH` limits the number of characters that can be entered. `Dali::Ui::InputField::Property::PASSWORD_MODE` controls password display mode, `Dali::Ui::InputField::Property::PASSWORD_MASK_CHARACTER` stores the Unicode code point used for masking, and `Dali::Ui::InputField::Property::PASSWORD_REVEAL_DURATION` stores the reveal duration in milliseconds.

```cpp
Dali::Ui::InputField field = Dali::Ui::InputField::New();

int maximumLength = field.GetMaximumLength();
Dali::Ui::Text::PasswordMode mode = field.GetPasswordMode();
uint32_t maskCharacter = field.GetPasswordMaskCharacter();
uint32_t revealDuration = field.GetPasswordRevealDuration();
```

When the current mode is `Dali::Ui::Text::PasswordMode`, use `GetPasswordMode` to synchronize application controls with the active password display behavior.

## Respond to Editing Signals

Connect to the signal owned by the `Dali::Ui::InputField` instance that generates the event. Signal callbacks receive `Dali::Ui::View`, so application handlers can stay in the dali-ui view object model.

```cpp
class InputFieldController : public Dali::ConnectionTracker
{
public:
  void Setup()
  {
    mField = Dali::Ui::InputField::New();

    mField.TextChangedSignal().Connect(this, &InputFieldController::OnTextChanged);
    mField.CursorPositionChangedSignal().Connect(this, &InputFieldController::OnCursorPositionChanged);
    mField.MaximumLengthReachedSignal().Connect(this, &InputFieldController::OnMaximumLengthReached);
  }

private:
  void OnTextChanged(Dali::Ui::View view)
  {
    Dali::Ui::InputField field = Dali::Ui::InputField::DownCast(view);
    Dali::String selected = field.GetSelectedText();
  }

  void OnCursorPositionChanged(Dali::Ui::View view, uint32_t position)
  {
    Dali::Ui::InputField field = Dali::Ui::InputField::DownCast(view);
    uint32_t currentPosition = field.GetCursorPosition();
  }

  void OnMaximumLengthReached(Dali::Ui::View view)
  {
    Dali::Ui::InputField field = Dali::Ui::InputField::DownCast(view);
    int maximumLength = field.GetMaximumLength();
  }

  Dali::Ui::InputField mField;
};
```

Selection and typing-style events use their own signal signatures.

```cpp
class InputSelectionController : public Dali::ConnectionTracker
{
public:
  void Setup(Dali::Ui::InputField field)
  {
    mField = field;

    mField.SelectionStartedSignal().Connect(this, &InputSelectionController::OnSelectionStarted);
    mField.SelectionChangedSignal().Connect(this, &InputSelectionController::OnSelectionChanged);
    mField.SelectionClearedSignal().Connect(this, &InputSelectionController::OnSelectionCleared);
    mField.TypingStyleChangedSignal().Connect(this, &InputSelectionController::OnTypingStyleChanged);
  }

private:
  void OnSelectionStarted(Dali::Ui::View view)
  {
    Dali::Ui::InputField field = Dali::Ui::InputField::DownCast(view);
    Dali::String selected = field.GetSelectedText();
  }

  void OnSelectionChanged(Dali::Ui::View view, uint32_t start, uint32_t end)
  {
    Dali::Ui::InputField field = Dali::Ui::InputField::DownCast(view);
    Dali::String selected = field.GetSelectedText();
  }

  void OnSelectionCleared(Dali::Ui::View view)
  {
    Dali::Ui::InputField field = Dali::Ui::InputField::DownCast(view);
    uint32_t cursor = field.GetCursorPosition();
  }

  void OnTypingStyleChanged(Dali::Ui::View view, Dali::Ui::Text::TypingStyle::Mask mask)
  {
    Dali::Ui::InputField field = Dali::Ui::InputField::DownCast(view);
    float size = field.GetFontSize();
  }

  Dali::Ui::InputField mField;
};
```

`Dali::Ui::InputField::InputRejectedSignal` reports input rejected by an input filter.

```cpp
class InputRejectedController : public Dali::ConnectionTracker
{
public:
  void Setup(Dali::Ui::InputField field)
  {
    mField = field;
    mField.InputRejectedSignal().Connect(this, &InputRejectedController::OnInputRejected);
  }

private:
  void OnInputRejected(Dali::Ui::View view, Dali::Ui::Text::InputFilter::RejectReason reason)
  {
    Dali::Ui::InputField field = Dali::Ui::InputField::DownCast(view);
    uint32_t cursor = field.GetCursorPosition();
  }

  Dali::Ui::InputField mField;
};
```

## Property Reference for App Code

`Dali::Ui::InputField::Property` owns the property constants for this feature. Use these names when your app needs a property identifier for a configuration layer or state bridge.

```cpp
int textProperty = Dali::Ui::InputField::Property::TEXT;
int placeholderProperty = Dali::Ui::InputField::Property::PLACEHOLDER;
int cursorWidthProperty = Dali::Ui::InputField::Property::CURSOR_WIDTH;
int selectedTextProperty = Dali::Ui::InputField::Property::SELECTED_TEXT;
int passwordModeProperty = Dali::Ui::InputField::Property::PASSWORD_MODE;
```

Common groups are:

- Text content: `Dali::Ui::InputField::Property::TEXT`, `Dali::Ui::InputField::Property::PLACEHOLDER`, `Dali::Ui::InputField::Property::SHOW_PLACEHOLDER_ON_FOCUS`, `Dali::Ui::InputField::Property::MARKUP_ENABLED`
- Font and color: `Dali::Ui::InputField::Property::FONT_FAMILY`, `Dali::Ui::InputField::Property::FONT_SIZE`, `Dali::Ui::InputField::Property::FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::FONT_SLANT`, `Dali::Ui::InputField::Property::FONT_WEIGHT`, `Dali::Ui::InputField::Property::FONT_WIDTH`, `Dali::Ui::InputField::Property::TEXT_COLOR`, `Dali::Ui::InputField::Property::TEXT_BACKGROUND_COLOR`
- Cursor: `Dali::Ui::InputField::Property::CURSOR_WIDTH`, `Dali::Ui::InputField::Property::CURSOR_COLOR`, `Dali::Ui::InputField::Property::CURSOR_BLINK_ENABLED`, `Dali::Ui::InputField::Property::CURSOR_BLINK_INTERVAL`, `Dali::Ui::InputField::Property::CURSOR_POSITION`
- Selection: `Dali::Ui::InputField::Property::SELECTION_ENABLED`, `Dali::Ui::InputField::Property::SELECTION_COLOR`, `Dali::Ui::InputField::Property::SELECTED_TEXT`, `Dali::Ui::InputField::Property::SELECTED_TEXT_START`, `Dali::Ui::InputField::Property::SELECTED_TEXT_END`
- Password and limits: `Dali::Ui::InputField::Property::MAXIMUM_LENGTH`, `Dali::Ui::InputField::Property::PASSWORD_MODE`, `Dali::Ui::InputField::Property::PASSWORD_MASK_CHARACTER`, `Dali::Ui::InputField::Property::PASSWORD_REVEAL_DURATION`, `Dali::Ui::InputField::Property::EDITABLE`
- Typing style: `Dali::Ui::InputField::Property::TYPING_TEXT_COLOR`, `Dali::Ui::InputField::Property::TYPING_FONT_FAMILY`, `Dali::Ui::InputField::Property::TYPING_FONT_SIZE`, `Dali::Ui::InputField::Property::TYPING_FONT_WEIGHT`, `Dali::Ui::InputField::Property::TYPING_FONT_WIDTH`, `Dali::Ui::InputField::Property::TYPING_FONT_SLANT`
