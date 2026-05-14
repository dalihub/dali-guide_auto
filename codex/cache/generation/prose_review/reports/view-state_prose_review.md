# View State Prose Review

Changed prose:
- Clarified transition wording to anchor callbacks on public `Dali::Ui::View::StateChangedSignal()` and `Dali::Ui::StateEvent`, then compare previous/current `Dali::Ui::ViewState` values.
- Reworded the `Dali::ConnectionTracker` sentence from "signal-owning objects" to "objects that own signal connections" for accuracy.
- Tightened `Dali::Ui::ViewState::ToString()` wording to mention exact `Normal` and `All` output strings.

Source evidence:
- `dali-ui-foundation/public-api/view-state.h` defines `Dali::Ui::ViewState`, predefined values, `Contains()`, `HasIntersectionWith()`, `IsNormal()`, `IsAnyDisabled()`, transition helpers, and bitmask operators.
- `dali-ui-foundation/public-api/view-state.cpp` shows `Create("Normal")` returns zero, `Create("All")` returns `UINT32_MAX`, repeated names reuse the same registered bit, and `ToString()` returns `Normal`, `All`, or registered state names joined into a readable string.
- `dali-ui-foundation/public-api/view.h` exposes `StateChangedSignalType = Signal<void(View, StateEvent)>`, `GetState()`, and `StateChangedSignal()`.
- `dali-ui-foundation/public-api/state-event.h` exposes `GetPrev()`, `GetCurrent()`, `Added()`, `Removed()`, and `Changed()`.
- `automated-tests/src/dali-ui-foundation/utc-Dali-ViewState2.cpp` verifies default/normal behavior, predefined composites, custom state registration, operators, `ToString()`, and transition helper behavior.
- `automated-tests/src/dali-ui-foundation/utc-Dali-ViewState.cpp` and `utc-Dali-ViewStateHandler.cpp` verify state-change dispatch with `View`, `StateEvent`, and `ConnectionTracker`.

Remaining concerns:
- The `ViewStateObserver` example intentionally keeps comparison logic separate and does not show a full `StateChangedSignal()` connection. This matches the section's stated purpose, but a future example could show the full signal hookup if richer app-facing signal guidance is desired.
