---
title: Input Field
sidebar_label: Input Field
category: views-components
---

# Input Field

`Dali::Ui::InputField` is a single-line editable text view for dali-ui applications.

## Table of Contents

- [Create an Input Field](#create-an-input-field)
- [Control Editing and Cursor Feedback](#control-editing-and-cursor-feedback)
- [Apply Text Styling](#apply-text-styling)
- [Read Current Input State](#read-current-input-state)
- [Handle Input Field Signals](#handle-input-field-signals)
- [Use Owned Properties](#use-owned-properties)

## Create an Input Field

Create an input field with `Dali::Ui::InputField::New`. In application code, treat it as a `Dali::Ui::View` object that can be placed in your dali-ui view tree, while configuring text-entry behavior through `Dali::Ui::InputField` typed APIs.

```cpp
#include <dali-ui-foundation/public-api/input-field.h>
#include <dali-ui-foundation/public-api/ui-color.h>

Dali::Ui::InputField CreateNameField()
{
  return Dali::Ui::InputField::New()
    .SetEditable(true)
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(20.0f)
    .SetCursorWidth(2)
    .SetCursorColor(Dali::Ui::UiColor(0x222222))
    .SetCursorBlinkEnabled(true)
    .SetCursorBlinkInterval(0.5f);
}
```

`Dali::Ui::InputField::SetEditable` controls whether the user can edit the field through interaction. `Dali::Ui::InputField::SetFontFamily`, `Dali::Ui::InputField::SetFontSize`, and cursor setters are chainable and return the same `Dali::Ui::InputField`.

## Control Editing and Cursor Feedback

Use cursor APIs when the input field needs a custom editing feel. `Dali::Ui::InputField::SetCursorWidth` takes a pixel width, `Dali::Ui::InputField::SetCursorColor` takes a `Dali::Ui::UiColor`, and `Dali::Ui::InputField::SetCursorBlinkInterval` uses seconds.

```cpp
void ConfigureCursor(Dali::Ui::InputField field)
{
  field
    .SetCursorBlinkEnabled(true)
    .SetCursorBlinkInterval(0.35f)
    .SetCursorWidth(3)
    .SetCursorColor(Dali::Ui::UiColor(0x0057D9))
    .SetCursorPosition(0u);
}
```

For themed text handles, set image URLs with `Dali::Ui::InputField::SetCursorHandleImage` and `Dali::Ui::InputField::SetCursorHandlePressedImage`.

```cpp
void ConfigureCursorHandle(Dali::Ui::InputField field)
{
  field
    .SetCursorHandleImage("images/input/cursor_handle.png")
    .SetCursorHandlePressedImage("images/input/cursor_handle_pressed.png");
}
```

The matching read APIs let you inspect the active state:

```cpp
struct CursorState
{
  bool     blinkEnabled;
  float    blinkInterval;
  int      width;
  uint32_t position;
};

CursorState ReadCursorState(Dali::Ui::InputField field)
{
  return CursorState{
    field.IsCursorBlinkEnabled(),
    field.GetCursorBlinkInterval(),
    field.GetCursorWidth(),
    field.GetCursorPosition()
  };
}
```

## Apply Text Styling

Use the input field font setters for the default text style. `Dali::Ui::InputField::SetFontSizeScale` multiplies the current font size by the configured scale, and the adjusted scale can be read with `Dali::Ui::InputField::GetAdjustedFontSizeScale`.

```cpp
Dali::Ui::InputField CreateSearchField()
{
  return Dali::Ui::InputField::New()
    .SetEditable(true)
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(18.0f)
    .SetFontSizeScale(1.0f)
    .SetFontSlant(Dali::Ui::Text::FontSlant::NORMAL);
}
```

You can read the resolved styling back when synchronizing app state or a formatting panel.

```cpp
void CaptureFontState(Dali::Ui::InputField field)
{
  Dali::String fontFamily = field.GetFontFamily();
  float fontSize = field.GetFontSize();
  float requestedScale = field.GetFontSizeScale();
  float adjustedScale = field.GetAdjustedFontSizeScale();
  Dali::Ui::Text::FontSlant slant = field.GetFontSlant();

  (void)fontFamily;
  (void)fontSize;
  (void)requestedScale;
  (void)adjustedScale;
  (void)slant;
}
```

`Dali::Ui::InputField::GetFontWeight`, `Dali::Ui::InputField::GetFontWidth`, and `Dali::Ui::InputField::GetFontVariation` are useful when your UI mirrors the current field style in a toolbar or inspector.

## Read Current Input State

Application code often needs to read input state after user edits, cursor movement, or selection changes. `Dali::Ui::InputField::GetSelectedText` returns the current selected UTF-8 text, and `Dali::Ui::InputField::GetMaximumLength` returns the configured character limit.

```cpp
struct InputSnapshot
{
  uint32_t     cursorPosition;
  int          maximumLength;
  Dali::String selectedText;
};

InputSnapshot SnapshotInput(Dali::Ui::InputField field)
{
  return InputSnapshot{
    field.GetCursorPosition(),
    field.GetMaximumLength(),
    field.GetSelectedText()
  };
}
```

Password-related state is also readable from the input field. `Dali::Ui::InputField::GetPasswordMode` returns a `Dali::Ui::Text::PasswordMode`, `Dali::Ui::InputField::GetPasswordMaskCharacter` returns the mask character as a Unicode code point, and `Dali::Ui::InputField::GetPasswordRevealDuration` returns milliseconds.

```cpp
bool UsesPasswordMasking(Dali::Ui::InputField field)
{
  return field.GetPasswordMode() != Dali::Ui::Text::PasswordMode::NONE;
}

uint32_t GetMaskCodePoint(Dali::Ui::InputField field)
{
  return field.GetPasswordMaskCharacter();
}
```

## Handle Input Field Signals

Connect to `Dali::Ui::InputField` signals when the application needs to respond to user editing. Signal callbacks receive `Dali::Ui::View`, matching the dali-ui app-facing object model.

```cpp
class FormController : public Dali::ConnectionTracker
{
public:
  void Connect(Dali::Ui::InputField field)
  {
    field.TextChangedSignal().Connect(this, &FormController::OnTextChanged);
    field.CursorPositionChangedSignal().Connect(this, &FormController::OnCursorPositionChanged);
    field.MaximumLengthReachedSignal().Connect(this, &FormController::OnMaximumLengthReached);
    field.SelectionStartedSignal().Connect(this, &FormController::OnSelectionStarted);
    field.SelectionChangedSignal().Connect(this, &FormController::OnSelectionChanged);
    field.SelectionClearedSignal().Connect(this, &FormController::OnSelectionCleared);
  }

private:
  void OnTextChanged(Dali::Ui::View view)
  {
    (void)view;
  }

  void OnCursorPositionChanged(Dali::Ui::View view, uint32_t position)
  {
    (void)view;
    (void)position;
  }

  void OnMaximumLengthReached(Dali::Ui::View view)
  {
    (void)view;
  }

  void OnSelectionStarted(Dali::Ui::View view)
  {
    (void)view;
  }

  void OnSelectionChanged(Dali::Ui::View view, uint32_t start, uint32_t end)
  {
    (void)view;
    (void)start;
    (void)end;
  }

  void OnSelectionCleared(Dali::Ui::View view)
  {
    (void)view;
  }
};
```

Use `Dali::Ui::InputField::TextChangedSignal` for content changes, `Dali::Ui::InputField::CursorPositionChangedSignal` for insertion-point tracking, and the selection signals for UI that depends on the selected range.

For filtered input workflows, connect `Dali::Ui::InputField::InputRejectedSignal` after applying an input filter. The callback includes a `Dali::Ui::Text::InputFilter::RejectReason`.

```cpp
class FilterStatusController : public Dali::ConnectionTracker
{
public:
  void Connect(Dali::Ui::InputField field)
  {
    field.InputRejectedSignal().Connect(this, &FilterStatusController::OnInputRejected);
  }

private:
  void OnInputRejected(
    Dali::Ui::View view,
    Dali::Ui::Text::InputFilter::RejectReason reason)
  {
    (void)view;
    (void)reason;
  }
};
```

Use `Dali::Ui::InputField::TypingStyleChangedSignal` when a formatting UI needs to update after the current typing style changes at the cursor position or selected range.

```cpp
class TypingStyleController : public Dali::ConnectionTracker
{
public:
  void Connect(Dali::Ui::InputField field)
  {
    field.TypingStyleChangedSignal().Connect(this, &TypingStyleController::OnTypingStyleChanged);
  }

private:
  void OnTypingStyleChanged(
    Dali::Ui::View view,
    Dali::Ui::Text::TypingStyle::Mask mask)
  {
    (void)view;
    (void)mask;
  }
};
```

## Use Owned Properties

`Dali::Ui::InputField::Property` defines the input-field property constants. Prefer typed setters and getters for direct application code, and use property constants when integrating with property-driven tooling or configuration layers that already work with dali-ui properties.

Common content and visual properties include `Dali::Ui::InputField::Property::TEXT`, `Dali::Ui::InputField::Property::PLACEHOLDER`, `Dali::Ui::InputField::Property::PLACEHOLDER_COLOR`, `Dali::Ui::InputField::Property::TEXT_COLOR`, `Dali::Ui::InputField::Property::TEXT_BACKGROUND_COLOR`, `Dali::Ui::InputField::Property::FONT_FAMILY`, `Dali::Ui::InputField::Property::FONT_SIZE`, `Dali::Ui::InputField::Property::FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::MINIMUM_FONT_SIZE_SCALE`, and `Dali::Ui::InputField::Property::MAXIMUM_FONT_SIZE_SCALE`.

Editing and cursor properties include `Dali::Ui::InputField::Property::EDITABLE`, `Dali::Ui::InputField::Property::CURSOR_POSITION`, `Dali::Ui::InputField::Property::CURSOR_WIDTH`, `Dali::Ui::InputField::Property::CURSOR_COLOR`, `Dali::Ui::InputField::Property::CURSOR_BLINK_ENABLED`, and `Dali::Ui::InputField::Property::CURSOR_BLINK_INTERVAL`.

Selection and handle properties include `Dali::Ui::InputField::Property::SELECTION_ENABLED`, `Dali::Ui::InputField::Property::SELECTION_COLOR`, `Dali::Ui::InputField::Property::SELECTED_TEXT`, `Dali::Ui::InputField::Property::SELECTED_TEXT_START`, `Dali::Ui::InputField::Property::SELECTED_TEXT_END`, `Dali::Ui::InputField::Property::CURSOR_HANDLE_IMAGE`, and `Dali::Ui::InputField::Property::CURSOR_HANDLE_PRESSED_IMAGE`.

```cpp
int TextProperty()
{
  return Dali::Ui::InputField::Property::TEXT;
}

int CursorPositionProperty()
{
  return Dali::Ui::InputField::Property::CURSOR_POSITION;
}

int SelectedTextProperty()
{
  return Dali::Ui::InputField::Property::SELECTED_TEXT;
}
```

Password configuration is represented by `Dali::Ui::InputField::Property::PASSWORD_MODE`, `Dali::Ui::InputField::Property::PASSWORD_MASK_CHARACTER`, and `Dali::Ui::InputField::Property::PASSWORD_REVEAL_DURATION`. The typed read APIs are usually clearer in application code:

```cpp
void ReadPasswordConfiguration(Dali::Ui::InputField field)
{
  Dali::Ui::Text::PasswordMode mode = field.GetPasswordMode();
  uint32_t maskCharacter = field.GetPasswordMaskCharacter();
  uint32_t revealDuration = field.GetPasswordRevealDuration();

  (void)mode;
  (void)maskCharacter;
  (void)revealDuration;
}
```
