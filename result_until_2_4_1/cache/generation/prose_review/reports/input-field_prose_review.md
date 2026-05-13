# Input Field Prose Review

Changed prose:
- Replaced "input-related notifications" with "input-related signals" to match the public signal API wording.
- Clarified that `Dali::Ui::InputField` is a `Dali::Ui::View`-derived handle.
- Added that cursor position is a character index in the current text.
- Added `GetText()` and `GetVerticalTextAlignment()` to the state-reading examples so the code matches the section scope and public API.
- Tightened `InputRejectedSignal` wording to reference `Dali::Ui::Text::InputFilter`, and clarified that `TypingStyleChangedSignal` is based on the current cursor position or selected text range.
- Added `TEXT_HANDLE_ENABLED` and `TEXT_HANDLE_COLOR` to the property constants table.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/input-field.h` defines `InputField : public View`, `InputField::New`, typed setters/getters, cursor position as a character index, password getters, selection getters, and all listed property constants.
- `repos/dali-ui/dali-ui-foundation/public-api/input-field.h` defines signal signatures: `Signal<void(View)>`, `Signal<void(View, uint32_t)>`, `Signal<void(View, uint32_t, uint32_t)>`, `Signal<void(View, Text::InputFilter::RejectReason)>`, and `Signal<void(View, Text::TypingStyle::Mask)>`.
- `repos/dali-ui/samples/text/text-input-field-example.cpp` shows app-facing usage with `InputField::New`, fluent setters, cursor/selection/password state reads, `SetInputFilter`, and signal connections.
- `repos/dali-ui/samples/text/text-scale-example.cpp` and `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-InputField.cpp` support the font scale and adjusted scale behavior.

Remaining concerns:
- Code blocks were preserved except for minimal additions to existing examples. They were checked against public headers for symbol availability, but no compile/test run was performed in this read-only review.
