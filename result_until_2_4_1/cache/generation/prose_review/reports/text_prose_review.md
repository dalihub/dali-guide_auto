# Text Prose Review

Changed prose:
- Replaced "owned text style/configuration/rule object" with "value type" for `Dali::Ui::Text::Bevel`, `Dali::Ui::Text::FitRange`, `Dali::Ui::Text::FitCandidate`, and `Dali::Ui::Text::InputFilter`.
- Grounded bevel application in `Dali::Ui::Label`, which exposes `SetBevel` and `ClearBevel`.
- Tightened `FitRange` and `FitCandidate` wording to match `Dali::Ui::Label::SetTextFit`: range-based fitting searches for the largest fitting font size, and candidate-based fitting selects the largest fitting candidate.
- Adjusted font variation serialization wording to avoid overclaiming that every axis serializes as plain `tag=value`; `ToString` uses that canonical form for normal four-letter tags.
- Replaced "owns the property index constants" with "defines the property index constants" for `Dali::Ui::Text::InputFieldPropertyIndex`.
- Added that `InputFilter` patterns use `std::regex` ECMAScript grammar and clarified that `InputField::SetInputFilter` applies to inserted input, not direct `SetText`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/text/style/bevel.h` documents `Bevel` as a value-type object and its typed setters/getters.
- `repos/dali-ui/samples/text/text-style-bevel-example.cpp` applies `Text::Bevel` through `Label::SetBevel` and uses the top-left light direction examples.
- `repos/dali-ui/dali-ui-foundation/public-api/text/fit/text-fit-range.h`, `text-fit-candidate.h`, and `label.h` describe range and candidate text fitting behavior.
- `repos/dali-ui/dali-ui-foundation/public-api/text/font-variation/font-variation.h` and `font-variation.cpp` define `FromString`, `ToString`, supported formats, duplicate-tag behavior, and serialization.
- `repos/dali-ui/dali-ui-foundation/public-api/text/input-field-properties.h` defines the listed `InputFieldPropertyIndex` constants.
- `repos/dali-ui/dali-ui-foundation/public-api/text/input-filter.h` and `input-field.h` define allow/deny patterns, regex grammar, `SetInputFilter`, and the direct-`SetText` exception.

Remaining concerns:
- The code examples are illustrative snippets and were preserved as requested; they were checked against public API signatures but not compiled in this review pass.
