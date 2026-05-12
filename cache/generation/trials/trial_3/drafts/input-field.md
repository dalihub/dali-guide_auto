---
title: Input Field
sidebar_label: Input Field
category: views-components
---

# Input Field

`Dali::Ui::InputField` is a single-line editable text view for dali-ui applications.

## Table of Contents

- [Create an Input Field](#create-an-input-field)
- [Text Appearance](#text-appearance)
- [Cursor Behavior](#cursor-behavior)
- [Editing State and Limits](#editing-state-and-limits)
- [Selection and Read-Back](#selection-and-read-back)
- [Password Display](#password-display)
- [Signals](#signals)
- [Property Constants](#property-constants)

## Create an Input Field

Create an input field with `Dali::Ui::InputField::New`. In application code, keep the object as `Dali::Ui::InputField` when configuring text-specific behavior. It is also a `Dali::Ui::View`, so it can be placed in the same view tree as other dali-ui views.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

InputField CreateNameField()
{
  InputField field = InputField::New()
    .SetEditable(true)
    .SetFontFamily("SamsungOne")
    .SetFontSize(20.0f)
    .SetFontSizeScale(1.0f)
    .SetCursorWidth(2)
    .SetCursorBlinkEnabled(true)
    .SetCursorBlinkInterval(0.5f)
    .SetCursorColor(UiColor(0x222222));

  return field;
}
```

`Dali::Ui::InputField` stores editable UTF-8 text. Use typed setters such as `SetFontFamily`, `SetFontSize`, `SetEditable`, `SetCursorWidth`, and `SetCursorColor` for common application setup.

## Text Appearance

Use `SetFontFamily`, `SetFontSize`, and `SetFontSizeScale` to configure the base text style. `GetFontFamily`, `GetFontSize`, `GetFontSizeScale`, and `GetAdjustedFontSizeScale` let you read the resolved configuration back.

```cpp
void ConfigureTextStyle(InputField field)
{
  field
    .SetFontFamily("SamsungOne")
    .SetFontSize(22.0f)
    .SetFontSizeScale(1.15f);
}

float ReadRenderedScale(InputField field)
{
  const float requestedScale = field.GetFontSizeScale();
  const float adjustedScale = field.GetAdjustedFontSizeScale();

  return adjustedScale > 0.0f ? adjustedScale : requestedScale;
}
```

For style state exposed through the property system, use the `Dali::Ui::InputField::Property` owner. The text-related constants include `InputField::Property::TEXT`, `InputField::Property::FONT_FAMILY`, `InputField::Property::FONT_SIZE`, `InputField::Property::FONT_SIZE_SCALE`, `InputField::Property::MINIMUM_FONT_SIZE_SCALE`, `InputField::Property::MAXIMUM_FONT_SIZE_SCALE`, `InputField::Property::SYSTEM_FONT_SIZE_SCALE_ENABLED`, `InputField::Property::TEXT_COLOR`, `InputField::Property::TEXT_BACKGROUND_COLOR`, `InputField::Property::FONT_WEIGHT`, `InputField::Property::FONT_WIDTH`, and `InputField::Property::FONT_SLANT`.

## Cursor Behavior

The insertion cursor is configured directly on `Dali::Ui::InputField`. Use `SetCursorWidth` for cursor thickness, `SetCursorColor` for color, `SetCursorBlinkEnabled` for blinking, `SetCursorBlinkInterval` for the blink interval in seconds, and `SetCursorPosition` for the character index.

```cpp
void ConfigureCursor(InputField field)
{
  field
    .SetCursorWidth(2)
    .SetCursorColor(UiColor(0x005BBB))
    .SetCursorBlinkEnabled(true)
    .SetCursorBlinkInterval(0.4f)
    .SetCursorPosition(0u);
}

void MoveCursorToStart(InputField field)
{
  const uint32_t oldPosition = field.GetCursorPosition();

  if(oldPosition != 0u)
  {
    field.SetCursorPosition(0u);
  }
}
```

Cursor handle images are also owned by `Dali::Ui::InputField`. `SetCursorHandleImage` sets the normal cursor handle image, and `SetCursorHandlePressedImage` sets the pressed image.

```cpp
void ConfigureCursorHandle(InputField field)
{
  field
    .SetCursorHandleImage("images/cursor_handle.png")
    .SetCursorHandlePressedImage("images/cursor_handle_pressed.png");
}

Dali::String ReadCursorHandleImage(InputField field)
{
  return field.GetCursorHandleImage();
}
```

Related property constants include `InputField::Property::CURSOR_WIDTH`, `InputField::Property::CURSOR_COLOR`, `InputField::Property::CURSOR_BLINK_ENABLED`, `InputField::Property::CURSOR_BLINK_INTERVAL`, `InputField::Property::CURSOR_POSITION`, `InputField::Property::CURSOR_HANDLE_IMAGE`, and `InputField::Property::CURSOR_HANDLE_PRESSED_IMAGE`.

## Editing State and Limits

`SetEditable` controls whether the user can edit the field through interaction. The maximum length is exposed through `InputField::Property::MAXIMUM_LENGTH` and can be read with `GetMaximumLength`.

```cpp
void MakeReadOnly(InputField field)
{
  field.SetEditable(false);
}

bool IsLengthLimitConfigured(InputField field)
{
  return field.GetMaximumLength() > 0;
}
```

Use `InputField::Property::EDITABLE` for the editable state and `InputField::Property::MAXIMUM_LENGTH` for the maximum character count when integrating with property-driven configuration.

## Selection and Read-Back

Selection state is owned by `Dali::Ui::InputField`. Use `GetSelectedText` to read the current selected UTF-8 text, and use cursor or selection signals to know when to refresh application UI.

```cpp
Dali::String CopySelectionForCommand(InputField field)
{
  return field.GetSelectedText();
}

uint32_t ReadInsertionPoint(InputField field)
{
  return field.GetCursorPosition();
}
```

Selection-related property constants include `InputField::Property::SELECTION_ENABLED`, `InputField::Property::SELECTION_COLOR`, `InputField::Property::SELECTED_TEXT`, `InputField::Property::SELECTED_TEXT_START`, `InputField::Property::SELECTED_TEXT_END`, `InputField::Property::SELECTION_HANDLE_IMAGE_LEFT`, `InputField::Property::SELECTION_HANDLE_IMAGE_RIGHT`, `InputField::Property::SELECTION_HANDLE_PRESSED_IMAGE_LEFT`, and `InputField::Property::SELECTION_HANDLE_PRESSED_IMAGE_RIGHT`.

## Password Display

Password behavior is part of `Dali::Ui::InputField`. Read password state with `GetPasswordMode`, `GetPasswordMaskCharacter`, and `GetPasswordRevealDuration`.

```cpp
uint32_t ReadMaskCharacter(InputField field)
{
  return field.GetPasswordMaskCharacter();
}

uint32_t ReadRevealDuration(InputField field)
{
  return field.GetPasswordRevealDuration();
}
```

Use `InputField::Property::PASSWORD_MODE` to represent the display mode, `InputField::Property::PASSWORD_MASK_CHARACTER` for the Unicode code point used as the mask character, and `InputField::Property::PASSWORD_REVEAL_DURATION` for the reveal duration in milliseconds.

## Signals

Connect to `Dali::Ui::InputField` signals to react to editing changes. Signal callbacks receive `Dali::Ui::View`, so downcast to `Dali::Ui::InputField` before reading input-field state.

```cpp
class SearchController : public ConnectionTracker
{
public:
  void Attach(InputField field)
  {
    mField = field;

    mField.TextChangedSignal().Connect(this, &SearchController::OnTextChanged);
    mField.CursorPositionChangedSignal().Connect(this, &SearchController::OnCursorPositionChanged);
    mField.MaximumLengthReachedSignal().Connect(this, &SearchController::OnMaximumLengthReached);
    mField.SelectionStartedSignal().Connect(this, &SearchController::OnSelectionStarted);
    mField.SelectionChangedSignal().Connect(this, &SearchController::OnSelectionChanged);
    mField.SelectionClearedSignal().Connect(this, &SearchController::OnSelectionCleared);
  }

private:
  void OnTextChanged(View view)
  {
    InputField field = InputField::DownCast(view);
    if(field)
    {
      const uint32_t cursor = field.GetCursorPosition();
    }
  }

  void OnCursorPositionChanged(View view, uint32_t position)
  {
    InputField field = InputField::DownCast(view);
    if(field)
    {
      const uint32_t current = position;
    }
  }

  void OnMaximumLengthReached(View view)
  {
    InputField field = InputField::DownCast(view);
    if(field)
    {
      const int maximumLength = field.GetMaximumLength();
    }
  }

  void OnSelectionStarted(View view)
  {
    InputField field = InputField::DownCast(view);
    if(field)
    {
      const Dali::String selectedText = field.GetSelectedText();
    }
  }

  void OnSelectionChanged(View view, uint32_t start, uint32_t end)
  {
    InputField field = InputField::DownCast(view);
    if(field)
    {
      const uint32_t selectionStart = start;
      const uint32_t selectionEnd = end;
    }
  }

  void OnSelectionCleared(View view)
  {
    InputField field = InputField::DownCast(view);
    if(field)
    {
      const Dali::String selectedText = field.GetSelectedText();
    }
  }

  InputField mField;
};
```

Use `TextChangedSignal` when the text content changes, `MaximumLengthReachedSignal` when input reaches the configured length limit, `CursorPositionChangedSignal` when the insertion position changes, `SelectionStartedSignal` when selection begins, `SelectionChangedSignal` when the selected range changes, and `SelectionClearedSignal` when selection is cleared.

## Property Constants

`Dali::Ui::InputField::Property` is the property owner for input-field configuration and state. Use these constants only with `Dali::Ui::InputField` instances.

```cpp
int CursorWidthProperty()
{
  return InputField::Property::CURSOR_WIDTH;
}

int HintTextProperty()
{
  return InputField::Property::PLACEHOLDER;
}

int SelectedTextProperty()
{
  return InputField::Property::SELECTED_TEXT;
}
```

Common property groups are:

- Text content and style: `InputField::Property::TEXT`, `InputField::Property::FONT_FAMILY`, `InputField::Property::FONT_SIZE`, `InputField::Property::TEXT_COLOR`, `InputField::Property::TEXT_BACKGROUND_COLOR`
- Hint text: `InputField::Property::PLACEHOLDER`, `InputField::Property::PLACEHOLDER_COLOR`, `InputField::Property::SHOW_PLACEHOLDER_ON_FOCUS`
- Cursor: `InputField::Property::CURSOR_WIDTH`, `InputField::Property::CURSOR_COLOR`, `InputField::Property::CURSOR_BLINK_ENABLED`, `InputField::Property::CURSOR_BLINK_INTERVAL`, `InputField::Property::CURSOR_POSITION`
- Selection: `InputField::Property::SELECTION_ENABLED`, `InputField::Property::SELECTION_COLOR`, `InputField::Property::SELECTED_TEXT`, `InputField::Property::SELECTED_TEXT_START`, `InputField::Property::SELECTED_TEXT_END`
- Editing and layout: `InputField::Property::EDITABLE`, `InputField::Property::MAXIMUM_LENGTH`, `InputField::Property::HORIZONTAL_ALIGNMENT`, `InputField::Property::VERTICAL_ALIGNMENT`, `InputField::Property::OVERFLOW_MODE`, `InputField::Property::LAYOUT_DIRECTION_MODE`
- Password: `InputField::Property::PASSWORD_MODE`, `InputField::Property::PASSWORD_MASK_CHARACTER`, `InputField::Property::PASSWORD_REVEAL_DURATION`
