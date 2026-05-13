# Text Prose Review

Changed prose:
- Corrected the overview from "`Dali::Ui::Text` value objects" to "value objects and property indices in the `Dali::Ui::Text` namespace" because `Dali::Ui::Text` is a namespace, not a value object.
- Changed `FitRange` wording from "continuous range" to "stepped range" and clarified that text fitting uses `fontSizeStep`.
- Changed `FontVariation::FromString` wording to "supported variation string" and described `ToString` as returning canonical `wght=700,wdth=100` form.
- Changed input-filter grammar wording from generic ECMAScript regular-expression syntax to `std::regex` ECMAScript syntax.
- Added a localized sentence explaining combined allow and deny pattern behavior.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/text/style/bevel.h` defines `Dali::Ui::Text::Bevel` as a value-type object with `SetDirection`, `SetIntensity`, `SetLightColor`, `SetShadowColor`, and matching getters.
- `repos/dali-ui/dali-ui-foundation/public-api/text/fit/text-fit-range.h` states that text fit searches the configured range using the specified font-size step and that line height follows the current text style configuration.
- `repos/dali-ui/dali-ui-foundation/public-api/text/fit/text-fit-candidate.h` states that each candidate defines font size and absolute line height, and that fitting selects the largest candidate that fits.
- `repos/dali-ui/dali-ui-foundation/public-api/text/font-variation/font-variation.h` documents supported input formats, strict parsing, and canonical `wght=700,wdth=90` output.
- `repos/dali-ui/dali-ui-foundation/public-api/text/input-filter.h` documents allow and deny pattern behavior and `std::regex` ECMAScript grammar.
- `repos/dali-ui/dali-ui-foundation/public-api/text/input-field-properties.h` defines the referenced `InputFieldPropertyIndex` values.
- Samples under `repos/dali-ui/samples/text/` show `Label::SetBevel`, `Label::SetTextFit`, `Label::SetFontVariation`, and `InputField::SetInputFilter` used with the documented value objects.

Remaining concerns:
- The examples remain intentionally focused on value-object construction and inspection. They do not show full `Label` or `InputField` setup for every section, although repository samples confirm those application-facing attachment points.
- The allow-pattern example uses `[0-9]+`; exact match behavior depends on the input-filter application path in the control implementation, which was not fully traced beyond the public `InputFilter` contract in this pass.
