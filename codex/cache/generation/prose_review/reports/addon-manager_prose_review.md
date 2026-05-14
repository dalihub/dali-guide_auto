# Addon Manager Prose Review

Changed prose:
- Replaced “a dali-ui application” with “DALi application integration code” in the overview because the feature is exposed through `dali-core` integration APIs, not a `Dali::Ui` view API.
- Changed “owns add-on discovery...” to “handles add-on discovery...” because `AddOnManager` is a singleton created by the adaptor and does not imply application ownership.
- Clarified that `Dali::Integration::AddOnManager::Get` returns an adaptor-created singleton; the constructor is protected in the public header.
- Changed `EnumerateAddOns` wording from “available” to “known to the manager” because enumeration may be populated from registered dispatch tables or environment-configured libraries.
- Narrowed `Dali::AddOnInfo::next` guidance to “only when metadata supplies an additional data structure” because the header defines it as a generic pointer to additional data structures.
- Clarified `LoadAddOns` as loading add-ons already known to the manager by name.
- Added a `LoadAddOn` null-handle guard to the `InvokeGlobalProc` example and clarified that `InvokeGlobalProc` should be used when the handle is valid and the procedure is expected to exist.
- Changed the dispatch-table section to address add-on or platform integration code, not ordinary UI view code, because the header documents `RegisterAddOnDispatchTable` as a function called by the add-on.

Source evidence:
- `repos/dali-core/dali/integration-api/addon-manager.h` declares `Dali::Integration::AddOnManager::Get`, pure virtual `EnumerateAddOns`, `GetAddOnInfo`, `LoadAddOns`, `LoadAddOn`, `GetGlobalProc`, `GetInstanceProc`, lifecycle methods, and `RegisterAddOnDispatchTable`.
- The same header documents `AddOnManager` as a singleton created by the adaptor and lists users as application, DALi lifecycle handling, and add-on self-registration.
- `repos/dali-core/dali/integration-api/addon-manager.h` defines `Dali::AddOnInfo`, `Dali::AddOnInfo::BuildInfo`, and `Dali::AddOnDispatchTable` fields used in the guide.
- `repos/dali-adaptor/dali/internal/addons/linux/addon-manager-impl-linux.cpp` shows enumeration from registered names and environment-configured libraries, loading by known names, `LoadAddOn` returning `nullptr` on failure, and lifecycle forwarding through registered or looked-up callbacks.
- `repos/dali-core/dali/devel-api/addons/addon-base.h` shows add-ons self-registering a `Dali::AddOnDispatchTable` through `Dali::Integration::AddOnManager::Get()->RegisterAddOnDispatchTable(&table)`.
- `repos/dali-adaptor/automated-tests/src/dali-adaptor-internal/utc-Dali-AddOns.cpp` verifies `EnumerateAddOns`, `GetAddOnInfo`, typed `GetGlobalProc`, typed `GetInstanceProc`, and lifecycle forwarding behavior.

Remaining concerns:
- The examples use illustrative add-on names such as `diagnostics`, `counter`, and `image-loader`; they demonstrate API shape but are not verified as shipped add-ons in the repository.
- `InvokeGlobalProc` does not provide its own missing-procedure guard before invocation, so application code should prefer `GetGlobalProc` when the procedure may be optional.
