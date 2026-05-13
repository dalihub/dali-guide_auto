# Ui Scale Manager Prose Review

Changed prose:
- Replaced "controls the application-wide UI scale" with "provides access to the system-driven UI scale" to match the public header wording.
- Changed "process-wide scale manager" to "singleton scale manager" because `Dali::Ui::UiScaleManager::Get` returns the singleton handle and creates it on first call.
- Tightened `SetScale` re-layout prose to say it resets effective-scale caches and invalidates measure state for registered layout roots, instead of the broader "schedules layout again".
- Clarified handle wording: `Dali::Ui::UiScaleManager` handles can be copied, passed, stored, or moved; moving transfers the handle value, not unique ownership.
- Added `<utility>` to the `std::move` example so the code block has the required standard header.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-scale-manager.h:40-43` describes `UiScaleManager` as access to the system-driven adaptive UI scale and a handle-based singleton that propagates changes to registered layout roots.
- `repos/dali-ui/dali-ui-foundation/public-api/ui-scale-manager.h:92-124` documents `Get`, `DownCast`, `GetScale`, and `SetScale`, including default scale `1.0` and re-layout behavior.
- `repos/dali-ui/dali-ui-foundation/internal/ui-scale-manager-impl.cpp:58-92` shows `SetScale` rejecting `NaN`, zero, and negative values, storing changed scale values, resetting effective scale recursively, and invalidating measure on each registered root.
- `repos/dali-ui/dali-ui-foundation/public-api/view-impl.cpp:1167-1192` shows root `INHERIT` scale resolving to `UiScaleManager::Get().GetScale()`.
- `repos/dali-ui/samples/ui-scale/ui-scale-example.cpp:149-178` shows sample app usage of `UiScaleManager::Get().SetScale(...)` and `GetScale()` for UI display.

Remaining concerns:
- The guide intentionally keeps `Dali::Ui::UiScaleManager::SetScale` as an application-facing example because samples use it directly, although the public header also notes it is called by system/vconf handlers.
- No repository edit was made because the workspace is read-only; the revised Markdown is provided in full above.
