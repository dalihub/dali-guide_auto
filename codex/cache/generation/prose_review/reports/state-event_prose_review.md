# State Event Prose Review

Changed prose:
- Clarified the opening description from “optional input event” to “input event information for transitions that originated from input,” matching the API where `GetCause()` always returns a valid `InputEvent`, with `InputEventType::NONE` for non-input changes.
- Replaced “from a state change signal” with the public signal name `Dali::Ui::View::StateChangedSignal`.
- Replaced “framework behavior” with “framework-triggered lifecycle changes,” matching `InputEventType::NONE` documentation.
- Reworded `GetInputEventType` as a convenience shortcut for `GetCause().GetInputEventType()`, matching the public header and implementation.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.h` documents `StateEvent` as a `BaseHandle`, provides `GetPrev`, `GetCurrent`, `Added`, `Removed`, `Changed`, `GetCause`, `GetInputEventType`, and typed input accessors.
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.cpp` implements `GetInputEventType()` as `GetCause().GetInputEventType()`.
- `repos/dali-ui/dali-ui-foundation/internal/state-event-impl.cpp` returns `InputEvent::None()` when no stored cause exists.
- `repos/dali-ui/dali-ui-foundation/public-api/input-event-type.h` defines `NONE` as non-concrete input, including explicit API calls and framework-triggered lifecycle changes.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` defines `StateChangedSignalType = Signal<void(View, StateEvent)>`.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ViewState.cpp` verifies signal callbacks receive previous/current snapshots and that `Added` works in state-change handlers.

Remaining concerns:
- Code blocks were preserved. The snippets rely on surrounding application declarations and normal DALi headers for helper functions and `Dali::ConnectionTracker`.
