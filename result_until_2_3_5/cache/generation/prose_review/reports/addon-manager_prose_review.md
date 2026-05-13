# Addon Manager Prose Review

Changed prose:
- Replaced “dali-ui application” with “DALi application” in the overview because `AddOnManager` is declared in `dali-core` integration API and described as used by applications, DALi lifecycle handling, and add-on self-registration.
- Clarified that `Dali::Integration::AddOnManager` is “created by the DALi adaptor and exposed as a singleton,” matching the public header comment.
- Added that the constructor is protected, which supports the guidance not to create `Dali::Integration::AddOnManager` directly.
- Changed “obtain an add-on handle” to “obtain an opaque add-on handle,” matching the `AddOnLibrary` typedef documentation.
- Corrected `Dali::Integration::AddOnManager::InvokeGlobalProc()` prose from “load and invoke” to “retrieve and invoke ... from an existing add-on handle.” The template calls `GetGlobalProc<R(Args...)>(addOnLibrary, functionName)(args...)`; it does not load the add-on.
- Adjusted lifecycle prose to say lifecycle methods forward events “through add-on lifecycle callbacks,” avoiding the narrower claim that only loaded add-ons with registered callbacks are involved.
- Changed “The table owns” to “The table contains” for `Dali::AddOnDispatchTable` function pointers, because the public struct stores function pointers but does not define ownership semantics.

Source evidence:
- `repos/dali-core/dali/integration-api/addon-manager.h` declares `Dali::Integration::AddOnManager`, protected constructor, static `Get()`, singleton `mSingleton`, `EnumerateAddOns()`, `GetAddOnInfo()`, `LoadAddOns()`, `LoadAddOn()`, `GetAddOn()`, `GetGlobalProc()`, `GetInstanceProc()`, `InvokeGlobalProc()`, lifecycle methods, and `RegisterAddOnDispatchTable()`.
- `repos/dali-core/dali/integration-api/addon-manager.h` documents `AddOnLibrary` as a fully opaque object.
- `repos/dali-core/dali/integration-api/addon-manager.h` defines `Dali::AddOnInfo`, nested `BuildInfo`, and `Dali::AddOnDispatchTable` fields used by the examples.
- `repos/dali-adaptor/dali/internal/addons/linux/addon-manager-impl-linux.cpp` shows enumeration, metadata lookup, load-by-name, load-by-library, global/instance proc lookup, lifecycle invocation, and dispatch-table registration behavior.

Remaining concerns:
- The feature is integration-level and comes from `dali-core`/adaptor rather than a typical `dali-ui` view API, so the guide necessarily uses `Dali::Integration::AddOnManager` rather than `Dali::Ui::View`.
- The example function names such as `DoSum`, `Initialize`, `CreateInstance`, and `StringLen` are illustrative add-on exports; they are not DALi APIs.
- The `InvokeGlobalProc()` helper does not let callers check for a missing function before invocation, so the guide keeps the existing warning to prefer `GetGlobalProc()` when absence is possible.
