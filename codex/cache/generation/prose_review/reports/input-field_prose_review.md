# Input Field Prose Review

Changed prose and examples:
- Replaced the cursor blink read example with `Dali::Ui::InputField::IsCursorBlinkEnabled()` instead of inferring blink state from `GetCursorBlinkInterval()`.
- Clarified that `InputRejectedSignal` is for filtered input workflows after applying an input filter.
- Changed "`Dali::Ui::InputField::Property` owns" to "defines" the input-field property constants.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/input-field.h` defines `InputField : public View`, `InputField::New()`, chainable setters, `Property` constants, and cursor APIs including `IsCursorBlinkEnabled()`, `SetCursorBlinkInterval(float)`, `GetCursorBlinkInterval()`, `SetCursorWidth(int)`, `SetCursorColor(const UiColor&)`, and `SetCursorPosition(uint32_t)`.
- The same header documents `SetInputFilter`, `ClearInputFilter`, and `InputRejectedSignal()` with `Text::InputFilter::RejectReason`.
- The signal callback signatures in the draft match the public header: `TextChangedSignal`, `MaximumLengthReachedSignal`, `CursorPositionChangedSignal`, selection signals, `InputRejectedSignal`, and `TypingStyleChangedSignal` all use `Dali::Ui::View`.
- `repos/dali-ui/samples/text/text-input-field-example.cpp` confirms app-facing usage with `InputField::New()`, chainable setters, cursor and handle configuration, status reads, and signal connections.
- `repos/dali-ui/samples/text/text-typing-style-example.cpp` confirms `TypingStyleChangedSignal(View, Text::TypingStyle::Mask)` usage.

Remaining concerns:
- The guide examples are illustrative snippets and omit some includes needed for standalone compilation, such as `Dali::ConnectionTracker` and related dali-ui text type headers.
- No full compile was run in this read-only review pass.
