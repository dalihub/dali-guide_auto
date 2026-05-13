# State Event Prose Review

Changed prose:
- Clarified the opening sentence so the input cause is described as present for concrete input-originated transitions, while non-input transitions use `InputEventType::NONE`.
- Changed callback prose from a generic “callback parameter” to `Dali::Ui::View::StateChangedSignal` delivery.
- Updated all example handler signatures from `const Dali::Ui::StateEvent&` to `Dali::Ui::StateEvent` to match the public signal type.
- Tightened the input-cause paragraph to mention `Dali::Ui::InputEventType::NONE` explicitly for programmatic or framework state changes.
- Revised the final re-entrant notification paragraph to say DALi finishes the current state-change batch before delivering the next transition.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.h`: documents `StateEvent`, `GetPrev`, `GetCurrent`, `Added`, `Removed`, `Changed`, `GetCause`, `GetInputEventType`, and typed getter preconditions.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: defines `StateChangedSignalType = Signal<void(View, StateEvent)>` and shows `StateChangedSignal().Connect(..., [](View v, StateEvent e) { ... })`.
- `repos/dali-ui/dali-ui-foundation/public-api/input-event-type.h`: defines `NONE`, concrete input event types, and `RESERVED`.
- `repos/dali-ui/dali-ui-foundation/internal/views/view-state-manager.cpp`: queues re-entrant state changes and emits pending batches in order.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ViewState.cpp`: verifies previous/current snapshots, no dispatch for unchanged state, and deferred notification ordering.

Remaining concerns:
- The draft examples use placeholder application helpers such as `ShowFocusDecoration()` and `QueueStateRecord()`. They are intentionally illustrative and are not DALi public APIs.
- No build or snippet compilation was run; this was a source-grounded prose and example-signature review only.
