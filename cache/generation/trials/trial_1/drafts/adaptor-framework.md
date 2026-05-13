---
title: Adaptor Framework
sidebar_label: Adaptor Framework
category: uncategorized
---

# Adaptor Framework

The Adaptor Framework provides the core infrastructure for initializing, running, and managing DALi applications. It handles application lifecycle, window management, device events, and platform integration.

## Table of Contents

- [Application Lifecycle](#application-lifecycle)
- [Window Management](#window-management)
- [Device Events and Status](#device-events-and-status)
- [Alternative Application Types](#alternative-application-types)
- [Low-Level Adaptor Control](#low-level-adaptor-control)

## Application Lifecycle

The `Dali::Application` class is the primary entry point for DALi applications. It initializes the DALi core, creates the main window, and provides signals for lifecycle events.

### Creating an Application

Create an `Application` instance in your main function and connect to the `InitSignal()` to set up your UI:

```cpp
#include <dali/public-api/adaptor-framework/application.h>
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
    View rootView = View::New();
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

Connect to lifecycle signals to handle application state changes:

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

  void OnInit(Application application)
  {
    // Initialize resources and create UI
  }

  void OnPause(Application application)
  {
    // Save state, release resources
  }

  void OnResume(Application application)
  {
    // Restore resources, update UI
  }

  void OnTerminate(Application application)
  {
    // Clean up resources
  }

private:
  Application& mApplication;
};
```

### Quitting the Application

Use `Quit()` to exit the application completely:

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

Use `Lower()` to minimize the application without fully quitting, which improves restart performance on Tizen:

```cpp
void MinimizeApp()
{
  mApplication.Lower();
}
```

### UI Thread Mode

Enable UI thread mode to separate UI event processing from system events. This prevents UI blocking during heavy system operations:

```cpp
int main(int argc, char** argv)
{
  WindowData windowData;
  // Configure windowData as needed

  Application application = Application::New(
    &argc, &argv,
    "",           // stylesheet
    true,         // useUiThread = true
    windowData
  );

  // With UI thread enabled:
  // - Normal signals (Init, Pause, Resume, etc.) emit on UI thread
  // - Task signals (TaskInit, TaskAppControl, etc.) emit on main thread

  MyAppController controller(application);
  application.MainLoop();
  return 0;
}
```

### LifecycleController

Use `LifecycleController` to receive lifecycle events from any part of your code without direct access to the `Application` instance:

```cpp
#include <dali/devel-api/adaptor-framework/lifecycle-controller.h>

class MyManager
{
public:
  MyManager()
  {
    LifecycleController controller = LifecycleController::Get();
    controller.InitSignal().Connect(this, &MyManager::OnAppInit);
    controller.LanguageChangedSignal().Connect(this, &MyManager::OnLanguageChanged);
  }

  void OnAppInit()
  {
    // Initialize manager resources
  }

  void OnLanguageChanged()
  {
    // Update localized strings
  }
};
```

## Window Management

The `Dali::Window` class represents a native window and provides methods for adding content, handling input, and configuring window properties.

### Getting the Main Window

Retrieve the main window from the application:

```cpp
void OnInit(Application application)
{
  Window window = application.GetWindow();
  window.SetBackgroundColor(Color::WHITE);

  // Add content to window
  View rootView = View::New();
  window.Add(rootView);
}
```

### Adding Views to a Window

Add `Dali::Ui::View` objects to the window. Since `View` inherits from `Actor`, you can add views directly:

```cpp
void CreateContent(Window window)
{
  // Create a root layout
  AbsoluteLayout rootLayout = AbsoluteLayout::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT);

  // Add child views
  View redBox = View::New();
  redBox.SetBackgroundColor(Color::RED);
  redBox.SetLayoutParams(
    AbsoluteLayoutParams::New().SetBounds(LayoutRect(50.0f, 50.0f, 100.0f, 100.0f))
  );
  rootLayout.Add(redBox);

  window.Add(rootLayout);
}
```

### Window Background Color

Set the window background color:

```cpp
window.SetBackgroundColor(Vector4(0.9f, 0.9f, 0.9f, 1.0f)); // Light gray
```

### Handling Key Events

Connect to `KeyEventSignal()` to handle keyboard input:

```cpp
class MyAppController : public ConnectionTracker
{
public:
  void OnInit(Application application)
  {
    Window window = application.GetWindow();
    window.KeyEventSignal().Connect(this, &MyAppController::OnKeyEvent);
  }

  void OnKeyEvent(Window window, KeyEvent event)
  {
    if (event.GetState() == KeyEvent::DOWN)
    {
      if (IsKey(event, Dali::DALI_KEY_ESCAPE))
      {
        mApplication.Quit();
      }
    }
  }
};
```

### Handling Touch Events

Connect to `TouchedSignal()` for touch events:

```cpp
void OnInit(Application application)
{
  Window window = application.GetWindow();
  window.TouchedSignal().Connect(this, &MyAppController::OnTouch);
}

void OnTouch(Window window, TouchEvent event)
{
  if (event.GetPointCount() > 0)
  {
    Vector2 point = event.GetPoint(0).GetLocalPosition();
    // Handle touch at point
  }
}
```

### Window Focus and Resize

Handle window focus and resize events:

```cpp
void OnInit(Application application)
{
  Window window = application.GetWindow();
  window.FocusChangeSignal().Connect(this, &MyAppController::OnFocusChange);
  window.ResizeSignal().Connect(this, &MyAppController::OnResize);
}

void OnFocusChange(Window window, bool focusIn)
{
  if (focusIn)
  {
    // Window gained focus
  }
  else
  {
    // Window lost focus
  }
}

void OnResize(Window window, Window::WindowSize size)
{
  int width = size.GetWidth();
  int height = size.GetHeight();
  // Adjust layout for new size
}
```

### Window Orientation

Configure supported orientations and preferred orientation:

```cpp
void ConfigureOrientation(Window window)
{
  // Add supported orientations
  window.AddAvailableOrientation(WindowOrientation::PORTRAIT);
  window.AddAvailableOrientation(WindowOrientation::LANDSCAPE);
  window.AddAvailableOrientation(WindowOrientation::PORTRAIT_INVERSE);
  window.AddAvailableOrientation(WindowOrientation::LANDSCAPE_INVERSE);

  // Set preferred orientation
  window.SetPreferredOrientation(WindowOrientation::PORTRAIT);
}
```

### Creating Additional Windows

Create additional windows for multi-window scenarios:

```cpp
Window CreateSecondaryWindow()
{
  PositionSize positionSize(100, 100, 400, 300);
  Window secondaryWindow = Window::New(positionSize, "SecondaryWindow");
  secondaryWindow.SetBackgroundColor(Color::WHITE);
  secondaryWindow.Show();
  return secondaryWindow;
}
```

## Device Events and Status

The Adaptor Framework provides signals for device-level events such as low memory, low battery, and orientation changes.

### Low Memory and Low Battery

Handle system resource warnings:

```cpp
class MyAppController : public ConnectionTracker
{
public:
  MyAppController(Application& application)
    : mApplication(application)
  {
    mApplication.LowMemorySignal().Connect(this, &MyAppController::OnLowMemory);
    mApplication.LowBatterySignal().Connect(this, &MyAppController::OnLowBattery);
  }

  void OnLowMemory(DeviceStatus::Memory::Status status)
  {
    switch (status)
    {
      case DeviceStatus::Memory::LOW:
        // Release non-essential resources
        break;
      case DeviceStatus::Memory::CRITICALLY_LOW:
        // Release all possible resources
        break;
      default:
        break;
    }
  }

  void OnLowBattery(DeviceStatus::Battery::Status status)
  {
    switch (status)
    {
      case DeviceStatus::Battery::CRITICALLY_LOW:
        // Save state, reduce activity
        break;
      case DeviceStatus::Battery::POWER_OFF:
        // Emergency save and prepare for shutdown
        break;
      default:
        break;
    }
  }
};
```

### Device Orientation Changes

Handle device orientation changes:

```cpp
class MyAppController : public ConnectionTracker
{
public:
  MyAppController(Application& application)
    : mApplication(application)
  {
    mApplication.DeviceOrientationChangedSignal().Connect(
      this, &MyAppController::OnDeviceOrientationChanged
    );
  }

  void OnDeviceOrientationChanged(DeviceStatus::Orientation::Status status)
  {
    switch (status)
    {
      case DeviceStatus::Orientation::ORIENTATION_0:
        // Portrait
        break;
      case DeviceStatus::Orientation::ORIENTATION_90:
        // Landscape (rotated clockwise)
        break;
      case DeviceStatus::Orientation::ORIENTATION_180:
        // Portrait inverse
        break;
      case DeviceStatus::Orientation::ORIENTATION_270:
        // Landscape (rotated counter-clockwise)
        break;
    }
  }
};
```

### Language and Region Changes

Handle locale changes:

```cpp
class MyAppController : public ConnectionTracker
{
public:
  MyAppController(Application& application)
    : mApplication(application)
  {
    mApplication.LanguageChangedSignal().Connect(this, &MyAppController::OnLanguageChanged);
    mApplication.RegionChangedSignal().Connect(this, &MyAppController::OnRegionChanged);
  }

  void OnLanguageChanged(Application application)
  {
    Dali::String language = application.GetLanguage();
    // Reload localized strings
  }

  void OnRegionChanged(Application application)
  {
    Dali::String region = application.GetRegion();
    // Update region-specific content
  }
};
```

## Alternative Application Types

### ComponentApplication

Use `ComponentApplication` for component-based applications that can host multiple UI components in a single process:

```cpp
#include <dali/devel-api/adaptor-framework/component-application.h>

class MyComponentController : public ConnectionTracker
{
public:
  MyComponentController(ComponentApplication& application)
    : mApplication(application)
  {
    mApplication.CreateSignal().Connect(this, &MyComponentController::OnCreate);
    mApplication.InitSignal().Connect(this, &MyComponentController::OnInit);
  }

  Any OnCreate()
  {
    // Return component data
    return Any();
  }

  void OnInit(Application application)
  {
    Window window = application.GetWindow();
    // Initialize component UI
  }

private:
  ComponentApplication& mApplication;
};

int main(int argc, char** argv)
{
  ComponentApplication application = ComponentApplication::New(&argc, &argv);
  MyComponentController controller(application);
  application.MainLoop();
  return 0;
}
```

### OffscreenApplication

Use `OffscreenApplication` for rendering without a visible window, useful for automated testing or image generation:

```cpp
#include <dali/devel-api/adaptor-framework/offscreen-application.h>

int main(int argc, char** argv)
{
  OffscreenApplication application = OffscreenApplication::New(
    &argc, &argv,
    OffscreenApplication::FrameworkBackend::ECORE,
    OffscreenApplication::RenderMode::MANUAL
  );

  application.Start();

  OffscreenWindow window = application.GetWindow();
  // Add content to offscreen window

  // Render frames manually
  for (int i = 0; i < 10; ++i)
  {
    application.RenderOnce();
    // Capture or process frame
  }

  application.Terminate();
  return 0;
}
```

## Low-Level Adaptor Control

For advanced use cases where you need to control the main loop yourself, use `Dali::Adaptor` directly.

### Creating an Adaptor

```cpp
#include <dali/integration-api/adaptor-framework/adaptor.h>

int main()
{
  // Create a window
  PositionSize positionSize(0, 0, 800, 1280);
  Window window = Window::New(positionSize, "MyApp");

  // Create adaptor with the window
  Adaptor& adaptor = Adaptor::New(window);
  adaptor.Start();

  // Create your DALi content
  // ...

  // Run your own main loop
  MyPlatform::RunMainLoop();

  return 0;
}
```

### Adaptor Lifecycle

Control adaptor state manually:

```cpp
Adaptor& adaptor = Adaptor::New(window);

adaptor.Start();   // Begin rendering
adaptor.Pause();   // Pause rendering
adaptor.Resume();  // Resume rendering
adaptor.Stop();    // Stop rendering
```

### Feeding Events

Feed touch and key events to DALi:

```cpp
void OnPlatformTouchEvent(int x, int y, int type, int timestamp)
{
  TouchPoint point;
  point.x = x;
  point.y = y;
  // Configure point...

  Adaptor::Get().FeedTouchPoint(point, timestamp);
}

void OnPlatformKeyEvent(int keyCode, int type)
{
  KeyEvent event;
  // Configure event...

  Adaptor::Get().FeedKeyEvent(event);
}
```

### Idle Callbacks

Schedule work to run when the main loop is idle:

```cpp
void ScheduleIdleWork()
{
  CallbackBase* callback = MakeCallback(nullptr, &DoIdleWork);
  Adaptor::Get().AddIdle(callback, false);
}

void DoIdleWork()
{
  // This runs when the main loop is idle
}
```

### Rendering Control

Control rendering behavior:

```cpp
// Reduce frame rate for power saving
Adaptor::Get().SetRenderRefreshRate(2);  // Render every other frame

// Set maximum frame rate
Adaptor::Get().SetMaximumRenderFrameRate(30.0f);

// Force a single render
Adaptor::Get().RenderOnce();
```
