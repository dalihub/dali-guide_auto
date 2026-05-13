# Input Field Prose Review

Changed prose:
- Clarified that `Dali::Ui::InputField` is a `Dali::Ui::View` in the view tree because the public header declares `class InputField : public View`.
- Removed the unsupported implication that `SetEditable(false)` guarantees selection behavior; the public API only states that it controls user editing.
- Reworded font scaling from "accessibility-aware sizing" to "font-scale-aware sizing" and added `SetSystemFontSizeScaleEnabled`, matching the public scale API.
- Changed cursor wording from "initial character index" to "character index" because `SetCursorPosition` sets the current cursor position.
- Tightened `Dali::Ui::InputField::Property` ownership wording to match the nested public `Property` struct.
- Clarified that `InputRejectedSignal` depends on an input filter set with `SetInputFilter`.

Changed code:
- Made the signal controller examples inherit `Dali::ConnectionTracker`, matching the repository samples that connect `this` to signal callbacks.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/input-field.h`: class inheritance, typed setters/getters, property constants, cursor APIs, font-scale APIs, input-filter APIs, and signal callback signatures.
- `repos/dali-ui/dali-ui-foundation/public-api/text/input-filter.h`: `Text::InputFilter::RejectReason::NOT_ALLOWED` and `DENIED`.
- `repos/dali-ui/samples/text/text-input-field-example.cpp`: app-facing `InputField` construction, cursor/selection/filter usage, signal connection style, and `ConnectionTracker` controller pattern.
- `repos/dali-ui/dali-ui-foundation/internal/text/controller/text-controller-text-updater.cpp`: input filter rejection emits `NOT_ALLOWED` and `DENIED` during inserted input processing.

Remaining concerns:
- The snippets are guide-sized examples, not standalone translation units; they assume the application has the usual DALi headers and signal infrastructure available.
