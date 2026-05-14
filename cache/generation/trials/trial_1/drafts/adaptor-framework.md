---
title: Adaptor Framework
sidebar_label: Adaptor Framework
category: application-framework
---

# Adaptor Framework

The Adaptor Framework provides the core infrastructure for DALi applications, including application lifecycle management, window handling, device input, and system integration services.

## Table of Contents

- [Application Lifecycle](#application-lifecycle)
- [Window Management](#window-management)
- [Device Orientation](#device-orientation)
- [Input Method Context](#input-method-context)
- [Async Task Processing](#async-task-processing)
- [Timer and Scheduling](#timer-and-scheduling)
- [Clipboard Operations](#clipboard-operations)
- [Drag and Drop](#drag-and-drop)
- [Feedback and Haptics](#feedback-and-haptics)

## Application Lifecycle

The `Dali::Application` class is the primary entry point for DALi applications. It initializes the DALi core and provides lifecycle signals for application state changes.

### Creating an Application

Create an `Application` instance in your main function and connect to the `InitSignal()` to set up your UI:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class MyAppController : public ConnectionTracker
{
public:
  MyAppController(Application& application)
    : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &MyAppController::OnInitialize);
  }

  void OnInitialize(Application application)
  {
    Window window = application.GetWindow();
    window.SetBackgroundColor(Color::WHITE);

    // Build your UI here using Dali::Ui::View
    View rootView = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT);
    window.Add(rootView);
  }

private:
  Application& mApplication;
};

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);
  MyAppController controller(application);
  application.MainLoop();
  return 0;
}
```

### Lifecycle Signals

Connect to lifecycle signals to handle application state transitions:

```cpp
// Connect to lifecycle signals
application.InitSignal().Connect(this, &MyAppController::OnInitialize);
application.PauseSignal().Connect(this, &MyAppController::OnPause);
application.ResumeSignal().Connect(this, &MyAppController::OnResume);
application.TerminateSignal().Connect(this, &MyAppController::OnTerminate);
```

The `LifecycleController` provides an alternative way to observe lifecycle events from any component:

```cpp
LifecycleController lifecycle = LifecycleController::Get();
lifecycle.InitSignal().Connect(this, &MyClass::OnApplicationInit);
lifecycle.PauseSignal().Connect(this, &MyClass::OnPause);
lifecycle.ResumeSignal().Connect(this, &MyClass::OnResume);
```

### Quitting the Application

Call `Quit()` to terminate the application:

```cpp
void OnKeyEvent(Window window, KeyEvent event)
{
  if (event.GetState() == KeyEvent::DOWN)
  {
    if (IsKey(event, Dali::DALI_KEY_ESCAPE) || IsKey(event, Dali::DALI_KEY_BACK))
    {
      mApplication.Quit();
    }
  }
}
```

### Component Application

For component-based applications that draw multiple UI instances in one process, use `ComponentApplication`:

```cpp
ComponentApplication application = ComponentApplication::New(&argc, &argv);
application.CreateSignal().Connect(this, &MyController::OnCreate);
application.MainLoop();
```

### Offscreen Application

For rendering without a visible window, use `OffscreenApplication`:

```cpp
OffscreenApplication app = OffscreenApplication::New(
  &argc, &argv,
  OffscreenApplication::FrameworkBackend::ECORE,
  OffscreenApplication::RenderMode::AUTO);

app.Start();

OffscreenWindow window = app.GetWindow();
// Configure offscreen rendering...

// Run your own event loop
while (running)
{
  // Process events
  // In AUTO mode, rendering happens automatically
  // In MANUAL mode, call app.RenderOnce() when needed
}

app.Terminate();
```

## Window Management

The `Dali::Window` class manages the application window and provides access to the scene.

### Accessing the Window

Retrieve the main window from the application:

```cpp
Window window = application.GetWindow();
```

### Adding Content to the Window

Add actors to the window's scene:

```cpp
Window window = application.GetWindow();
window.SetBackgroundColor(Color::WHITE);

View rootView = View::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT);
rootView.SetBackgroundColor(Color::BLUE);
window.Add(rootView);
```

### Window Properties

Configure window appearance and behavior:

```cpp
// Set background color
window.SetBackgroundColor(Vector4(0.9f, 0.9f, 0.9f, 1.0f));

// Set window size and position
window.SetSize(Window::WindowSize(800, 600));
window.SetPosition(Window::WindowPosition(100, 100));

// Control visibility
window.Show();
window.Hide();

// Raise or lower window in stack
window.Raise();
window.Lower();
```

### Window Signals

Connect to window events:

```cpp
// Focus change
window.FocusChangeSignal().Connect(this, &MyClass::OnFocusChange);

// Resize
window.ResizeSignal().Connect(this, &MyClass::OnResize);

// Key events
window.KeyEventSignal().Connect(this, &MyClass::OnKeyEvent);

// Touch events
window.TouchedSignal().Connect(this, &MyClass::OnTouch);
```

### Creating Additional Windows

Create secondary windows for multi-window applications:

```cpp
Window secondaryWindow = Window::New(
  PositionSize(0, 0, 400, 300),
  "Secondary Window",
  false);  // isTransparent

secondaryWindow.SetBackgroundColor(Color::WHITE);
secondaryWindow.Show();
```

### Window Orientation

Configure supported orientations:

```cpp
window.AddAvailableOrientation(WindowOrientation::PORTRAIT);
window.AddAvailableOrientation(WindowOrientation::LANDSCAPE);
window.SetPreferredOrientation(WindowOrientation::PORTRAIT);
```

## Device Orientation

The `Dali::Orientation` class provides device orientation information. Orientation can be obtained through the application's `DeviceOrientationChangedSignal`.

### Device Orientation Events

Connect to the device orientation changed signal on the application:

```cpp
application.DeviceOrientationChangedSignal().Connect(this, &MyClass::OnDeviceOrientationChanged);

void OnDeviceOrientationChanged(DeviceStatus::Orientation::Status status)
{
  // Handle device orientation change
  // Status values: ORIENTATION_0, ORIENTATION_90, ORIENTATION_180, ORIENTATION_270
}
```

### Window Orientation

Configure supported orientations on the window:

```cpp
window.AddAvailableOrientation(WindowOrientation::PORTRAIT);
window.AddAvailableOrientation(WindowOrientation::LANDSCAPE);
window.SetPreferredOrientation(WindowOrientation::PORTRAIT);

WindowOrientation preferred = window.GetPreferredOrientation();
```

### Orientation Object

The `Orientation` class provides orientation values in degrees or radians. Obtain an instance via `Application::GetOrientation()`:

```cpp
Orientation orientation = application.GetOrientation();

int degrees = orientation.GetDegrees();    // 0, 90, 180, or 270
float radians = orientation.GetRadians(); // 0, PI/2, PI, or 3*PI/2

// Connect to orientation change signal
orientation.ChangedSignal().Connect(this, &MyClass::OnOrientationChanged);
```

## Input Method Context

The `Dali::InputMethodContext` class manages the virtual keyboard and text input.

### Activating the Keyboard

```cpp
InputMethodContext imc = InputMethodContext::New();

// Show keyboard when text input is needed
imc.Activate();

// Hide keyboard when done
imc.Deactivate();
```

### Handling Keyboard Events

Connect to the event received signal to handle text input:

```cpp
imc.EventReceivedSignal().Connect(this, &MyClass::OnKeyboardEvent);

InputMethodContext::CallbackData OnKeyboardEvent(
  InputMethodContext imc,
  const InputMethodContext::EventData& event)
{
  switch (event.eventName)
  {
    case InputMethodContext::EventType::COMMIT:
      // Handle committed text: event.predictiveString
      break;
    case InputMethodContext::EventType::PRE_EDIT:
      // Handle pre-edit text
      break;
    case InputMethodContext::EventType::DELETE_SURROUNDING:
      // Delete characters around cursor
      break;
    default:
      break;
  }

  return InputMethodContext::CallbackData();
}
```

### Configuring Input Options

Configure the keyboard using `InputMethodOptions` with a property map:

```cpp
InputMethodOptions options;

Property::Map settings;
settings.Insert("PANEL_LAYOUT", InputMethod::PanelLayout::NORMAL);
settings.Insert("BUTTON_ACTION", InputMethod::ButtonAction::DONE);
settings.Insert("AUTO_CAPITALIZE", InputMethod::AutoCapital::SENTENCE);

options.ApplyProperty(settings);
imc.ApplyOptions(options);
```

Available panel layouts include `InputMethod::PanelLayout::NORMAL`, `NUMBER`, `EMAIL`, `URL`, `PASSWORD`, and others. Button actions include `DEFAULT`, `DONE`, `GO`, `SEARCH`, `SEND`, `NEXT`, `JOIN`, `LOGIN`, and `SIGNIN`.

### Keyboard State

Check keyboard visibility:

```cpp
InputMethodContext::State state = imc.GetInputPanelState();
if (state == InputMethodContext::State::SHOW)
{
  // Keyboard is visible
}
```

## Async Task Processing

The `Dali::AsyncTaskManager` enables background processing of tasks.

### Creating an Async Task

Define a custom task by inheriting from `AsyncTask`:

```cpp
class MyAsyncTask : public AsyncTask
{
public:
  MyAsyncTask(CallbackBase* callback)
    : AsyncTask(callback, PriorityType::DEFAULT, ThreadType::MAIN_THREAD)
  {
  }

  void Process() override
  {
    // Perform background work here
    // This runs on a worker thread
    mResult = PerformHeavyComputation();
  }

  bool IsReady() override
  {
    return true; // Return false to delay processing
  }

  int GetResult() const { return mResult; }

private:
  int mResult{0};
};
```

### Submitting Tasks

```cpp
AsyncTaskManager taskManager = AsyncTaskManager::Get();

MyAsyncTask* task = new MyAsyncTask(MakeCallback(&OnTaskComplete));
taskManager.AddTask(AsyncTaskPtr(task));

void OnTaskComplete()
{
  // Called on main thread when task completes
  // Access task results here
}
```

### Removing Tasks

```cpp
taskManager.RemoveTask(taskPtr);
```

## Timer and Scheduling

The `Dali::Timer` class provides periodic or one-shot timers.

### Creating a Timer

```cpp
Timer timer = Timer::New(1000); // 1000ms interval
timer.TickSignal().Connect(this, &MyClass::OnTimerTick);
timer.Start();

bool OnTimerTick()
{
  // Return true to continue, false to stop
  return true;
}
```

### Timer Control

```cpp
timer.Start();   // Start or restart the timer
timer.Stop();    // Stop the timer
timer.Pause();   // Pause the timer
timer.Resume();  // Resume from pause

// Check if running
bool running = timer.IsRunning();

// Get interval
uint32_t interval = timer.GetInterval();

// Change interval
timer.SetInterval(500); // Stops and restarts with new interval
```

### One-shot Timer

For a single execution, return `false` from the tick callback:

```cpp
Timer oneShot = Timer::New(2000);
oneShot.TickSignal().Connect([]() {
  // Do something once
  return false; // Stop after first tick
});
oneShot.Start();
```

## Clipboard Operations

The `Dali::Clipboard` class provides clipboard access for copy and paste.

### Setting Clipboard Data

```cpp
Clipboard clipboard = Clipboard::Get();

Clipboard::ClipData clipData;
clipData.SetMimeType("text/plain");
clipData.SetData("Hello, World!");

clipboard.SetData(clipData);
```

### Getting Clipboard Data

```cpp
clipboard.DataReceivedSignal().Connect(this, &MyClass::OnDataReceived);

uint32_t requestId = clipboard.GetData("text/plain");

void OnDataReceived(uint32_t id, const char* mimeType, const char* data)
{
  // Process received clipboard data
}
```

### Checking Clipboard Content

```cpp
bool hasText = clipboard.HasType("text/plain");
size_t itemCount = clipboard.NumberOfItems();
```

### Clipboard Visibility

```cpp
clipboard.ShowClipboard();
clipboard.HideClipboard();
bool visible = clipboard.IsVisible();
```

## Drag and Drop

The `Dali::DragAndDrop` class enables drag and drop between windows.

### Starting a Drag Operation

```cpp
DragAndDrop dnd = DragAndDrop::Get();

DragAndDrop::DragData dragData;
const char* mimeTypes[] = {"text/plain"};
const char* data[] = {"Dragged text"};
dragData.SetMimeTypes(mimeTypes, 1);
dragData.SetDataSet(data, 1);

dnd.StartDragAndDrop(
  sourceActor,
  shadowWindow,
  dragData,
  [](DragAndDrop::SourceEventType type) {
    switch (type)
    {
      case DragAndDrop::SourceEventType::ACCEPT:
        // Drop was accepted
        break;
      case DragAndDrop::SourceEventType::CANCEL:
        // Drag was cancelled
        break;
      default:
        break;
    }
  });
```

### Receiving Drop Events

```cpp
dnd.AddListener(targetActor, "text/plain",
  [](const DragAndDrop::DragEvent& event) {
    switch (event.GetAction())
    {
      case DragAndDrop::DragType::ENTER:
        // Highlight drop target
        break;
      case DragAndDrop::DragType::DROP:
        // Handle dropped data
        break;
      case DragAndDrop::DragType::LEAVE:
        // Remove highlight
        break;
      default:
        break;
    }
  });

// Remove listener when done
dnd.RemoveListener(targetActor);
```

## Feedback and Haptics

The `Dali::FeedbackPlayer` class provides haptic and sound feedback.

### Vibration Feedback

```cpp
FeedbackPlayer feedback = FeedbackPlayer::Get();

// Play monotone vibration
feedback.PlayMonotone(100); // 100ms duration

// Play vibration pattern from file
feedback.PlayFile("/path/to/vibration_pattern.ivt");

// Stop vibration
feedback.Stop();
```

### Sound Feedback

```cpp
// Play sound file
int soundHandle = feedback.PlaySound("/path/to/sound.wav");

// Stop sound
feedback.StopSound(soundHandle);
```

### Feedback Patterns

```cpp
// Play predefined feedback pattern
feedback.PlayFeedbackPattern(type, pattern);
```

## Physical Keyboard Detection

The `Dali::PhysicalKeyboard` class detects hardware keyboard attachment.

```cpp
PhysicalKeyboard keyboard = PhysicalKeyboard::Get();

if (keyboard.IsAttached())
{
  // Physical keyboard is connected
}

// Listen for keyboard connection changes
keyboard.StatusChangedSignal().Connect(this, &MyClass::OnKeyboardStatusChanged);

void OnKeyboardStatusChanged(PhysicalKeyboard keyboard)
{
  if (keyboard.IsAttached())
  {
    // Keyboard was just connected
  }
  else
  {
    // Keyboard was just disconnected
  }
}
```
