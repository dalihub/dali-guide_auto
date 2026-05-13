# Ui Scale Manager Prose Review

Changed prose:
- Reworded the overview to say `Dali::Ui::UiScaleManager` gives access to the shared system UI scale used by `Dali::Ui::View` layout roots during measurement.
- Changed “process-wide scale manager” to “shared scale manager” to stay closer to the public singleton API wording.
- Changed layout-root guidance from “does not need to manage” to “should not call the internal layout-root registration APIs directly.”
- Clarified that `SetScale()` invalidates registered layout roots and allows `Dali::Ui::View`-based layouts to be measured again.
- Changed “authoritative dali-ui scale source” to “shared dali-ui scale source” to avoid overclaiming beyond the handle singleton behavior.
- Corrected validation wording: the implementation ignores `NaN`, zero, and negative values. It does not explicitly reject positive infinity.
- Added a sentence recommending an application-level range for user-entered values.
- Added “copy it” to the handle paragraph because the public header exposes the default copy constructor and copy assignment.
- Added `<utility>` to the move example so `std::move` is explicitly available.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-scale-manager.h` documents `UiScaleManager` as a handle-based singleton with `Get()`, `GetScale()`, `SetScale()`, and `DownCast()`.
- `repos/dali-ui/dali-ui-foundation/internal/ui-scale-manager-impl.h` initializes `mScale` to `1.0f`.
- `repos/dali-ui/dali-ui-foundation/internal/ui-scale-manager-impl.cpp` creates the singleton on first `Get()`, returns `mScale` from `GetScale()`, ignores `std::isnan(scale)` and `scale <= 0.0f`, then resets effective scale and invalidates registered roots when the scale changes.
- `repos/dali-ui/dali-ui-foundation/public-api/view-impl.cpp` uses `UiScaleManager::Get().GetScale()` for root/inherited effective scale and registers/unregisters layout roots internally.
- `repos/dali-ui/samples/ui-scale/ui-scale-example.cpp` uses `UiScaleManager::Get().SetScale()` and `UiScaleManager::Get().GetScale()` in an application-facing scale control flow.

Remaining concerns:
- The guide keeps simple standalone snippets and does not show a full `Dali::Ui::View` tree because the existing section structure and code blocks were preserved.
- The public comment says `SetScale()` is called by system/vconf handlers, while the sample also uses it from UI controls. The guide therefore presents application calls as valid for app-controlled scale settings without inventing additional APIs.
