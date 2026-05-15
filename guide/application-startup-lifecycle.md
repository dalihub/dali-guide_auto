---
title: Application Startup and Lifecycle
sidebar_label: Application Startup and Lifecycle
category: application-framework
---

# Application Startup and Lifecycle

Create a `Dali::Application`, build your `Dali::Ui::View` tree from `InitSignal()`, then run the main loop and respond to lifecycle signals.

## Table of Contents

- [Start a dali-ui Application](#start-a-dali-ui-application)
- [Create the Root View Tree](#create-the-root-view-tree)
- [Handle Lifecycle and System Events](#handle-lifecycle-and-system-events)
- [Lower or Quit the Application](#lower-or-quit-the-application)
- [Post Worker Results to the Event Thread](#post-worker-results-to-the-event-thread)
- [Advanced Host Lifecycle Control](#advanced-host-lifecycle-control)

## Start a dali-ui Application

A normal dali-ui executable starts with `Dali::Application::New()`, connects `InitSignal()`, and enters `MainLoop()`. Pass `&argc` and `&argv` so DALi can consume supported runtime options and update the remaining argument list.

Create dali-ui objects after `InitSignal()` is emitted. This keeps startup aligned with DALi initialization and follows the same point where DALi samples retrieve the application window with `GetWindow()`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class StartupController : public ConnectionTracker
{
public:
  explicit StartupController(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &StartupController::OnInit);
  }

  void OnInit(Application application)
  {
    Window window = application.GetWindow();
    window.SetBackgroundColor(Color::WHITE);

    View root = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(Color::WHITE);

    window.Add(root);
    mRoot = root;
  }

private:
  Application& mApplication;
  View mRoot;
};

int DALI_EXPORT_API main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);
  StartupController controller(application);
  application.MainLoop();
  return 0;
}
```

`Application::New()` also accepts an optional stylesheet path:

```cpp
Application application = Application::New(&argc, &argv, "application-theme.json");
```

## Create the Root View Tree

In a dali-ui application, use `Dali::Ui::View` as the app-facing object model. A full-screen root should normally use `MATCH_PARENT` for both requested dimensions, then add child `View` objects to form the UI tree.

```cpp
class ViewTreeController : public ConnectionTracker
{
public:
  explicit ViewTreeController(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &ViewTreeController::OnInit);
  }

  void OnInit(Application application)
  {
    Window window = application.GetWindow();

    View root = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(Color::WHITE);

    View content = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(Color::BLUE);

    root.Add(content);
    window.Add(root);

    mRoot = root;
    mContent = content;
  }

private:
  Application& mApplication;
  View mRoot;
  View mContent;
};
```

Keep `View` handles as members when later lifecycle callbacks need to adjust the same UI objects. For example, a controller can fade a retained root view during pause and restore it on resume.

```cpp
void SetForegroundVisualState(bool foreground)
{
  if(mRoot)
  {
    mRoot.SetOpacity(foreground ? 1.0f : 0.35f);
  }
}
```

## Handle Lifecycle and System Events

`Dali::Application` provides lifecycle signals for application state and system signals for device conditions. `PauseSignal()`, `ResumeSignal()`, `TerminateSignal()`, and `ResetSignal()` use the `Application` callback signature. `LanguageChangedSignal()` and `RegionChangedSignal()` also pass the `Application`, so use `GetLanguage()` and `GetRegion()` inside the callback to read the current values.

`LowBatterySignal()` passes `Dali::DeviceStatus::Battery::Status`. `LowMemorySignal()` passes `Dali::DeviceStatus::Memory::Status`.

```cpp
class LifecycleController : public ConnectionTracker
{
public:
  explicit LifecycleController(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &LifecycleController::OnInit);
    mApplication.PauseSignal().Connect(this, &LifecycleController::OnPause);
    mApplication.ResumeSignal().Connect(this, &LifecycleController::OnResume);
    mApplication.TerminateSignal().Connect(this, &LifecycleController::OnTerminate);
    mApplication.ResetSignal().Connect(this, &LifecycleController::OnReset);
    mApplication.LanguageChangedSignal().Connect(this, &LifecycleController::OnLanguageChanged);
    mApplication.RegionChangedSignal().Connect(this, &LifecycleController::OnRegionChanged);
    mApplication.LowBatterySignal().Connect(this, &LifecycleController::OnLowBattery);
    mApplication.LowMemorySignal().Connect(this, &LifecycleController::OnLowMemory);
  }

  void OnInit(Application application)
  {
    Window window = application.GetWindow();

    mRoot = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(Color::WHITE);

    window.Add(mRoot);
    mLanguage = application.GetLanguage();
    mRegion = application.GetRegion();
  }

  void OnPause(Application)
  {
    if(mRoot)
    {
      mRoot.SetOpacity(0.35f);
    }
  }

  void OnResume(Application)
  {
    if(mRoot)
    {
      mRoot.SetOpacity(1.0f);
      mRoot.InvalidateMeasure();
    }
  }

  void OnTerminate(Application)
  {
    mRoot.Reset();
  }

  void OnReset(Application application)
  {
    mLanguage = application.GetLanguage();
    mRegion = application.GetRegion();

    if(mRoot)
    {
      mRoot.InvalidateMeasure();
    }
  }

  void OnLanguageChanged(Application application)
  {
    mLanguage = application.GetLanguage();

    if(mRoot)
    {
      mRoot.InvalidateMeasure();
    }
  }

  void OnRegionChanged(Application application)
  {
    mRegion = application.GetRegion();

    if(mRoot)
    {
      mRoot.InvalidateMeasure();
    }
  }

  void OnLowBattery(DeviceStatus::Battery::Status status)
  {
    if(status == DeviceStatus::Battery::CRITICALLY_LOW && mRoot)
    {
      mRoot.SetOpacity(0.6f);
    }
  }

  void OnLowMemory(DeviceStatus::Memory::Status status)
  {
    if(status == DeviceStatus::Memory::LOW ||
       status == DeviceStatus::Memory::CRITICALLY_LOW)
    {
      if(mRoot)
      {
        mRoot.InvalidateMeasure();
      }
    }
  }

private:
  Application& mApplication;
  View mRoot;
  Dali::String mLanguage;
  Dali::String mRegion;
};
```

Use the normal application signals when you need to touch windows or `View` objects. The `Application` header documents normal signals as UI-thread callbacks when `useUiThread` is enabled, and as main-thread callbacks otherwise.

## Lower or Quit the Application

Use `Lower()` when the application should move to the bottom of the application stack without ending the process. Use `Quit()` when the application should leave `MainLoop()` so the executable can finish.

```cpp
class ExitController : public ConnectionTracker
{
public:
  explicit ExitController(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &ExitController::OnInit);
  }

  void OnInit(Application application)
  {
    Window window = application.GetWindow();

    mRoot = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(Color::WHITE);

    window.Add(mRoot);
  }

  void MoveToBackground()
  {
    mApplication.Lower();
  }

  void ExitCompletely()
  {
    mApplication.Quit();
  }

private:
  Application& mApplication;
  View mRoot;
};
```

For platform back or escape handling, call the same controller methods from your input callback. The lifecycle choice remains the same: `Lower()` preserves the running application, while `Quit()` exits the application loop.

## Post Worker Results to the Event Thread

`Dali::EventThreadCallback` lets a worker thread notify the main event thread. Create the `EventThreadCallback` on the main thread, keep it alive while workers may trigger it, and mutate `Dali::Ui::View` objects only from the event-thread callback.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>
#include <dali/devel-api/adaptor-framework/event-thread-callback.h>

#include <memory>
#include <mutex>
#include <thread>

using namespace Dali;
using namespace Dali::Ui;

class WorkerResultController : public ConnectionTracker
{
public:
  explicit WorkerResultController(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &WorkerResultController::OnInit);
    mApplication.TerminateSignal().Connect(this, &WorkerResultController::OnTerminate);
  }

  ~WorkerResultController()
  {
    if(mWorker.joinable())
    {
      mWorker.join();
    }
  }

  void OnInit(Application application)
  {
    Window window = application.GetWindow();

    mRoot = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(Color::WHITE);

    window.Add(mRoot);

    mEventCallback.reset(new EventThreadCallback(MakeCallback(this, &WorkerResultController::ApplyWorkerResult)));

    mWorker = std::thread([this]()
    {
      {
        std::lock_guard<std::mutex> lock(mMutex);
        mNextOpacity = 0.5f;
      }

      mEventCallback->Trigger();
    });
  }

  void ApplyWorkerResult()
  {
    float opacity = 1.0f;

    {
      std::lock_guard<std::mutex> lock(mMutex);
      opacity = mNextOpacity;
    }

    if(mRoot)
    {
      mRoot.SetOpacity(opacity);
      mRoot.InvalidateMeasure();
    }
  }

  void OnTerminate(Application)
  {
    if(mWorker.joinable())
    {
      mWorker.join();
    }

    mEventCallback.reset();
    mRoot.Reset();
  }

private:
  Application& mApplication;
  View mRoot;
  std::unique_ptr<EventThreadCallback> mEventCallback;
  std::thread mWorker;
  std::mutex mMutex;
  float mNextOpacity{1.0f};
};
```

`EventThreadCallback::Trigger()` is the worker-side notification point. The callback registered in the constructor runs on the main event thread, where it is safe to call `SetOpacity()` and `InvalidateMeasure()` on retained `View` handles.

## Advanced Host Lifecycle Control

`Dali::ApplicationController` is for host or integration scenarios that need to drive DALi lifecycle phases directly. It is not the normal startup path for application developers; most apps should use `Application::New()`, `InitSignal()`, and `MainLoop()`.

When a host owns the lifecycle, call the corresponding pre/post pair for each transition it drives. `ApplicationController` does not expose the managed `Window` through its public API, so keep UI tree creation on the host surface that your integration owns.

```cpp
#include <dali/integration-api/adaptor-framework/application-controller.h>

using namespace Dali;

class EmbeddedHost
{
public:
  EmbeddedHost()
  : mController(ApplicationController::New(PositionSize(0, 0, 720, 1280)))
  {
  }

  void Initialize()
  {
    mController.PreInitialize();
    mController.PostInitialize();
  }

  void Pause()
  {
    mController.PrePause();
    mController.PostPause();
  }

  void Resume()
  {
    mController.PreResume();
    mController.PostResume();
  }

  void Terminate()
  {
    mController.PreTerminate();
    mController.PostTerminate();
  }

private:
  ApplicationController mController;
};
```

Use this pattern only when another framework is the host and must explicitly map its own lifecycle to DALi. For a regular dali-ui executable, `Dali::Application` owns those transitions for you.
