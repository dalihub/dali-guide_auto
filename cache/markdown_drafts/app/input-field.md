# Overview

The `InputField` component is a versatile text-entry control that provides users with a standardized interface for interacting with application data through keyboard or gesture-based input. It is designed for single-line text entry scenarios, offering robust control over input validation, visual presentation, and interaction state.

```cpp
InputField inputField = InputField::New();
inputField.SetText("Initial Text Content");
```

## Visual Styling and Typography

Customize the look of your input field by configuring font properties, text colors, outline styles, and dynamic scaling to match your application's design language. These configurations ensure consistent branding and readability across different screen densities.

The typography can be defined by setting the font family via `SetFontFamily(const Dali::String &)`, the size via `SetFontSize(float)`, and the weight, width, or slant using `SetFontWeight(Text::FontWeight)`, `SetFontWidth(Text::FontWidth)`, and `SetFontSlant(Text::FontSlant)` respectively. The text color is applied using `SetTextColor(const UiColor &)`. Advanced rendering styles, such as shadows, outlines, underlines, and strike-through effects, are managed through `SetShadow(const Text::Shadow &)`, `SetOutline(const Text::Outline &)`, `SetUnderline(const Text::Underline &)`, and `SetLineThrough(const Text::LineThrough &)`.

Accessibility is supported by scaling font sizes relative to system settings. You can enable this feature with `SetSystemFontSizeScaleEnabled(bool)` and define the bounds of this scaling using `SetMinimumFontSizeScale(float)` and `SetMaximumFontSizeScale(float)`.

```cpp
InputField inputField = InputField::New();
inputField.SetFontFamily("Arial");
inputField.SetFontSize(24.0f);
inputField.SetTextColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));
inputField.SetOutline(Text::Outline(UiColor(0.0f, 0.0f, 0.0f, 1.0f), 1.0f));
```

## Placeholder and State Configuration

Manage user guidance and focus states by setting custom placeholder text and defining how the component responds visually when it is active or inactive. The placeholder provides context to the user regarding the expected input before any interaction occurs.

The placeholder text is assigned using `SetPlaceholder(const Dali::String &)` and its color is set via `SetPlaceholderColor(const UiColor &)`. By default, the placeholder may hide upon focus, but you can override this behavior using `SetShowPlaceholderOnFocus(bool)` to keep the hint visible as needed.

```cpp
InputField inputField = InputField::New();
inputField.SetPlaceholder("Enter your name...");
inputField.SetPlaceholderColor(UiColor(0.5f, 0.5f, 0.5f, 1.0f));
```

## Input Interaction and Editing Control

Define the boundaries of user interaction by setting constraints such as maximum character length, password masking, and toggling editability. These controls ensure that the data entered meets the specific requirements of your application logic.

The edit state of the component is toggled using `SetEditable(bool)`. To prevent excessive input, the total character count is constrained using `SetMaximumLength(int)`. Security-sensitive fields are supported through `SetPasswordMode(Text::PasswordMode)`, which allows masking of characters. The mask character itself is set via `SetPasswordMaskCharacter(uint32_t)`, and you can define how long a character remains visible before being masked using `SetPasswordRevealDuration(uint32_t)`. For advanced validation, custom input filters can be applied using `SetInputFilter(const Text::InputFilter &)`.

```cpp
InputField inputField = InputField::New();
inputField.SetPasswordMode(Text::PasswordMode::HIDE_ALL);
inputField.SetPasswordMaskCharacter('*');
inputField.SetMaximumLength(8);
```

## Cursor and Selection Mechanics

Configure the behavior and appearance of the text cursor and selection handles to ensure a precise and accessible text editing experience. These settings allow for fine-grained control over how users navigate and highlight text within the field.

The cursor's appearance is controlled by adjusting its width via `SetCursorWidth(int)` and its color via `SetCursorColor(const UiColor &)`. Cursor visibility and animation are managed using `SetCursorBlinkEnabled(bool)` and `SetCursorBlinkInterval(float)`. To manage text selection, you can enable or disable selection capabilities with `SetSelectionEnabled(bool)` and define the highlight color using `SetSelectionColor(const UiColor &)`. Editing handles are further customized using various image setters, such as `SetCursorHandleImage(const Dali::String &)` and `SetSelectionHandleImageLeft(const Dali::String &)`.

```cpp
InputField inputField = InputField::New();
inputField.SetCursorColor(UiColor(0.0f, 0.0f, 1.0f, 1.0f));
inputField.SetSelectionEnabled(true);
inputField.SetSelectionColor(UiColor(0.0f, 0.0f, 1.0f, 0.3f));
inputField.SetCursorHandleImage("cursor_handle.png");
inputField.SetSelectionHandleImageLeft("selection_left.png");
```

## Handling User Input Events

Monitor and react to user interaction through dedicated signals that notify your application of typing style changes, text selection, or rejected input attempts. These signals provide hooks for real-time validation, UI updates, and logging.

Applications can listen to text changes through `TextChangedSignal()`, or detect when the maximum character limit is hit using `MaximumLengthReachedSignal()`. Input filtering rejections are reported via `InputRejectedSignal()`. Additionally, navigation and selection events are tracked by observing `CursorPositionChangedSignal()`, `SelectionStartedSignal()`, `SelectionChangedSignal()`, and `SelectionClearedSignal()`.

```cpp
class MyHandler {
public:
  void OnTextChanged(View view) {
    InputField field = InputField::DownCast(view);
    // validation logic here
  }
};
MyHandler handler;
InputField inputField = InputField::New();
inputField.TextChangedSignal().Connect(&handler, &MyHandler::OnTextChanged);
```

## Retrieving and Manipulating Text

Access the current state of the InputField by programmatically retrieving raw text content, selected segments, or metadata about the current input layout. This ensures that the application remains synchronized with the user's input.

The primary content is retrieved using `GetText()`. You can programmatically update the text with `SetText(const Dali::String &)`. When working with user selections, the current selection range is queried using `GetSelectedTextStart()` and `GetSelectedTextEnd()`, while the content itself is fetched via `GetSelectedText()`. You can also force a selection programmatically using `SelectText(uint32_t, uint32_t)` or `SelectWholeText()`.

```cpp
InputField inputField = InputField::New();
inputField.SetText("Hello DALi");
Dali::String currentText = inputField.GetText();
inputField.SelectWholeText();
```