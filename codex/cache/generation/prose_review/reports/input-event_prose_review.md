# Input Event Prose Review

Changed prose:
- Clarified that input events are delivered through dali-ui interactive view callbacks, specifically `Dali::Ui::InteractiveView` and `Dali::Ui::InteractiveTrait`, while callbacks still pass `Dali::Ui::View`.
- Replaced broad “later processing during the same application flow” wording with “pass the same event cause through application helper code” to avoid implying a lifetime policy not stated by the API.
- Added that typed accessors expect the stored event type to match the accessor, matching the implementation assertions.
- Clarified non-input causes as explicit API calls and framework-triggered lifecycle changes, matching `InputEventType::NONE` documentation.
- Clarified that the wheel wrapper example adapts a `Dali::WheelEvent`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/input-event.h` declares `Dali::Ui::InputEvent`, `New()` overloads, `None()`, `GetInputEventType()`, and typed accessors.
- `repos/dali-ui/dali-ui-foundation/public-api/input-event-type.h` defines `NONE`, `TOUCH_EVENT`, `KEY_EVENT`, `TAP_GESTURE`, `LONG_PRESS_GESTURE`, `WHEEL_EVENT`, and `RESERVED`; `NONE` covers explicit API calls and framework-triggered lifecycle changes.
- `repos/dali-ui/dali-ui-foundation/internal/input-event-impl.cpp` stores the event type for each `New(...)` overload and asserts matching types before returning typed event references.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-view.h` and `interactive-trait.h` expose callbacks/signals with `View` and `InputEvent` parameters.

Remaining concerns:
- The guide keeps the existing wheel-focused creation section, although the public API also has `New(...)` overloads for touch, key, tap, and long-press events. This was left intact to preserve the draft structure and avoid broadening the guide beyond the localized review scope.
