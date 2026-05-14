# Ui Color Prose Review

Changed prose:
- Reworded the overview from "views use" to "color setters use" because `Dali::Ui::UiColor` is accepted by `Dali::Ui::View` setters and other dali-ui color setter surfaces, not only by views as objects.
- Added `Dali::Ui::View::SetColor` to the theme binding paragraph so the prose matches the preceding code block, which uses `SetBackgroundColor`, `SetColor`, and `SetBorderlineColor`.
- Clarified that `WithAlpha` and `ScaleAlpha` clamp alpha to the `0.0f` to `1.0f` range.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color.h` documents `UiColor` as direct RGBA or string identifier resolved through the current theme, and declares `PRIMARY`, `BACKGROUND`, `OUTLINE`, `HasColorId`, `GetColorId`, `GetRgba`, `WithAlpha`, `ScaleAlpha`, and `operator Vector4`.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color.cpp` defines predefined token IDs as `"Primary"`, `"Background"`, and `"Outline"`, resolves token colors through `UiColorManager`, and clamps alpha in `WithAlpha`, `ScaleAlpha`, and token resolution.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` declares `SetBackgroundColor`, `SetColor`, and `SetBorderlineColor` overloads that accept `const UiColor&`.
- `repos/dali-ui/dali-ui-foundation/public-api/view-impl.h` and `view-impl.cpp` show `SetColorBinding` registering bindings when `UiColor::HasColorId()` is true and applying `color.GetRgba()` immediately.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UiColor.cpp` verifies direct RGBA behavior, token IDs, default alpha, token resolution, alpha preservation for token colors, chaining, clamping, and implicit `Vector4` conversion.

Remaining concerns:
- No remaining non-English prose was present in the draft.
- The guide intentionally keeps the original examples and structure; it does not add coverage for the public `Vector4`, string ID, or hex constructors because the requested review favored localized prose edits and preserving existing code blocks.
