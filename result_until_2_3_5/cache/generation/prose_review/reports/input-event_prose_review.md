# Input Event Prose Review

Changed prose:
- Clarified that `Dali::Ui::InputEvent` is an app-facing handle, matching its `BaseHandle` inheritance and callback use.
- Added that `Dali::Ui::InputEvent::New(...)` overloads exist for `Dali::TouchEvent`, `Dali::KeyEvent`, `Dali::TapGesture`, `Dali::LongPressGesture`, and `Dali::WheelEvent`.
- Tightened routing guidance so it no longer overclaims that the sample router calls the helper.
- Replaced “owns typed accessors” with “provides typed accessors.”
- Added that typed accessors are checked against the stored `Dali::Ui::InputEventType`.
- Clarified `Dali::Ui::InputEvent::None()` as a shared `NONE`-type event and mentioned `Dali::Ui::InputEvent::New()` for a separate `NONE`-type handle.
- Updated the storing example to initialize `mLastInput` with `Dali::Ui::InputEvent::None()` so `GetLastInputType()` is not shown reading a default uninitialized handle.
- Replaced “copy assignment through `operator=`” with copy construction and copy assignment, matching the public handle API.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/input-event.h` declares `InputEvent` as a `BaseHandle`, public `New()` overloads for no input, touch, key, tap, long press, and wheel, `None()`, `GetInputEventType()`, and the typed payload accessors.
- `repos/dali-ui/dali-ui-foundation/public-api/input-event-type.h` defines `InputEventType::NONE`, `TOUCH_EVENT`, `KEY_EVENT`, `TAP_GESTURE`, `LONG_PRESS_GESTURE`, and `WHEEL_EVENT`.
- `repos/dali-ui/dali-ui-foundation/public-api/input-event.cpp` shows `None()` returning a static shared `InputEvent` created by `InputEvent::New()`.
- `repos/dali-ui/dali-ui-foundation/internal/input-event-impl.cpp` shows each typed accessor asserting that the stored `InputEventType` matches the requested payload.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-trait.h` and samples show interaction callbacks using `View` and `InputEvent`.

Remaining concerns:
- The guide intentionally keeps DALi core event classes as payload types because the public dali-ui `InputEvent` API returns those types.
- The `RouteInputEvent()` sample remains minimal to preserve the original code block shape; a fuller `switch` example could be added in a separate enrichment pass if section restructuring is allowed.
