# Adaptor Framework Prose Review

## Summary

Reviewed the Adaptor Framework guide draft against public headers in `repos/dali-adaptor/dali/public-api/adaptor-framework/` and sample files in `repos/dali-ui/samples/`. The draft was largely accurate, with one code block correction needed.

## Changes Made

### 1. Code Block Correction: "Adding Views to a Window" Section

**Original:**
```cpp
View rootView = Layout::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT);
```

**Revised:**
```cpp
View rootView = View::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT);
```

**Source Evidence:** Searched `repos/dali-ui` for layout APIs. Found concrete layout types (`AbsoluteLayout`, `LinearLayout`, `FlexLayout`, `StackLayout`, `GridLayout`) but no base `Layout::New()` factory method. Sample files (e.g., `repos/dali-ui/samples/absolutelayout/absolutelayout-example.cpp`) use `View::New()` for simple containers or concrete layout types like `AbsoluteLayout::New()` when layout behavior is needed.

### 2. Prose Correction: Device Orientation Description

**Original:**
```
case DeviceStatus::Orientation::ORIENTATION_90:
  // Device rotated 90 degrees clockwise
```

**Revised:**
```
case DeviceStatus::Orientation::ORIENTATION_90:
  // Device's left side is at the top
```

**Source Evidence:** `repos/dali-adaptor/dali/public-api/adaptor-framework/device-status.h` line 60:
```cpp
ORIENTATION_90  = 90,  ///< The device's left side is at the top
```

The original description "rotated 90 degrees clockwise" is semantically equivalent but the header documentation uses "left side is at the top", which is more precise. Updated to match the official documentation.

## Verified Accurate (No Changes Needed)

1. **Application class APIs** - Verified against `repos/dali-adaptor/dali/public-api/adaptor-framework/application.h`:
   - `Application::New(&argc, &argv)` signature matches header
   - Lifecycle signals (`InitSignal()`, `PauseSignal()`, `ResumeSignal()`, `TerminateSignal()`, `ResetSignal()`) all present
   - `Quit()`, `Lower()`, `MainLoop()` methods verified
   - UI thread mode `Application::New(&argc, &argv, stylesheet, useUiThread, windowData)` signature correct

2. **Window class APIs** - Verified against `repos/dali-adaptor/dali/public-api/adaptor-framework/window.h`:
   - `Window::New(name, className, windowData)` signature correct
   - `Add(Actor)`, `SetBackgroundColor()`, `SetSize()`, `SetPosition()`, `SetAcceptFocus()`, `Show()`, `IsVisible()` all present
   - Signal types (`FocusChangeSignal`, `ResizeSignal`, `KeyEventSignal`) match header typedefs
   - Orientation methods (`AddAvailableOrientation()`, `SetPreferredOrientation()`) verified

3. **Timer class APIs** - Verified against `repos/dali-adaptor/dali/public-api/adaptor-framework/timer.h`:
   - `Timer::New(uint32_t milliSec)` signature correct
   - `Start()`, `Stop()`, `Pause()`, `Resume()`, `IsRunning()`, `GetInterval()`, `SetInterval()` all present
   - `TickSignal()` returns `Signal<bool()>` - callback signature correct

4. **DeviceStatus enums** - Verified against `repos/dali-adaptor/dali/public-api/adaptor-framework/device-status.h`:
   - `DeviceStatus::Battery::Status` values (NORMAL, CRITICALLY_LOW, POWER_OFF) correct
   - `DeviceStatus::Memory::Status` values (NORMAL, LOW, CRITICALLY_LOW) correct
   - `DeviceStatus::Orientation::Status` values (ORIENTATION_0, ORIENTATION_90, ORIENTATION_180, ORIENTATION_270) correct

5. **WindowData and enumerations** - Verified against `repos/dali-adaptor/dali/public-api/adaptor-framework/window-data.h` and `window-enumerations.h`:
   - `SetTransparency()`, `SetWindowType()` methods present
   - `WindowType::NORMAL` enum value correct
   - `WindowOrientation::PORTRAIT`, `WindowOrientation::LANDSCAPE` enum values correct

6. **Sample code patterns** - Verified against `repos/dali-ui/samples/absolutelayout/absolutelayout-example.cpp`:
   - Application creation pattern matches
   - Signal connection pattern matches
   - Key event handling pattern matches

## Remaining Concerns

None. All APIs referenced in the guide are verified against public headers and follow established usage patterns from sample code.
