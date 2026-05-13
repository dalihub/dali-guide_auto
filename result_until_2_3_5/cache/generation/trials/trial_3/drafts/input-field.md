---
title: Input Field
sidebar_label: Input Field
category: views-components
---

# Input Field

`Dali::Ui::InputField` is the dali-ui single-line editable text view for application text entry.

## Table of Contents

- [Create an Editable Field](#create-an-editable-field)
- [Style Text for Entry](#style-text-for-entry)
- [Configure Cursor Behavior](#configure-cursor-behavior)
- [Use Input Properties](#use-input-properties)
- [React to Input Events](#react-to-input-events)
- [Read Field State](#read-field-state)

## Create an Editable Field

Use `Dali::Ui::InputField::New` to create the field. In dali-ui application code, the field is a `Dali::Ui::View` in the view tree, while the typed `Dali::Ui::InputField` handle gives access to input-specific configuration.

```cpp
#include <dali-ui-foundation/public-api/input-field.h>

using namespace Dali::Ui;

InputField CreateEditableInputField()
{
  InputField field = InputField::New()
    .SetEditable(true)
    .SetFontFamily("Sans")
    .SetFontSize(20.0f)
    .SetFontSizeScale(1.0f)
    .SetFontSlant(Text::FontSlant::NORMAL)
    .SetCursorWidth(2)
    .SetCursorBlinkEnabled(true)
    .SetCursorBlinkInterval(0.5f);

  return field;
}
```

`SetEditable(true)` allows user editing. Use `SetEditable(false)` when the field should remain visible in the UI flow but should not accept user edits.

```cpp
InputField CreateReadOnlyInputField()
{
  return InputField::New()
    .SetEditable(false)
    .SetFontFamily("Sans")
    .SetFontSize(18.0f)
    .SetCursorBlinkEnabled(false);
}
```

## Style Text for Entry

`Dali::Ui::InputField` owns the font configuration used by the editable text. The typed setters return `Dali::Ui::InputField&`, so they can be chained during construction or applied later to an existing handle.

```cpp
void ApplyDefaultEntryStyle(InputField field)
{
  field
    .SetFontFamily("Sans")
    .SetFontSize(22.0f)
    .SetFontSizeScale(1.0f)
    .SetFontSlant(Text::FontSlant::NORMAL);
}
```

Use the matching getters when your application needs to preserve or inspect the current field style before applying a variant.

```cpp
void MakeItalicIfLarge(InputField field)
{
  const float currentSize = field.GetFontSize();

  if(currentSize >= 20.0f)
  {
    field.SetFontSlant(Text::FontSlant::ITALIC);
  }
}
```

For font-scale-aware sizing, `SetFontSizeScale`, `SetSystemFontSizeScaleEnabled`, `GetFontSizeScale`, `GetMinimumFontSizeScale`, `GetMaximumFontSizeScale`, and `GetAdjustedFontSizeScale` describe the configured and resolved scale values owned by the input field.

```cpp
float GetRenderedTextScale(InputField field)
{
  const float requestedScale = field.GetFontSizeScale();
  const float adjustedScale  = field.GetAdjustedFontSizeScale();

  return adjustedScale > 0.0f ? adjustedScale : requestedScale;
}
```

## Configure Cursor Behavior

The cursor is part of the `Dali::Ui::InputField` editing surface. Configure its width, color, blink behavior, blink interval, handle images, and character index through the input field handle.

```cpp
void ApplyCursorStyle(InputField field)
{
  field
    .SetCursorWidth(2)
    .SetCursorColor(UiColor(0x202020))
    .SetCursorBlinkEnabled(true)
    .SetCursorBlinkInterval(0.45f)
    .SetCursorPosition(0);
}
```

Use handle image setters when your application provides custom cursor assets.

```cpp
void ApplyCursorHandleImages(InputField field)
{
  field
    .SetCursorHandleImage("cursor_handle.png")
    .SetCursorHandlePressedImage("cursor_handle_pressed.png");
}
```

The cursor getters are useful when synchronizing a status view, preserving field state, or applying incremental updates without replacing the current settings.

```cpp
void SlowCursorBlink(InputField field)
{
  const float currentInterval = field.GetCursorBlinkInterval();

  if(currentInterval < 0.8f)
  {
    field.SetCursorBlinkInterval(0.8f);
  }
}

uint32_t ReadInsertionIndex(InputField field)
{
  return field.GetCursorPosition();
}
```

## Use Input Properties

`Dali::Ui::InputField::Property` owns the property constants exposed by the input field. Use these constants when integrating with code paths that consume dali-ui property indices.

Common text-entry properties include `Dali::Ui::InputField::Property::TEXT`, `Dali::Ui::InputField::Property::PLACEHOLDER_COLOR`, `Dali::Ui::InputField::Property::TEXT_COLOR`, `Dali::Ui::InputField::Property::TEXT_BACKGROUND_COLOR`, and `Dali::Ui::InputField::Property::EDITABLE`.

```cpp
int EditablePropertyIndex()
{
  return InputField::Property::EDITABLE;
}

int PlaceholderColorPropertyIndex()
{
  return InputField::Property::PLACEHOLDER_COLOR;
}
```

Cursor and selection property constants are also owned by `Dali::Ui::InputField::Property`: `CURSOR_WIDTH`, `CURSOR_COLOR`, `CURSOR_BLINK_ENABLED`, `CURSOR_BLINK_INTERVAL`, `CURSOR_POSITION`, `SELECTION_ENABLED`, `SELECTION_COLOR`, `SELECTED_TEXT`, `SELECTED_TEXT_START`, and `SELECTED_TEXT_END`.

```cpp
int CursorPositionPropertyIndex()
{
  return InputField::Property::CURSOR_POSITION;
}

int SelectedTextPropertyIndex()
{
  return InputField::Property::SELECTED_TEXT;
}
```

Password and font-scale configuration is likewise part of the input field property surface. The owning constants include `PASSWORD_MODE`, `PASSWORD_MASK_CHARACTER`, `PASSWORD_REVEAL_DURATION`, `FONT_SIZE_SCALE`, `MINIMUM_FONT_SIZE_SCALE`, `MAXIMUM_FONT_SIZE_SCALE`, and `SYSTEM_FONT_SIZE_SCALE_ENABLED`.

```cpp
int PasswordModePropertyIndex()
{
  return InputField::Property::PASSWORD_MODE;
}

int FontScalePropertyIndex()
{
  return InputField::Property::FONT_SIZE_SCALE;
}
```

## React to Input Events

Connect to `Dali::Ui::InputField` signals from your application controller to respond to user edits, cursor movement, selection changes, and typing-style changes.

```cpp
class InputFieldController : public Dali::ConnectionTracker
{
public:
  void Attach(InputField field)
  {
    mField = field;

    mField.TextChangedSignal().Connect(this, &InputFieldController::OnTextChanged);
    mField.MaximumLengthReachedSignal().Connect(this, &InputFieldController::OnMaximumLengthReached);
    mField.CursorPositionChangedSignal().Connect(this, &InputFieldController::OnCursorPositionChanged);
    mField.SelectionStartedSignal().Connect(this, &InputFieldController::OnSelectionStarted);
    mField.SelectionChangedSignal().Connect(this, &InputFieldController::OnSelectionChanged);
    mField.SelectionClearedSignal().Connect(this, &InputFieldController::OnSelectionCleared);
    mField.TypingStyleChangedSignal().Connect(this, &InputFieldController::OnTypingStyleChanged);
  }

private:
  void OnTextChanged(View view)
  {
    const uint32_t cursorPosition = mField.GetCursorPosition();
    (void)view;
    (void)cursorPosition;
  }

  void OnMaximumLengthReached(View view)
  {
    (void)view;
  }

  void OnCursorPositionChanged(View view, uint32_t position)
  {
    (void)view;
    (void)position;
  }

  void OnSelectionStarted(View view)
  {
    (void)view;
  }

  void OnSelectionChanged(View view, uint32_t start, uint32_t end)
  {
    (void)view;
    (void)start;
    (void)end;
  }

  void OnSelectionCleared(View view)
  {
    (void)view;
  }

  void OnTypingStyleChanged(View view, Text::TypingStyle::Mask mask)
  {
    (void)view;
    (void)mask;
  }

  InputField mField;
};
```

`TextChangedSignal` and `MaximumLengthReachedSignal` use a `Dali::Ui::View` callback parameter. `CursorPositionChangedSignal` adds the current character index. `SelectionChangedSignal` adds the selected range start and end indices. `TypingStyleChangedSignal` reports a `Dali::Ui::Text::TypingStyle::Mask`.

If the field has an input filter set with `SetInputFilter`, `InputRejectedSignal` reports why inserted input was rejected.

```cpp
class InputRejectionController : public Dali::ConnectionTracker
{
public:
  void Attach(InputField field)
  {
    mField = field;
    mField.InputRejectedSignal().Connect(this, &InputRejectionController::OnInputRejected);
  }

private:
  void OnInputRejected(View view, Text::InputFilter::RejectReason reason)
  {
    (void)view;

    if(reason == Text::InputFilter::RejectReason::NOT_ALLOWED)
    {
      mLastRejectedByAllowPattern = true;
    }
    else if(reason == Text::InputFilter::RejectReason::DENIED)
    {
      mLastRejectedByDenyPattern = true;
    }
  }

  InputField mField;
  bool       mLastRejectedByAllowPattern{false};
  bool       mLastRejectedByDenyPattern{false};
};
```

## Read Field State

Use `Dali::Ui::InputField` getters to query the current input configuration and editing state without leaving the dali-ui object model.

```cpp
struct InputFieldSnapshot
{
  Dali::String              fontFamily;
  float                     fontSize;
  float                     fontScale;
  float                     adjustedFontScale;
  Text::FontSlant           fontSlant;
  uint32_t                  cursorPosition;
  int                       cursorWidth;
  float                     cursorBlinkInterval;
  Dali::String              selectedText;
  Text::PasswordMode        passwordMode;
  uint32_t                  passwordMaskCharacter;
  uint32_t                  passwordRevealDuration;
  Text::LayoutDirectionMode layoutDirectionMode;
  Text::OverflowMode        overflowMode;
};

InputFieldSnapshot CaptureInputFieldSnapshot(InputField field)
{
  InputFieldSnapshot snapshot{
    field.GetFontFamily(),
    field.GetFontSize(),
    field.GetFontSizeScale(),
    field.GetAdjustedFontSizeScale(),
    field.GetFontSlant(),
    field.GetCursorPosition(),
    field.GetCursorWidth(),
    field.GetCursorBlinkInterval(),
    field.GetSelectedText(),
    field.GetPasswordMode(),
    field.GetPasswordMaskCharacter(),
    field.GetPasswordRevealDuration(),
    field.GetLayoutDirectionMode(),
    field.GetOverflowMode()
  };

  return snapshot;
}
```

Prompt text and cursor visual state can be inspected through the field handle as well.

```cpp
struct InputFieldVisualState
{
  Dali::String cursorHandleImage;
  Dali::String cursorHandlePressedImage;
  Dali::String promptText;
  UiColor      cursorColor;
  UiColor      promptTextColor;
};

InputFieldVisualState CaptureInputFieldVisualState(InputField field)
{
  InputFieldVisualState state{
    field.GetCursorHandleImage(),
    field.GetCursorHandlePressedImage(),
    field.GetPlaceholder(),
    field.GetCursorColor(),
    field.GetPlaceholderColor()
  };

  return state;
}
```
