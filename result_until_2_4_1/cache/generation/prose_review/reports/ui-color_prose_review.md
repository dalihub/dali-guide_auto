# Ui Color Prose Review

Changed prose:
- Narrowed the overview from "colors for dali-ui views" to "dali-ui colors" because `Dali::Ui::UiColor` is a general color value used by multiple dali-ui APIs, while the guide still focuses on `Dali::Ui::View` examples.
- Clarified `Dali::Ui::View::SetBackgroundColor` behavior: ID colors are resolved from the current theme and registered for theme-refresh binding; direct RGBA colors are applied immediately and clear the previous ID binding.
- Added one sentence noting the public `Dali::Vector4`, string color ID, and `0xRRGGBB` constructors.
- Added the fallback behavior for `Dali::Ui::UiColor::GetRgba` when an ID cannot be found.
- Kept all code blocks unchanged because they match the public API and sample usage.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color.h` declares `Dali::Ui::UiColor`, predefined `PRIMARY`, `BACKGROUND`, and `OUTLINE`, RGBA/string/`Dali::Vector4`/hex constructors, `HasColorId`, `GetColorId`, `GetRgba`, `operator Vector4`, `WithAlpha`, and `ScaleAlpha`.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color.cpp` defines predefined IDs as `"Primary"`, `"Background"`, and `"Outline"`, resolves ID colors through `UiColorManager`, returns `Vector4::ZERO` when a token cannot be resolved, and clamps alpha in `WithAlpha` and `ScaleAlpha`.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` documents that `SetBackgroundColor` registers a theme-aware binding for ID colors and removes the previous ID-based binding for direct RGBA colors.
- `repos/dali-ui/dali-ui-foundation/public-api/view.cpp` confirms `SetBackgroundColor` returns `View&` for fluent chaining.
- `repos/dali-ui/dali-ui-foundation/public-api/view-impl.h` confirms the binding path: `RegisterBinding`/`SetBindingColor` for ID colors, `ClearBinding` for direct colors, then immediate setter application.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UiColor.cpp` verifies default/RGBA/string/hex construction, predefined IDs, alpha clamping, preservation of color IDs after alpha changes, and non-mutating alpha helpers.
- `repos/dali-ui/samples/color-controls/color-controls-example.cpp` shows app-facing `Dali::Ui::View`/`Layout` usage with `SetBackgroundColor(UiColor::BACKGROUND)`, `SetBackgroundColor(UiColor::PRIMARY)`, and custom string IDs.

Remaining concerns:
- The guide intentionally remains focused on `Dali::Ui::View::SetBackgroundColor`; it does not expand into every API that accepts `Dali::Ui::UiColor`.
- Custom string color IDs are mentioned through constructor coverage and sample evidence, but no new custom-ID example was added to avoid restructuring the draft.
