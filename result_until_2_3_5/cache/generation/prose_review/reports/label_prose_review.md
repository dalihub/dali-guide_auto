# Label Prose Review

Changed prose:
- Clarified that `SetLineHeight` is interpreted by `SetLineHeightMode`.
- Replaced "stringly typed" with "string-based" for clearer guide tone.
- Expanded `GetAdjustedFontSizeScale` wording to include system font size scale when enabled.
- Updated anchor prose to state that `AnchorClickedSignal` applies to anchors in markup-enabled text.
- Added theme color identifiers to the `UiColor` construction description.
- Clarified async rendering behavior as automatic requests during relayout as needed.
- Added the `SetRenderScale` constraint that the value must be `1.0f` or greater and that it is valid with async rendering.

Changed code:
- Updated the anchor signal example to use `<a href='...'>` markup and `SetMarkupEnabled(true)`, because a plain text label would not demonstrate anchor activation.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/label.h` defines `Dali::Ui::Label` as a `Dali::Ui::View`, `Label::New`, typed setters returning `Label&`, getters, property constants, async APIs, and signal signatures.
- `repos/dali-ui/dali-ui-foundation/public-api/label.h` documents `SetLineHeight`, `SetLineHeightMode`, `SetLayoutDirectionMode`, `SetAsyncRendering`, `SetRenderScale`, `GetAsyncLineCount`, and `GetAdjustedFontSizeScale`.
- `repos/dali-ui/dali-ui-foundation/public-api/text/text-enumerations.h` confirms `Alignment`, `LayoutDirectionMode`, `LineHeightMode`, `FontWeight`, `FontWidth`, and `FontSlant` values used in the examples.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color.h` confirms `UiColor` supports RGBA components, `Vector4`, string color identifiers, and hex RGB construction.
- `repos/dali-ui/samples/text/text-markup-example.cpp` shows anchor markup with `SetMarkupEnabled(true)`, anchor colors, and `AnchorClickedSignal`.
- `repos/dali-ui/samples/text/text-example.cpp` shows async render, async natural size, and async height-for-width signal usage.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-Label.cpp` confirms line-height mode, layout direction mode, anchor color, render scale, and property constant behavior.

Remaining concerns:
- The guide keeps the original section structure and does not add marquee, text fit, or style-effect sections, even though those APIs exist, because this pass requested localized prose review rather than a full rewrite.
