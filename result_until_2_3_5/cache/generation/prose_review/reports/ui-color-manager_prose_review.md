# Ui Color Manager Prose Review

Changed prose:
- Clarified that `Dali::Ui::UiColorManager` covers theme color lookup and named bindings, matching the public header wording.
- Changed “empty handle” to “uninitialized handle” for default construction.
- Added `Dali::Ui::UiColorManager::GetColor` missing-color behavior: `Dali::Vector4::ZERO` for the value overload and a separate boolean overload.
- Adjusted binding prose to say the target is a handle, while app-facing examples should pass `Dali::Ui::View`.
- Clarified that `RegisterBinding` and `SetBindingColor` do not apply immediately, and that callbacks refresh on theme changes and when color overrides are set or cleared.
- Added that `Dali::Ui::View` color setters already use the binding mechanism for token-backed `Dali::Ui::UiColor`, so direct manager use is mainly for custom binding IDs or callbacks.
- Added that `SetBindingColor` is a no-op when the binding ID has not already been registered.
- Added that setting or clearing an override refreshes existing bindings immediately.
- Added the thread-safety caution for color override functions.
- Tightened cache prose: `InvalidateCache()` is useful when override behavior changes without replacing the override function, and the `UiColor` overload applies to token-backed colors while direct RGBA colors are a no-op.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-color-manager.h`: public API signatures and comments for `Get`, `DownCast`, `GetColor`, `RegisterBinding`, `SetBindingColor`, `SetColorOverride`, `ClearColorOverride`, and `InvalidateCache`.
- `repos/dali-ui/dali-ui-foundation/internal/ui-color-manager-impl.cpp`: confirms override-first lookup, `Vector4::ZERO` fallback, binding refresh on theme/override changes, `SetBindingColor` no-op for missing bindings, and cache invalidation behavior.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UiColorManager.cpp`: confirms singleton behavior, default constructor invalid handle, default colors, binding storage/replacement/clear, override fallback, immediate refresh on override set/clear, and `nullptr` clearing.
- `repos/dali-ui/dali-ui-foundation/public-api/view-impl.h` and `view-impl.cpp`: confirm `Dali::Ui::View` setters use internal color binding for token-backed `Dali::Ui::UiColor`.

Remaining concerns:
- The public header describes `InvalidateCache(const UiColor&)` as invalidating a specific token color, but the current implementation invalidates all cached colors for token-backed inputs. The guide avoids promising per-token-only behavior.
- The examples remain intentionally focused on public API shape and do not demonstrate every concrete `Dali::Ui::View` color setter.
