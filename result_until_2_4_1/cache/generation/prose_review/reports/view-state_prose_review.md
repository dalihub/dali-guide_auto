# View State Prose Review

Changed prose:
- Clarified the overview from “the visual and interaction state” to “one or more visual and interaction states” to match `ViewState` as a bitmask value.
- Added a `Contains(ViewState::NORMAL)` caveat and directed normal-state checks to `IsNormal()`.
- Removed the phrase “state registry” from the reserved-name paragraph because the app-facing contract is that `"Normal"` and `"All"` are reserved names, not that applications should reason about the registry.
- Clarified `WasChanged()` wording so it does not imply composite masks are the same as “any bit changed”; kept `AnyChanged()` as the mask-oriented helper.
- Added a small `View::StateChangedSignal()` / `StateEvent` example for app-facing transition handling.
- Added a note that composite `ToString()` name order should not be relied on.
- Replaced the final `HasNoFocusedBit()` example with a direct `operator&` plus `operator bool` check.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/view-state.h`: `ViewState` is documented as a bitmask; exposes `NORMAL`, `ALL`, built-in states, composite states, `Create()`, `Contains()`, `HasIntersectionWith()`, `IsNormal()`, `IsAnyDisabled()`, transition helpers, and bitwise operators.
- `repos/dali-ui/dali-ui-foundation/public-api/view-state.cpp`: `"Normal"` maps to `0u`, `"All"` maps to `UINT32_MAX`, repeated names return the same bit, and `ToString()` builds composite names from registered state entries.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ViewState2.cpp`: verifies default construction, `NORMAL`, `ALL`, predefined combinations, `Contains(NORMAL)`, `HasIntersectionWith(NORMAL)`, operators, `ToString()`, and transition helpers.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: `View::GetState()` and `View::StateChangedSignal()` are public app-facing APIs; `SetEnabled(false)` adds disabled state and emits the state-changed signal.
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.h`: `StateEvent` exposes `GetPrev()`, `GetCurrent()`, `Added()`, `Removed()`, and `Changed()` for signal callbacks.

Remaining concerns:
- The guide still focuses on `ViewState` value handling rather than a full styling pipeline because no public theme/style application API was established from the supplied evidence.
- Custom-state lifetime and naming policy beyond same-name reuse and reserved `"Normal"` / `"All"` names were not expanded because the public API only documents registration and the 32-bit limit.
