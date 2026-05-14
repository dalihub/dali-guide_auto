---
title: Adaptor Framework
sidebar_label: Adaptor Framework
category: application-framework
---

# Adaptor Framework

The Adaptor Framework provides the core infrastructure for DALi applications, including application lifecycle management, window handling, timer-based events, and device status monitoring.

## Table of Contents

- [Application Lifecycle](#application-lifecycle)
- [Window Management](#window-management)
- [Timer and Periodic Events](#timer-and-periodic-events)
- [Device Status Monitoring](#device-status-monitoring)

## Application Lifecycle

Every DALi application must create an `Application` instance to initialize the DALi runtime. The `Application` class manages the main event loop and emits signals for lifecycle events such as initialization, pause, resume, and termination.

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
    // Build your UI here
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

The `Application` class provides signals for all major lifecycle events:

| Signal | Description |
|--------|-------------|
| `InitSignal()` | Emitted when the application is ready for initialization |
| `PauseSignal()` | Emitted when the application is paused |
| `ResumeSignal()` | Emitted when the application resumes |
| `TerminateSignal()` | Emitted when the application is terminating |
| `ResetSignal()` | Emitted when the system requires reinitialization |

Connect to these signals to manage application state:

```cpp
class MyAppController : public ConnectionTracker
{
public:
  MyAppController(Application& application)
    : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &MyAppController::OnInit);
    mApplication.PauseSignal().Connect(this, &MyAppController::OnPause);
    mApplication.ResumeSignal().Connect(this, &MyAppController::OnResume);
    mApplication.TerminateSignal().Connect(this, &MyAppController::OnTerminate);
  }

  void OnInit(Application app) { /* Initialize resources */ }
  void OnPause(Application app) { /* Pause animations, save state */ }
  void OnResume(Application app) { /* Resume animations */ }
  void OnTerminate(Application app) { /* Clean up resources */ }

private:
  Application& mApplication;
};
```

### Quitting and Lowering

Use `Quit()` to terminate the application completely, or `Lower()` to minimize it to the background:

```cpp
void OnKeyEvent(Window window, KeyEvent event)
{
  if (event.GetState() == KeyEvent::DOWN)
  {
    if (IsKey(event, Dali::DALI_KEY_ESCAPE) || IsKey(event, Dali::DALI_KEY_BACK))
    {
      mApplication.Quit();  // Fully terminate
      // Or: mApplication.Lower();  // Send to background
    }
  }
}
```

### UI Thread Mode

For applications that need to handle system events without blocking UI, enable UI thread mode:

```cpp
WindowData windowData;
windowData.SetTransparency(true);
Application application = Application::New(&argc, &argv, "", true, windowData);
```

When UI thread mode is enabled, task signals (`TaskInitSignal()`, `TaskTerminateSignal()`, etc.) are emitted on the main thread while regular signals are emitted on the UI thread.

## Window Management

The `Window` class represents a drawable surface. DALi creates a default main window accessible via `Application::GetWindow()`.

### Accessing the Main Window

```cpp
void OnInitialize(Application application)
{
  Window window = application.GetWindow();
  window.SetBackgroundColor(Color::WHITE);
}
```

### Adding Views to a Window

Add `View` objects to the window to build your UI:

```cpp
void OnInitialize(Application application)
{
  Window window = application.GetWindow();
  
  View rootView = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT);
  
  window.Add(rootView);
}
```

### Creating Additional Windows

Create secondary windows with `Window::New()`:

```cpp
void CreateSecondaryWindow()
{
  WindowData windowData;
  windowData.SetTransparency(true);
  windowData.SetWindowType(WindowType::NORMAL);
  
  Window secondaryWindow = Window::New("Overlay", "MyOverlayClass", windowData);
  secondaryWindow.SetBackgroundColor(Vector4(0.0f, 0.0f, 0.0f, 0.5f));
  secondaryWindow.Show();
}
```

### Window Properties

Configure window appearance and behavior:

```cpp
Window window = application.GetWindow();

// Background color
window.SetBackgroundColor(Vector4(0.9f, 0.9f, 0.9f, 1.0f));

// Size and position
window.SetSize(Window::WindowSize(800, 600));
window.SetPosition(Window::WindowPosition(100, 100));

// Focus handling
window.SetAcceptFocus(true);

// Visibility
window.Show();
bool visible = window.IsVisible();
```

### Window Signals

Handle window events through signals:

```cpp
class MyWindowHandler : public ConnectionTracker
{
public:
  void ConnectWindowSignals(Window window)
  {
    window.FocusChangeSignal().Connect(this, &MyWindowHandler::OnFocusChange);
    window.ResizeSignal().Connect(this, &MyWindowHandler::OnResize);
    window.KeyEventSignal().Connect(this, &MyWindowHandler::OnKeyEvent);
  }

  void OnFocusChange(Window window, bool focusIn)
  {
    if (focusIn) {
      // Window gained focus
    } else {
      // Window lost focus
    }
  }

  void OnResize(Window window, Window::WindowSize size)
  {
    int width = size.GetX();
    int height = size.GetY();
    // Handle resize
  }

  void OnKeyEvent(Window window, KeyEvent event)
  {
    if (event.GetState() == KeyEvent::DOWN) {
      // Handle key press
    }
  }
};
```

### Orientation

Control window orientation with `AddAvailableOrientation()` and `SetPreferredOrientation()`:

```cpp
Window window = application.GetWindow();
window.AddAvailableOrientation(WindowOrientation::PORTRAIT);
window.AddAvailableOrientation(WindowOrientation::LANDSCAPE);
window.SetPreferredOrientation(WindowOrientation::PORTRAIT);
```

## Timer and Periodic Events

The `Timer` class provides periodic callbacks for animations, polling, or scheduled updates.

### Creating a Timer

Create a timer with a specified interval in milliseconds:

```cpp
Timer timer = Timer::New(1000);  // 1 second interval
timer.TickSignal().Connect(this, &MyClass::OnTimerTick);
timer.Start();
```

### Timer Callback

Return `true` from the callback to continue the timer, or `false` to stop it:

```cpp
bool OnTimerTick()
{
  // Update animation frame, check status, etc.
  return true;  // Continue timer
}
```

### Controlling the Timer

```cpp
Timer timer = Timer::New(500);

timer.Start();   // Start or restart the timer
timer.Stop();    // Stop the timer
timer.Pause();   // Pause the timer
timer.Resume();  // Resume from pause

bool running = timer.IsRunning();
uint32_t interval = timer.GetInterval();
timer.SetInterval(1000);  // Set new interval and restart
```

### One-Shot Timer

For a single delayed callback, stop the timer after the first tick:

```cpp
Timer oneShot = Timer::New(2000);
oneShot.TickSignal().Connect(this, &MyClass::OnOneShot);
oneShot.Start();

// In callback:
bool OnOneShot()
{
  DoDelayedAction();
  return false;  // Stop after first execution
}
```

## Device Status Monitoring

The `Application` class emits signals for device-level events such as low battery, low memory, and orientation changes.

### Battery Status

Connect to `LowBatterySignal()` to respond to battery level changes:

```cpp
mApplication.LowBatterySignal().Connect(this, &MyApp::OnLowBattery);

void OnLowBattery(DeviceStatus::Battery::Status status)
{
  switch (status)
  {
    case DeviceStatus::Battery::NORMAL:
      // Battery above 5%
      break;
    case DeviceStatus::Battery::CRITICALLY_LOW:
      // Battery below 5%, reduce activity
      break;
    case DeviceStatus::Battery::POWER_OFF:
      // Battery below 1%, prepare for shutdown
      break;
  }
}
```

### Memory Status

Connect to `LowMemorySignal()` to release resources when memory is low:

```cpp
mApplication.LowMemorySignal().Connect(this, &MyApp::OnLowMemory);

void OnLowMemory(DeviceStatus::Memory::Status status)
{
  switch (status)
  {
    case DeviceStatus::Memory::NORMAL:
      // Normal operation
      break;
    case DeviceStatus::Memory::LOW:
      // Release non-essential caches
      ClearImageCaches();
      break;
    case DeviceStatus::Memory::CRITICALLY_LOW:
      // Release all possible resources
      ClearAllCaches();
      ReduceTextureQuality();
      break;
  }
}
```

### Device Orientation

Connect to `DeviceOrientationChangedSignal()` to respond to physical device rotation:

```cpp
mApplication.DeviceOrientationChangedSignal().Connect(this, &MyApp::OnOrientationChanged);

void OnOrientationChanged(DeviceStatus::Orientation::Status orientation)
{
  switch (orientation)
  {
    case DeviceStatus::Orientation::ORIENTATION_0:
      // Device in natural position
      break;
    case DeviceStatus::Orientation::ORIENTATION_90:
      // Device's left side is at the top
      break;
    case DeviceStatus::Orientation::ORIENTATION_180:
      // Device upside down
      break;
    case DeviceStatus::Orientation::ORIENTATION_270:
      // Device's right side is at the top
      break;
  }
}
```

### Locale Changes

Handle language and region changes:

```cpp
mApplication.LanguageChangedSignal().Connect(this, &MyApp::OnLanguageChanged);
mApplication.RegionChangedSignal().Connect(this, &MyApp::OnRegionChanged);

void OnLanguageChanged(Application app)
{
  Dali::String language = app.GetLanguage();
  ReloadLocalizedStrings(language);
}

void OnRegionChanged(Application app)
{
  Dali::String region = app.GetRegion();
  UpdateRegionalSettings(region);
}
```
