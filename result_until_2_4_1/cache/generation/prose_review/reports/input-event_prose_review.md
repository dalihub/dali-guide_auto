# Input Event Prose Review

Changed prose:
- Clarified the opening sentence from "carries the input cause" to "identifies the input or non-input cause" because `InputEventType::NONE` is part of the public model.
- Expanded the representation paragraph to list the public typed payloads: touch, key, tap, long-press, and wheel.
- Tightened the handle-copy sentence so it does not imply payload fields are exposed or manually copied by application code.
- Clarified `GetWheelEvent()` usage by naming the required `Dali::Ui::InputEventType::WHEEL_EVENT` condition.
- Added that typed getters assert when called for the wrong stored type.
- Clarified `InputEvent::None()` as a shared `Dali::Ui::InputEventType::NONE` event and noted no-argument `InputEvent::New()` for a separate `NONE` handle.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/input-event.h` declares `Dali::Ui::InputEvent`, `InputEvent::New()` overloads for `TouchEvent`, `KeyEvent`, `TapGesture`, `LongPressGesture`, and `WheelEvent`, `InputEvent::New()` with no arguments, `InputEvent::None()`, `GetInputEventType()`, and typed getters.
- `repos/dali-ui/dali-ui-foundation/public-api/input-event-type.h` defines `InputEventType::NONE`, `TOUCH_EVENT`, `KEY_EVENT`, `TAP_GESTURE`, `LONG_PRESS_GESTURE`, and `WHEEL_EVENT`, and documents `NONE` as programmatic or framework-triggered.
- `repos/dali-ui/dali-ui-foundation/public-api/input-event.cpp` implements `InputEvent::None()` as a static shared instance created by no-argument `InputEvent::New()`.
- `repos/dali-ui/dali-ui-foundation/internal/input-event-impl.cpp` stores the event type and payload and asserts the matching `InputEventType` in each typed getter.
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.h` documents state-event cause access and typed getter preconditions using `InputEventType`.

Remaining concerns:
- The examples remain intentionally small and preserve the original structure. They demonstrate `InputEvent` as a cause object but do not show a full dali-ui signal connection flow.
- The public `InputEvent` header has sparse getter comments, so getter behavior was verified against implementation assertions rather than public prose alone.
