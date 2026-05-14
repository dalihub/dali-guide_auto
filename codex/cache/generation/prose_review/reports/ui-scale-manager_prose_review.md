# Ui Scale Manager Prose Review

Changed prose:
- Replaced "stores the application-wide UI scale" with "provides access to the shared UI scale" to match the public header's handle-based singleton wording.
- Replaced "process-wide scale manager" with "singleton scale manager" because the public API documents `Dali::Ui::UiScaleManager::Get` as returning a singleton instance.
- Tightened the layout invalidation sentence to say the affected view tree is measured again, matching `UiScaleManagerImpl::SetScale`, which resets effective scale recursively and invalidates registered layout roots.
- Corrected the validation paragraph. The guide now recommends positive finite values, but states the implementation specifically logs and ignores `NaN` and non-positive values.
- Reworded per-view guidance from "per-view scale values" to "separate layout scale values to each view" to avoid confusing `UiScaleManager` with `Dali::Ui::View::SetScale`.
- Replaced the mechanical destructor/move-operation sentence with a handle-type explanation that includes copy and move support without implying application code should call special member functions directly.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-scale-manager.h` documents `UiScaleManager` as a handle-based singleton, with `Get`, `DownCast`, `GetScale`, and `SetScale`.
- `repos/dali-ui/dali-ui-foundation/internal/ui-scale-manager-impl.cpp` initializes the scale through `mScale`, returns it from `GetScale`, rejects `NaN` and non-positive values in `SetScale`, and invalidates registered layout roots after a scale change.
- `repos/dali-ui/dali-ui-foundation/public-api/view-impl.cpp` registers and unregisters layout roots with `UiScaleManager`, inherits root scale from `UiScaleManager::Get().GetScale()`, and resets effective scale recursively for re-measure.
- `repos/dali-ui/samples/ui-scale/ui-scale-example.cpp` shows application-facing usage of `UiScaleManager::Get().SetScale(...)` and `UiScaleManager::Get().GetScale()` with preset and custom scale controls.

Remaining concerns:
- The implementation error message says the scale must be a positive finite number, but the current code checks `std::isnan(scale)` and `scale <= 0.0f`; it does not explicitly reject positive infinity. The guide avoids claiming full finite-value enforcement while still recommending finite input for application code.
- The draft includes handle move examples. They are API-valid, but they are less central for app developers than `Get`, `SetScale`, and `GetScale`.
