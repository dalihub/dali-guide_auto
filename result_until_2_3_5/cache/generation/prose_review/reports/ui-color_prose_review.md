# Ui Color Prose Review

Changed prose:
- Replaced “theme-linked color tokens” wording with “string-based color IDs” / “ID-based colors” to match `UiColor` public API terminology.
- Added a localized paragraph explaining that `View::SetBackgroundColor()` and `View::SetBorderlineColor()` register theme bindings for ID-based colors and apply direct RGBA values immediately.
- Clarified `ScaleAlpha()` behavior for ID-based colors: the color ID is preserved and alpha is applied during resolution.
- Clarified that `WithAlpha()` and `ScaleAlpha()` clamp alpha to `0.0f` through `1.0f`.
- Clarified `GetRgba()` fallback behavior for unresolved IDs as transparent black.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color.h` defines `Dali::Ui::UiColor`, predefined `PRIMARY`, `BACKGROUND`, `OUTLINE`, RGBA and string-ID constructors, `HasColorId()`, `GetColorId()`, `GetRgba()`, `operator Vector4()`, `ScaleAlpha()`, and `WithAlpha()`.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color.cpp` defines predefined IDs as `"Primary"`, `"Background"`, and `"Outline"`, resolves ID-based colors through `UiColorManager`, returns `Vector4::ZERO` for unresolved IDs, preserves IDs during alpha operations, and clamps alpha.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` documents that `View::SetBackgroundColor()` and `View::SetBorderlineColor()` resolve ID-based `UiColor` values from the current theme and register bindings.
- `repos/dali-ui/samples/color-controls/color-controls-example.cpp` shows app-facing use of `View::New()`, `SetBackgroundColor(UiColor::BACKGROUND)`, `SetBackgroundColor(UiColor::PRIMARY)`, and custom string color IDs.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UiColor.cpp` verifies default transparent black, RGBA default alpha, predefined ID strings, copy/assignment behavior, alpha clamping, unresolved-ID fallback, and `operator Vector4()`.

Remaining concerns:
- Code blocks were preserved because they match public fluent `Dali::Ui::View` APIs and the requested guide profile.
- The guide still focuses on the most app-facing `UiColor` usage and does not enumerate every constructor, such as `Vector4`, string ID, or `0xRRGGBB`, to avoid turning the guide into a mechanical API listing.
