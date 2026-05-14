# Input Event Prose Review

## Summary

Reviewed the Input Event guide draft against public headers in `repos/dali-ui/dali-ui-foundation/public-api/`. The document structure and most API descriptions are accurate. One code correction was needed.

## Changes Made

### 1. UiColor Constructor Arguments (PressedChangedSignal Handler section)

**Location:** "PressedChangedSignal Handler" section, code example

**Original:**
```cpp
view.SetBackgroundColor(Dali::Ui::UiColor(0xCCCCCCFF));
// ...
view.SetBackgroundColor(Dali::Ui::UiColor(0xFFFFFFFF));
```

**Revised:**
```cpp
view.SetBackgroundColor(Dali::Ui::UiColor(0xCCCCCC));
// ...
view.SetBackgroundColor(Dali::Ui::UiColor(0xFFFFFF));
```

**Source Evidence:** `repos/dali-ui/dali-ui-foundation/public-api/ui-color.h` line 67-71:
```cpp
/**
 * @brief Creates a UiColor from a hex RGB value.
 *
 * @param[in] rgb 0xRRGGBB format hex color
 * @param[in] a Alpha component (0.0 ~ 1.0), defaults to 1.0
 */
explicit UiColor(uint32_t rgb, float a = 1.0f);
```

**Reason:** The `UiColor` hex constructor takes RGB format (0xRRGGBB), not ARGB. The original code passed 8-digit hex values (0xCCCCCCFF, 0xFFFFFFFF) which would be misinterpreted. Corrected to 6-digit RGB values.

## Verified Accurate

The following sections were verified against source and required no changes:

1. **Event Types table** - Matches `InputEventType` enum in `input-event-type.h` exactly
2. **`New()` overloads** - All six overloads verified in `input-event.h` (default NONE, TouchEvent, KeyEvent, TapGesture, LongPressGesture, WheelEvent)
3. **`None()` static method** - Verified in `input-event.h` and implementation in `input-event.cpp`
4. **Getter methods** - All five getters (`GetTouchEvent()`, `GetKeyEvent()`, `GetTapGesture()`, `GetLongPressGesture()`, `GetWheelEvent()`) verified with correct const reference return types
5. **Signal signatures** - Verified in `interactive-trait.h`:
   - `ClickedSignal`: `Signal<void(View, InputEvent)>`
   - `PressedChangedSignal`: `Signal<void(View, bool, InputEvent)>`
   - `LongPressedSignal`: `Signal<bool(View, InputEvent)>`
6. **Connect methods** - Template method signatures verified in `interactive-trait.h`
7. **Code examples** - Usage patterns match sample code in `repos/dali-ui/samples/attachment/attachment-example.cpp`

## Remaining Concerns

None. The document accurately describes the `InputEvent` API for application developers.
