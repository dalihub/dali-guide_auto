# State Event Prose Review

## Summary

The draft was reviewed against the following source files:
- `repos/dali-ui/dali-ui-foundation/public-api/state-event.h` - StateEvent class definition
- `repos/dali-ui/dali-ui-foundation/public-api/view-state.h` - ViewState class definition
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` - View class with StateChangedSignal
- `repos/dali-ui/dali-ui-foundation/public-api/input-event.h` - InputEvent class definition
- `repos/dali-ui/dali-ui-foundation/public-api/input-event-type.h` - InputEventType enum

## Changes Made

### 1. Added precondition note for specific event getters (Section: "Getting the Specific Event")

**Original prose:**
> Retrieve the underlying input event for detailed information:

**Revised prose:**
> Retrieve the underlying input event for detailed information. Each getter has a precondition: the input event type must match the requested event type.

**Source evidence:**
From `state-event.h`:
```cpp
/**
 * @brief Returns the touch event that caused this transition.
 *
 * @pre GetInputEventType() == InputEventType::TOUCH_EVENT
 */
const TouchEvent& GetTouchEvent() const;
```
Similar `@pre` preconditions exist for `GetKeyEvent()`, `GetTapGesture()`, `GetLongPressGesture()`, and `GetWheelEvent()`.

**Rationale:** The draft correctly showed type-checking before calling specific getters, but did not explicitly state that these methods have preconditions. Adding this note helps developers understand why the type check is necessary.

## Verified Accurate (No Changes Needed)

The following were verified against source and found to be accurate:

1. **API signatures**: All method signatures match the headers exactly:
   - `GetPrev()` returns `const ViewState&`
   - `GetCurrent()` returns `const ViewState&`
   - `Added(const ViewState&)` returns `bool`
   - `Removed(const ViewState&)` returns `bool`
   - `Changed(const ViewState&)` returns `bool`
   - `GetCause()` returns `const InputEvent&`
   - `GetInputEventType()` returns `InputEventType`
   - All specific event getters return const references

2. **Signal signature**: `StateChangedSignalType = Signal<void(View, StateEvent)>` matches the header exactly.

3. **InputEventType enum values**: All enum values in the draft (`NONE`, `TOUCH_EVENT`, `KEY_EVENT`, `TAP_GESTURE`, `LONG_PRESS_GESTURE`, `WHEEL_EVENT`) match the enum definition in `input-event-type.h`.

4. **ViewState constants**: `FOCUSED`, `PRESSED`, `DISABLED` are all valid predefined states in `view-state.h`.

5. **GetCause() behavior**: The draft correctly states it "Always valid; type is InputEventType::NONE for programmatic changes" - this matches the header doc: "Always returns a valid InputEvent. Programmatic or system-triggered state changes return an InputEvent with InputEventType::NONE."

6. **Code blocks**: All code examples use correct API syntax and follow the View-based idiom as required.

## Remaining Concerns

None. The draft is accurate and well-structured for the target audience (application developers using dali-ui public APIs).
