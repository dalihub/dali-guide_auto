# Text Prose Review

Changed prose:
- Tightened the `FitRange` description to say text fit finds the largest fitting size in the available layout space.
- Reworded candidate-based fitting to describe explicit font-size and line-height pairs, avoiding an unsupported design-token claim.
- Clarified `FontVariation::FromString()` failure behavior.
- Added that `InputFilter` patterns use `std::regex` ECMAScript grammar.
- Changed the property-index guidance to prefer typed `Dali::Ui::InputField` APIs.
- Reworded the subtle bevel guidance to avoid an absolute “only when” rule.

Changed code examples:
- Replaced `Text::InputFieldPropertyIndex` variable types with `Property::Index` for enum values from `Dali::Ui::Text::InputFieldPropertyIndex`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/text/fit/text-fit-range.h` documents range fitting as selecting the largest font size within the range and searching by font-size step.
- `repos/dali-ui/dali-ui-foundation/public-api/text/fit/text-fit-candidate.h` documents each candidate as a font size and absolute line height, with candidate fitting selecting the largest candidate that fits.
- `repos/dali-ui/dali-ui-foundation/public-api/text/font-variation/font-variation.h` documents supported string formats, canonical `ToString()` output, strict parsing, and empty-vector failure behavior.
- `repos/dali-ui/dali-ui-foundation/public-api/text/input-filter.h` documents allow and deny behavior, rejection reasons, and `std::regex` ECMAScript grammar.
- `repos/dali-ui/dali-ui-foundation/public-api/input-field.h` documents `SetInputFilter()`, `ClearInputFilter()`, and typed APIs corresponding to `InputFieldPropertyIndex`.
- `repos/dali-ui/samples/text/text-style-bevel-example.cpp`, `repos/dali-ui/samples/text/text-fit-example.cpp`, `repos/dali-ui/samples/text/text-fit-candidate-example.cpp`, and `repos/dali-ui/samples/text/text-input-field-example.cpp` confirm the guide’s app-facing usage patterns.

Remaining concerns:
- The guide still focuses on value objects and property indexes rather than a fuller `Label` or `InputField` workflow, but this matches the provided feature surface and the localized-edit constraint.
