# Ui Color Manager Prose Review

Changed prose:
- Narrowed the overview from "bind theme-aware colors" to keeping identifier-based `Dali::Ui::UiColor` values attached to views, matching the binding model.
- Clarified `Dali::Ui::UiColorManager::GetColor` wording to distinguish the single-return-value overload from the bool/out-parameter overload.
- Changed binding guidance to say the app target should "usually" be a `Dali::Ui::View`, because the public API accepts `Dali::BaseHandle` while app-facing examples should remain view-based.
- Added that `Dali::Ui::UiColorManager::SetBindingColor` stores an identifier-based `Dali::Ui::UiColor`; this matches the public header note that the stored color must have a color ID for binding refresh.
- Added theme changes as a binding refresh source.
- Added that `Dali::Ui::UiColorManager::SetBindingColor` is a no-op when the binding does not exist.
- Added that replacing an override refreshes existing view bindings.
- Added that `Dali::Ui::UiColorManager::InvalidateCache` does not invoke binding callbacks by itself.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color-manager.h` documents `Dali::Ui::UiColorManager::Get`, `DownCast`, both `GetColor` overloads, binding registration, no immediate callback invocation, no-op `SetBindingColor` behavior without a binding, override refresh behavior, `nullptr` override clearing, and cache invalidation APIs.
- `repos/dali-ui/dali-ui-foundation/internal/ui-color-manager-impl.cpp` confirms override-first lookup, singleton creation, binding storage/replacement, no-op `SetBindingColor` for missing bindings, refresh on `SetColorOverride`, `ClearColorOverride`, and theme changes, and cache invalidation without binding refresh.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UiColorManager.cpp` confirms default construction is uninitialized, `Get` returns a singleton, missing colors return `Dali::Vector4::ZERO`, token bindings can be stored/replaced/cleared, overrides fall through or replace theme colors, `nullptr` clears the override, and override changes refresh bindings.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color.h` confirms `Dali::Ui::UiColor` can hold direct RGBA or a string identifier resolved through the current theme.

Remaining concerns:
- Code blocks were preserved as requested. The examples are consistent with the public headers, but they remain illustrative and do not show full application lifecycle setup.
- `Dali::Ui::UiColorManager::InvalidateCache(const Dali::Ui::UiColor&)` is documented publicly as specific-color invalidation; the current implementation invalidates all cached colors for identifier-based colors. The guide keeps the public contract wording and avoids exposing that implementation detail.
