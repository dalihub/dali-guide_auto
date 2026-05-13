# State Event Prose Review

Changed prose:
- Tightened the overview to say `Dali::Ui::StateEvent` describes a single `Dali::Ui::View` state transition.
- Replaced "owned event object" with "event handle" to match `StateEvent : public BaseHandle`.
- Reworded `Added()`, `Removed()`, and `Changed()` descriptions to match their exact `GetPrev()` / `GetCurrent()` logic.
- Narrowed `Changed()` guidance to single-state refresh code, avoiding an overbroad implication for composite state checks.
- Clarified that `GetCause()` returns a valid `Dali::Ui::InputEvent`, with `Dali::Ui::InputEventType::NONE` for non-input changes.
- Replaced "raw actor-level state handling" with "lower-level state handling" to avoid introducing an unsupported actor-state concept.
- Reworded the final passing guidance to describe `Dali::Ui::StateEvent` as a handle type and to keep transition-helper advice focused on a single state.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.h` documents `StateEvent` as a single view state transition passed to `StateChangedSignal` handlers.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` defines `StateChangedSignalType = Signal<void(View, StateEvent)>` and documents emission whenever the view's `ViewState` changes.
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.h` defines `Added()` as `!GetPrev().Contains(state) && GetCurrent().Contains(state)`, `Removed()` as the reverse, and `Changed()` as `Added(state) || Removed(state)`.
- `repos/dali-ui/dali-ui-foundation/public-api/view-state.h` documents `ViewState` as a bitmask that may contain multiple states and provides `Contains()`.
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.cpp` implements `GetInputEventType()` through `GetCause().GetInputEventType()` and forwards typed event accessors through `GetCause()`.
- `repos/dali-ui/dali-ui-foundation/internal/state-event-impl.cpp` returns `InputEvent::None()` when no cause handle is stored.
- `repos/dali-ui/dali-ui-foundation/public-api/input-event.h` documents `InputEvent::None()` and `InputEventType::NONE` for non-input changes.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ViewState.cpp` verifies `GetPrev()`, `GetCurrent()`, and `Added()` behavior during state dispatch.

Remaining concerns:
- The code examples were preserved, but they were not compiled in this review pass.
- The examples use placeholder application functions such as `ShowFocusFeedback()` and `ApplyItemPresentation()`; those remain illustrative rather than complete runnable samples.
