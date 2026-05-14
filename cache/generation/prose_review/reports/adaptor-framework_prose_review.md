# Adaptor Framework Prose Review

## Summary

Reviewed the draft against public headers in `repos/dali-adaptor/dali/public-api/adaptor-framework/`, `repos/dali-adaptor/dali/devel-api/adaptor-framework/`, and sample code in `repos/dali-ui/samples/`. The draft was largely accurate. Minor corrections were applied for API signature accuracy and consistency.

## Changes Made

### 1. Application Lifecycle - InitSignal Callback Signature
**Location:** Application Lifecycle > Creating an Application
**Issue:** The draft showed `void OnInitialize(Application& application)` with `Application&` parameter.
**Source Evidence:** `application.h` line 47 defines `typedef Signal<void(Application)> AppSignalType;` - the signal passes `Application` by value, not by reference.
**Fix:** Changed callback signature to `void OnInitialize(Application application)`.

### 2. Device Orientation - Orientation Object Acquisition
**Location:** Device Orientation > Orientation Object
**Issue:** The draft stated `Orientation orientation; // Obtained through platform-specific means` which was vague and unhelpful.
**Source Evidence:** `orientation.h` line 51 comment states "This can be initialized by calling Dali::Application::GetOrientation()".
**Fix:** Updated to show `Orientation orientation = application.GetOrientation();` as the proper acquisition method.

### 3. Input Method Options - Button Actions List
**Location:** Input Method Context > Configuring Input Options
**Issue:** The draft listed button actions as "DONE, GO, SEARCH, SEND, and NEXT" but omitted other available values.
**Source Evidence:** `input-method.h` lines 113-125 show `ButtonAction::Type` enum includes: `DEFAULT`, `DONE`, `GO`, `JOIN`, `LOGIN`, `NEXT`, `SEARCH`, `SEND`, `SIGNIN`.
**Fix:** Updated to list complete set: `DEFAULT`, `DONE`, `GO`, `SEARCH`, `SEND`, `NEXT`, `JOIN`, `LOGIN`, and `SIGNIN`.

### 4. Window Management - Terminology Clarification
**Location:** Window Management > Adding Content to the Window
**Issue:** The draft used "Add views to the window's scene" but the API accepts `Actor`.
**Source Evidence:** `window.h` line 117 shows `void Add(Actor actor);`.
**Fix:** Changed to "Add actors to the window's scene" for API accuracy while noting that `View` (which inherits from `Actor`) can be used.

### 5. Device Orientation - Status Values Documentation
**Location:** Device Orientation > Device Orientation Events
**Issue:** The draft comment was vague about status values.
**Source Evidence:** `device-status.h` lines 55-62 show `DeviceStatus::Orientation::Status` enum values: `ORIENTATION_0`, `ORIENTATION_90`, `ORIENTATION_180`, `ORIENTATION_270`.
**Fix:** Added comment showing the actual status values.

## Verified Accurate (No Changes Required)

- **Application::New()** signature and usage verified against `application.h` and sample code
- **LifecycleController::Get()** and signals verified against `lifecycle-controller.h`
- **ComponentApplication** API verified against `component-application.h`
- **OffscreenApplication** API verified against `offscreen-application.h`
- **Window** methods and signals verified against `window.h`
- **Timer** API verified against `timer.h`
- **Clipboard** API verified against `clipboard.h`
- **DragAndDrop** API verified against `drag-and-drop.h`
- **FeedbackPlayer** API verified against `feedback-player.h`
- **PhysicalKeyboard** API verified against `physical-keyboard.h`
- **AsyncTask** and **AsyncTaskManager** API verified against `async-task-manager.h`
- **InputMethodContext** API verified against `input-method-context.h`
- **InputMethodOptions** API verified against `input-method-options.h`
- **InputMethod** enums verified against `input-method.h`

## Remaining Considerations

1. **Sample Code Style:** The code examples use `Dali::Ui::View` which is appropriate for the dali-ui-app profile, but the `Window::Add()` method accepts `Actor`. This is technically correct since `View` inherits from `Actor`.

2. **OffscreenApplication Event Loop:** The documentation correctly notes that `OffscreenApplication` does not have its own event loop. This is an important distinction from regular `Application`.

3. **AsyncTask ThreadType:** The example uses `ThreadType::MAIN_THREAD` for callback invocation. Developers should be aware that `ThreadType::WORKER_THREAD` is also available for background callback processing.

## Code Block Preservation

All code blocks were preserved as required by `prose_review.preserve_code_blocks: true`. Only prose surrounding code blocks was modified for accuracy.
