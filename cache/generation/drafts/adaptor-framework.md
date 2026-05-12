---
title: Adaptor Framework
sidebar_label: Adaptor Framework
category: uncategorized
---

# Adaptor Framework

The Adaptor Framework provides the bridge between DALi applications and the underlying platform. It handles application lifecycle, window management, device status monitoring, input methods, and system integration.

## Table of Contents

- [Application Lifecycle](#application-lifecycle)
- [Window Management](#window-management)
- [Device Status and Orientation](#device-status-and-orientation)
- [Input Method Integration](#input-method-integration)
- [Clipboard Operations](#clipboard-operations)
- [Async Task Processing](#async-task-processing)
- [Feedback and Sound](#feedback-and-sound)
- [Style and Theme Monitoring](#style-and-theme-monitoring)
- [Timer-based Events](#timer-based-events)
- [Accessibility Bridge](#accessibility-bridge)

## Application Lifecycle

The `Dali::Application` class is the entry point for all DALi applications. It initializes the DALi framework and provides signals for lifecycle events.

### Creating an Application

Create an application instance in your main function and connect to the `InitSignal()` to set up your UI:

```cpp
#include <dali/public-api/adaptor-framework/application.h>

using namespace Dali;

class MyAppController : public ConnectionTracker
{
public:
  MyAppController(Application& application)
    : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &MyAppController::OnInit);
  }

  void OnInit(Application& application)
  {
    Window window = application.GetWindow();
    window.SetBackgroundColor(Color::WHITE);
    // Build your UI here
  }

private:
  Application& mApplication;
};

int main(int argc, char** argv)
{
  Application app = Application::New(&argc, &argv);
  MyAppController controller(app);
  app.MainLoop();
  return 0;
}
```

### Lifecycle Signals

Connect to lifecycle signals to handle application state changes:

```cpp
// Connect to lifecycle signals
mApplication.InitSignal().Connect(this, &MyAppController::OnInit);
mApplication.PauseSignal().Connect(this, &MyAppController::OnPause);
mApplication.ResumeSignal().Connect(this, &MyAppController::OnResume);
mApplication.TerminateSignal().Connect(this, &MyAppController::OnTerminate);
```

The `Dali::LifecycleController` provides an alternative way to observe lifecycle events from any component:

```cpp
#include <dali/devel-api/adaptor-framework/lifecycle-controller.h>

void SetupLifecycleObserver()
{
  LifecycleController::Get().InitSignal().Connect(this, &MyClass::OnAppInit);
  LifecycleController::Get().PauseSignal().Connect(this, &MyClass::OnAppPause);
  LifecycleController::Get().ResumeSignal().Connect(this, &MyClass::OnAppResume);
}
```

### UI Thread Mode

Enable UI thread mode to separate UI events from system events:

```cpp
#include <dali/public-api/adaptor-framework/window-data.h>

WindowData windowData;
windowData.width = 720;
windowData.height = 1280;

Application app = Application::New(
  &argc, &argv,
  "",                    // stylesheet
  true,                   // useUiThread
  windowData
);
```

When UI thread is enabled, task signals are emitted on the main thread while normal signals are emitted on the UI thread.

### Quitting and Lowering

Use `Quit()` to exit completely or `Lower()` to minimize without terminating:

```cpp
void OnKeyEvent(Window window, KeyEvent event)
{
  if (event.GetState() == KeyEvent::DOWN)
  {
    if (IsKey(event, Dali::DALI_KEY_ESCAPE) || IsKey(event, Dali::DALI_KEY_BACK))
    {
      mApplication.Quit();  // Full exit
      // Or: mApplication.Lower();  // Minimize
    }
  }
}
```

## Window Management

The `Dali::Window` class manages the application window and its properties.

### Getting the Main Window

Retrieve the main window from the application:

```cpp
void OnInit(Application& application)
{
  Window window = application.GetWindow();
  window.SetBackgroundColor(Vector4(0.95f, 0.95f, 0.95f, 1.0f));
}
```

### Adding Content to Window

Add views to the window's root layer:

```cpp
using namespace Dali::Ui;

void CreateUI(Window window)
{
  // Create a root view
  AbsoluteLayout rootLayout = AbsoluteLayout::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT);

  // Add child views
  View button = View::New();
  button.SetBackgroundColor(Color::BLUE);
  rootLayout.Add(button);

  // Add to window
  window.Add(rootLayout);
}
```

### Window Properties

Configure window appearance and behavior:

```cpp
// Set background color
window.SetBackgroundColor(Vector4(1.0f, 1.0f, 1.0f, 0.9f));

// Set window size and position
Window::WindowSize size(720, 1280);
window.SetSize(size);

Window::WindowPosition position(100, 100);
window.SetPosition(position);

// Set window transparency
window.SetTransparency(true);

// Control focus acceptance
window.SetAcceptFocus(true);
```

### Window Orientation

Manage window orientation for different device positions:

```cpp
// Add available orientations
window.AddAvailableOrientation(WindowOrientation::PORTRAIT);
window.AddAvailableOrientation(WindowOrientation::LANDSCAPE);

// Set preferred orientation
window.SetPreferredOrientation(WindowOrientation::PORTRAIT);
```

### Window Signals

Connect to window events:

```cpp
// Focus change
window.FocusChangeSignal().Connect(this, &MyClass::OnWindowFocusChanged);

// Resize
window.ResizeSignal().Connect(this, &MyClass::OnWindowResized);

// Key events
window.KeyEventSignal().Connect(this, &MyClass::OnKeyEvent);

// Touch events
window.TouchedSignal().Connect(this, &MyClass::OnTouch);
```

### Creating Additional Windows

Create secondary windows for multi-window applications:

```cpp
// Create a secondary window
PositionSize windowRect(100, 100, 400, 300);
Window secondaryWindow = Window::New(windowRect, "Secondary", false);
secondaryWindow.SetBackgroundColor(Color::WHITE);
secondaryWindow.Show();
```

## Device Status and Orientation

Monitor device battery, memory, and orientation status.

### Battery and Memory Status

Connect to device status signals:

```cpp
void SetupDeviceMonitoring(Application& app)
{
  app.LowBatterySignal().Connect(this, &MyClass::OnLowBattery);
  app.LowMemorySignal().Connect(this, &MyClass::OnLowMemory);
}

void OnLowBattery(DeviceStatus::Battery::Status status)
{
  if (status == DeviceStatus::Battery::CRITICALLY_LOW)
  {
    // Save state and reduce activity
  }
  else if (status == DeviceStatus::Battery::POWER_OFF)
  {
    // Emergency save
  }
}

void OnLowMemory(DeviceStatus::Memory::Status status)
{
  if (status == DeviceStatus::Memory::LOW)
  {
    // Release non-essential resources
  }
  else if (status == DeviceStatus::Memory::CRITICALLY_LOW)
  {
    // Release all possible resources
  }
}
```

### Device Orientation

Track physical device orientation changes:

```cpp
void SetupOrientationMonitoring(Application& app)
{
  app.DeviceOrientationChangedSignal().Connect(this, &MyClass::OnDeviceOrientationChanged);
}

void OnDeviceOrientationChanged(DeviceStatus::Orientation::Status status)
{
  switch (status)
  {
    case DeviceStatus::Orientation::ORIENTATION_0:
      // Portrait (normal)
      break;
    case DeviceStatus::Orientation::ORIENTATION_90:
      // Landscape (rotated left)
      break;
    case DeviceStatus::Orientation::ORIENTATION_180:
      // Portrait (upside down)
      break;
    case DeviceStatus::Orientation::ORIENTATION_270:
      // Landscape (rotated right)
      break;
  }
}
```

Use `Dali::Orientation` for detailed orientation information:

```cpp
#include <dali/devel-api/adaptor-framework/orientation.h>

void SetupOrientation()
{
  Orientation orientation = application.GetOrientation();
  orientation.ChangedSignal().Connect(this, &MyClass::OnOrientationChanged);
}

void OnOrientationChanged(Orientation orientation)
{
  int degrees = orientation.GetDegrees();
  float radians = orientation.GetRadians();
  // Adjust UI based on orientation
}
```

## Input Method Integration

The `Dali::InputMethodContext` class manages virtual keyboard and text input.

### Setting Up Input Method Context

Create and configure an input method context for text input:

```cpp
#include <dali/devel-api/adaptor-framework/input-method-context.h>

InputMethodContext mInputMethodContext;

void SetupTextInput()
{
  mInputMethodContext = InputMethodContext::New();
  mInputMethodContext.EventReceivedSignal().Connect(this, &MyClass::OnInputEvent);
  mInputMethodContext.StatusChangedSignal().Connect(this, &MyClass::OnKeyboardStatusChanged);
}
```

### Showing and Hiding the Keyboard

Control the virtual keyboard visibility:

```cpp
void ActivateTextInput()
{
  mInputMethodContext.Activate();  // Shows keyboard if no hardware keyboard
}

void DeactivateTextInput()
{
  mInputMethodContext.Deactivate();  // Hides keyboard
}

void ShowKeyboard()
{
  mInputMethodContext.ShowInputPanel();
}

void HideKeyboard()
{
  mInputMethodContext.HideInputPanel();
}
```

### Handling Input Events

Process text input from the keyboard:

```cpp
void OnInputEvent(InputMethodContext& imc, const InputMethodContext::EventData& event)
{
  switch (event.eventName)
  {
    case InputMethodContext::COMMIT:
      // Text committed
      HandleCommit(event.predictiveString);
      break;

    case InputMethodContext::PRE_EDIT:
      // Pre-edit text (composing)
      HandlePreEdit(event.predictiveString);
      break;

    case InputMethodContext::DELETE_SURROUNDING:
      // Delete characters around cursor
      DeleteText(event.cursorOffset, event.numberOfChars);
      break;
  }
}
```

### Configuring Input Options

Apply input method options for specific input types using `Property::Map`:

```cpp
#include <dali/devel-api/adaptor-framework/input-method-options.h>
#include <dali/public-api/adaptor-framework/input-method.h>

void ConfigurePasswordInput()
{
  InputMethodOptions options;
  Property::Map settings;
  settings.Insert(InputMethod::Category::PANEL_LAYOUT, InputMethod::PanelLayout::PASSWORD);
  options.ApplyProperty(settings);
  mInputMethodContext.ApplyOptions(options);
}

void ConfigureNumberInput()
{
  InputMethodOptions options;
  Property::Map settings;
  settings.Insert(InputMethod::Category::PANEL_LAYOUT, InputMethod::PanelLayout::NUMBER_ONLY);
  options.ApplyProperty(settings);
  mInputMethodContext.ApplyOptions(options);
}
```

## Clipboard Operations

The `Dali::Clipboard` class provides clipboard access for copy and paste operations.

### Accessing the Clipboard

Get the clipboard instance:

```cpp
#include <dali/devel-api/adaptor-framework/clipboard.h>

void SetupClipboard()
{
  Clipboard clipboard = Clipboard::Get();
  if (clipboard.IsAvailable())
  {
    clipboard.DataReceivedSignal().Connect(this, &MyClass::OnClipboardDataReceived);
  }
}
```

### Sending Data to Clipboard

Copy data to the clipboard:

```cpp
void CopyToClipboard(const std::string& text)
{
  Clipboard clipboard = Clipboard::Get();
  Clipboard::ClipData clipData("text/plain", text.c_str());
  clipboard.SetData(clipData);
}
```

### Receiving Data from Clipboard

Request and receive clipboard data:

```cpp
void PasteFromClipboard()
{
  Clipboard clipboard = Clipboard::Get();
  uint32_t requestId = clipboard.GetData("text/plain");
  // Result arrives via DataReceivedSignal
}

void OnClipboardDataReceived(uint32_t requestId, const char* mimeType, const char* data)
{
  if (mimeType && std::string(mimeType) == "text/plain")
  {
    std::string pastedText(data);
    // Handle pasted text
  }
}
```

## Async Task Processing

Use `Dali::AsyncTaskManager` to process tasks in background threads.

### Creating an Async Task

Define a custom async task:

```cpp
#include <dali/devel-api/adaptor-framework/async-task-manager.h>

class ImageLoadTask : public AsyncTask
{
public:
  ImageLoadTask(const std::string& path, CallbackBase* callback)
    : AsyncTask(callback, PriorityType::HIGH),
      mPath(path)
  {}

  void Process() override
  {
    // This runs on a worker thread
    // Load image data from mPath
    mResult = LoadImageFromFile(mPath);
  }

  bool IsReady() override
  {
    return true;  // Ready to process immediately
  }

  ImageData GetResult() const { return mResult; }

private:
  std::string mPath;
  ImageData mResult;
};
```

### Submitting Tasks

Add tasks to the async task manager:

```cpp
void LoadImageAsync(const std::string& path)
{
  auto callback = MakeCallback(this, &MyClass::OnImageLoaded);
  AsyncTaskPtr task = new ImageLoadTask(path, callback);
  AsyncTaskManager::Get().AddTask(task);
}

void OnImageLoaded()
{
  // Called on main thread after task completes
  // Safe to update UI here
}
```

### Task Priority

Control task processing order with priority:

```cpp
// High priority - processed first
AsyncTaskPtr urgentTask = new MyTask(callback, AsyncTask::PriorityType::HIGH);

// Low priority - may wait if threads are busy
AsyncTaskPtr backgroundTask = new MyTask(callback, AsyncTask::PriorityType::LOW);
```

## Feedback and Sound

Provide haptic and audio feedback using the feedback player classes.

### Haptic Feedback

Play vibration patterns:

```cpp
#include <dali/devel-api/adaptor-framework/feedback-player.h>

void PlayTouchFeedback()
{
  FeedbackPlayer player = FeedbackPlayer::Get();

  // Play a short vibration
  player.PlayMonotone(50);  // 50ms vibration

  // Play a vibration pattern file
  player.PlayFile("/usr/share/feedback/touch.ivt");
}

void StopFeedback()
{
  FeedbackPlayer::Get().Stop();
}
```

### Sound Playback

Play sound effects:

```cpp
#include <dali/devel-api/adaptor-framework/sound-player.h>

int mSoundHandle = 0;

void PlayClickSound()
{
  SoundPlayer player = SoundPlayer::Get();
  player.SoundPlayFinishedSignal().Connect(this, &MyClass::OnSoundFinished);
  mSoundHandle = player.PlaySound("/usr/share/sounds/click.wav");
}

void StopClickSound()
{
  if (mSoundHandle != 0)
  {
    SoundPlayer::Get().Stop(mSoundHandle);
    mSoundHandle = 0;
  }
}

void OnSoundFinished(SoundPlayer& player)
{
  mSoundHandle = 0;
}
```

## Style and Theme Monitoring

Monitor system style and theme changes using `Dali::StyleMonitor`.

### Getting Style Information

Retrieve current style settings:

```cpp
#include <dali/devel-api/adaptor-framework/style-monitor.h>

void LoadCurrentStyle()
{
  StyleMonitor monitor = StyleMonitor::Get();

  std::string fontFamily = monitor.GetDefaultFontFamily();
  std::string fontStyle = monitor.GetDefaultFontStyle();
  int fontSize = monitor.GetDefaultFontSize();
  const std::string& theme = monitor.GetTheme();
}
```

### Responding to Style Changes

Connect to style change signals:

```cpp
void SetupStyleMonitoring()
{
  StyleMonitor::Get().StyleChangeSignal().Connect(this, &MyClass::OnStyleChanged);
}

void OnStyleChanged(StyleMonitor monitor, StyleChange::Type changeType)
{
  switch (changeType)
  {
    case StyleChange::DEFAULT_FONT_CHANGE:
      // Update fonts
      break;
    case StyleChange::DEFAULT_FONT_SIZE_CHANGE:
      // Update font sizes
      break;
    case StyleChange::THEME_CHANGE:
      // Reload theme
      break;
  }
}
```

### Setting Custom Theme

Apply a custom theme:

```cpp
void ApplyCustomTheme(const std::string& themePath)
{
  StyleMonitor monitor = StyleMonitor::Get();
  monitor.SetTheme(themePath);
}
```

## Timer-based Events

Use `Dali::Timer` for periodic or one-shot events.

### Creating a Timer

Create and start a timer:

```cpp
#include <dali/public-api/adaptor-framework/timer.h>

Timer mTimer;

void SetupTimer()
{
  mTimer = Timer::New(1000);  // 1 second interval
  mTimer.TickSignal().Connect(this, &MyClass::OnTimerTick);
  mTimer.Start();
}

bool OnTimerTick()
{
  // Return true to continue, false to stop
  UpdateUI();
  return true;
}
```

### Timer Control

Control timer execution:

```cpp
void PauseTimer()
{
  mTimer.Pause();
}

void ResumeTimer()
{
  mTimer.Resume();
}

void StopTimer()
{
  mTimer.Stop();
}

void ChangeInterval()
{
  mTimer.SetInterval(2000);  // Change to 2 seconds, restarts timer
}
```

### One-shot Timer

Create a timer that fires once:

```cpp
void SetupOneShotTimer()
{
  Timer oneShot = Timer::New(3000);
  oneShot.TickSignal().Connect(this, &MyClass::OnOneShot);
  oneShot.Start();
}

bool OnOneShot()
{
  DoSomething();
  return false;  // Returning false stops the timer
}
```

## Accessibility Bridge

The accessibility framework provides AT-SPI integration for assistive technologies.

### Accessibility Types

The `Dali::Accessibility` namespace provides types for accessibility support:

```cpp
#include <dali/devel-api/adaptor-framework/accessibility.h>

// Accessibility roles
Role buttonRole = Role::PUSH_BUTTON;
Role labelRole = Role::LABEL;

// Accessibility states
States states;
states.Set(State::ENABLED, true);
states.Set(State::FOCUSABLE, true);
states.Set(State::VISIBLE, true);
```

### Accessibility Relations

Define relationships between accessible objects:

```cpp
// Create a relation between objects
std::vector<Accessible*> targets;
targets.push_back(targetAccessible);

Relation relation(RelationType::LABELLED_BY, targets);
```

### Accessibility Addresses

Identify accessible objects on the accessibility bus:

```cpp
Address address = accessible->GetAddress();
std::string bus = address.GetBus();
std::string path = address.GetPath();
```

### Gesture Information

Handle accessibility gestures:

```cpp
GestureInfo gestureInfo(
  Gesture::ONE_FINGER_DOUBLE_TAP,
  startX, endX,
  startY, endY,
  GestureState::ENDED,
  eventTime
);
