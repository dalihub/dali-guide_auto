# Ui Color Manager Prose Review

Changed prose:
- Clarified that `UiColorManager` supports color lookup overrides, matching `ColorOverrideFunc` behavior.
- Added that `GetColor(StringView)` returns `Dali::Vector4::ZERO` when a color id is not found.
- Revised `UiColor` wording to say it stores either direct RGBA values or theme color identifiers.
- Corrected binding prose: `RegisterBinding()` registers the callback, `SetBindingColor()` stores the `UiColor`, and callbacks are not invoked immediately.
- Added binding guidance that theme-refreshable bindings should use a `UiColor` constructed from a color id.
- Added that setting or clearing a color override refreshes existing view bindings.
- Clarified `InvalidateCache(const UiColor&)`: use it for token-backed `UiColor`; direct RGBA colors are a no-op.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color-manager.h` documents `UiColorManager::Get()`, `DownCast()`, both `GetColor()` overloads, binding APIs, `SetColorOverride()`, `ClearColorOverride()`, and both `InvalidateCache()` overloads.
- `repos/dali-ui/dali-ui-foundation/internal/ui-color-manager-impl.cpp` shows `GetColor(StringView)` returns `Vector4::ZERO` when lookup fails, overrides are checked before theme lookup, `SetColorOverride()` and `ClearColorOverride()` invalidate cache and refresh bindings, and binding callbacks are invoked during refresh only for token-backed `UiColor` values.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color.h` documents `UiColor` constructors for direct RGBA values and string color identifiers.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UiColorManager.cpp` confirms singleton behavior, default constructor state, default color lookup, not-found lookup, binding storage and removal, override fallback, override clearing, and override-triggered binding refresh.

Remaining concerns:
- The draft keeps generic binding examples that receive `const Dali::Ui::UiColor& color`; callers should pass a color-id-backed `UiColor` for refreshable bindings.
- The guide does not show the `bool GetColor(StringView, Vector4&)` overload, although it is public and useful when callers need to distinguish not-found from a valid zero color.
