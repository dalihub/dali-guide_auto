# Addon Manager Prose Review

Changed prose:
- Clarified that `EnumerateAddOns()` returns names that can be passed to other manager methods, instead of implying a stronger cross-method guarantee.
- Changed `Dali::AddOnInfo` wording from "owned by" to "stored in" and added `Dali::AddOnInfo::type` to the metadata list and practical field guidance.
- Clarified that `GetAddOn()` returns an opaque `Dali::AddOnLibrary` handle.
- Added a caution that `InvokeGlobalProc()` should be used only when the procedure is known to exist, because the template invokes the returned function directly.
- Changed the `LoadOptionalAddOns()` example return type from `std::vector<void*>` to `std::vector<Dali::AddOnLibrary>`.
- Changed `Dali::AddOnDispatchTable` wording from "owned fields" to "fields".

Source evidence:
- `repos/dali-core/dali/integration-api/addon-manager.h` defines `Dali::AddOnInfo` with `type`, `next`, `name`, `version`, and `buildInfo`, and defines `Dali::AddOnLibrary` as an opaque `void*` typedef.
- `repos/dali-core/dali/integration-api/addon-manager.h` declares `EnumerateAddOns()`, `GetAddOnInfo()`, `LoadAddOns()`, `LoadAddOn()`, `GetAddOn()`, `GetGlobalProc()`, `GetInstanceProc()`, `InvokeGlobalProc()`, lifecycle methods, and `RegisterAddOnDispatchTable()`.
- `repos/dali-core/dali/integration-api/addon-manager.h` implements `InvokeGlobalProc()` by immediately calling `GetGlobalProc<R(Args...)>(...)(args...)`, so missing procedures are not checked by that helper.
- `repos/dali-adaptor/dali/internal/addons/linux/addon-manager-impl-linux.cpp` shows enumeration, metadata lookup, loading, global and instance procedure lookup, and lifecycle callback forwarding through the platform implementation.
- `repos/dali-adaptor/automated-tests/src/dali-adaptor-internal/utc-Dali-AddOns.cpp` verifies `EnumerateAddOns()`, `GetAddOnInfo()`, `GetAddOn()`, typed `GetGlobalProc()`, typed `GetInstanceProc()`, and lifecycle calls.
- `repos/dali-core/automated-tests/src/dali/test-sample-addon.cpp` provides the `SampleAddOn` and `DoSum` symbols used by the examples.

Remaining concerns:
- The feature is an integration API from `dali-core`, not a `dali-ui` View API. The guide keeps the dali-ui framing by limiting Addon Manager use to platform integration and keeping UI examples View-oriented, but the actual API surface remains `Dali::Integration::AddOnManager`.
- `RegisterDispatchTable()` is primarily an add-on or integration-layer operation, not a typical application UI operation. It remains in the lifecycle section because the draft already included it and it is part of the public contract.
