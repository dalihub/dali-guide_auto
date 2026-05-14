---
title: Adaptor Framework
sidebar_label: Adaptor Framework
category: application-framework
---

# Adaptor Framework

The adaptor framework starts a DALi application, delivers lifecycle and system events, and provides the main platform window that hosts your dali-ui `Dali::Ui::View` tree.

## Table of Contents

- [Starting a dali-ui Application](#starting-a-dali-ui-application)
- [Building the Initial View Tree on Init](#building-the-initial-view-tree-on-init)
- [Handling Lifecycle and System Events](#handling-lifecycle-and-system-events)
- [Quitting or Lowering the Application](#quitting-or-lowering-the-application)
- [Posting Work Back to the Event Thread](#posting-work-back-to-the-event-thread)
- [Advanced Embedding with ApplicationController](#advanced-embedding-with-applicationcontroller)

## Starting a dali-ui Application

`Dali::Application` is the normal entry point for a dali-ui application. Create it in `main()`, connect to `InitSignal()`, then enter `MainLoop()`. Build UI from `InitSignal()`, because this is the application initialization signal and the usual point to retrieve the main window with `GetWindow()`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class GuideApp : public ConnectionTracker
{
public:
  explicit GuideApp(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &GuideApp::OnInit);
  }

  void OnInit(Application application)
  {
    auto window = application.GetWindow();

    View root = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT);

    window.Add(root);
  }

private:
  Application& mApplication;
};

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);
  GuideApp guideApp(application);
  application.MainLoop();
  return 0;
}
```

`Dali::Application::New()` accepts the process argument pointers. The adaptor consumes supported DALi command-line options such as window size, DPI, and help, then updates `argc` and `argv` when those options are found. `Dali::Application::MainLoop()` starts the application main loop.

## Building the Initial View Tree on Init

In a dali-ui app, treat `Dali::Ui::View` as the app-facing object model. The window is the platform scene holder, while your application structure should be expressed as a `Dali::Ui::View` tree.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class MainViewController : public ConnectionTracker
{
public:
  explicit MainViewController(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &MainViewController::Create);
  }

  void Create(Application application)
  {
    auto window = application.GetWindow();

    View header = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(80.0f);

    View content = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT);

    Layout root = Layout::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .Children({
        header,
        content
      });

    window.Add(root);
  }

private:
  Application& mApplication;
};
```

`Dali::Ui::View::New()` creates an initialized view handle. Use typed dali-ui setters such as `SetRequestedWidth()`, `SetRequestedHeight()`, and `Children()` to describe layout intent. The layout system computes the final rendered size after the view tree is added to the window.

## Handling Lifecycle and System Events

`Dali::Application` exposes lifecycle signals for common application state changes. The callback type for `InitSignal()`, `PauseSignal()`, `ResumeSignal()`, `TerminateSignal()`, `ResetSignal()`, `LanguageChangedSignal()`, and `RegionChangedSignal()` receives a `Dali::Application` handle.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class LifecycleAwareController : public ConnectionTracker
{
public:
  explicit LifecycleAwareController(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &LifecycleAwareController::OnInit);
    mApplication.PauseSignal().Connect(this, &LifecycleAwareController::OnPause);
    mApplication.ResumeSignal().Connect(this, &LifecycleAwareController::OnResume);
    mApplication.LanguageChangedSignal().Connect(this, &LifecycleAwareController::OnLanguageChanged);
  }

  void OnInit(Application application)
  {
    mRoot = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT);

    application.GetWindow().Add(mRoot);
  }

  void OnPause(Application)
  {
    if(mRoot)
    {
      mRoot.SetOpacity(0.4f);
    }
  }

  void OnResume(Application)
  {
    if(mRoot)
    {
      mRoot.SetOpacity(1.0f);
    }
  }

  void OnLanguageChanged(Application application)
  {
    Dali::String language = application.GetLanguage();
    Dali::String region = application.GetRegion();

    if(mRoot)
    {
      mRoot.InvalidateMeasure();
      mRoot.InvalidateArrange();
    }
  }

private:
  Application& mApplication;
  View mRoot;
};
```

Use `Dali::Application::GetLanguage()` and `Dali::Application::GetRegion()` when rebuilding localized UI state after `LanguageChangedSignal()` or `RegionChangedSignal()`. If existing layout depends on that state, `Dali::Ui::View::InvalidateMeasure()` and `Dali::Ui::View::InvalidateArrange()` request fresh layout work.

For low-resource events, `LowBatterySignal()` passes `Dali::DeviceStatus::Battery::Status`, and `LowMemorySignal()` passes `Dali::DeviceStatus::Memory::Status`.

```cpp
class ResourcePolicyController : public ConnectionTracker
{
public:
  explicit ResourcePolicyController(Application& application)
  {
    application.LowBatterySignal().Connect(this, &ResourcePolicyController::OnLowBattery);
    application.LowMemorySignal().Connect(this, &ResourcePolicyController::OnLowMemory);
  }

  void SetContent(View content)
  {
    mContent = content;
  }

  void OnLowBattery(DeviceStatus::Battery::Status)
  {
    if(mContent)
    {
      mContent.SetOpacity(0.8f);
    }
  }

  void OnLowMemory(DeviceStatus::Memory::Status)
  {
    if(mContent)
    {
      mContent.ClearBackground();
    }
  }

private:
  View mContent;
};
```

## Quitting or Lowering the Application

Use `Dali::Application::Quit()` when the application should leave the main loop. Use `Dali::Application::Lower()` when the app should move behind other applications without terminating.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

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
    View root = View::New()
      .SetFocusable(true)
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT);

    root.KeyEventSignal().Connect(this, &ExitController::OnKeyEvent);
    application.GetWindow().Add(root);
  }

  bool OnKeyEvent(View, KeyEvent event)
  {
    if(event.GetState() == KeyEvent::DOWN)
    {
      if(IsKey(event, Dali::DALI_KEY_ESCAPE) || IsKey(event, Dali::DALI_KEY_BACK))
      {
        mApplication.Quit();
        return true;
      }
    }

    return false;
  }

private:
  Application& mApplication;
};
```

`Dali::Ui::View::KeyEventSignal()` callbacks return `true` to consume the event and `false` to allow propagation. This keeps key handling in the view tree instead of using a raw actor event pattern.

## Posting Work Back to the Event Thread

`Dali::EventThreadCallback` lets worker-side code request execution of a callback on the main event thread. Create the `Dali::EventThreadCallback` on the main thread, keep it alive for as long as worker code may call it, and call `Trigger()` from the worker side when UI work must be scheduled back to DALi.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>
#include <dali/devel-api/adaptor-framework/event-thread-callback.h>

using namespace Dali;
using namespace Dali::Ui;

class AsyncUiBridge
{
public:
  explicit AsyncUiBridge(View statusView)
  : mStatusView(statusView),
    mEventCallback(MakeCallback(this, &AsyncUiBridge::ApplyResultOnEventThread))
  {
  }

  void NotifyResultReady()
  {
    mEventCallback.Trigger();
  }

  void ApplyResultOnEventThread()
  {
    if(mStatusView)
    {
      mStatusView.SetOpacity(1.0f);
      mStatusView.InvalidateMeasure();
    }
  }

private:
  View mStatusView;
  EventThreadCallback mEventCallback;
};
```

`Dali::EventThreadCallback::Trigger()` is the cross-thread notification point. Keep dali-ui view mutation, such as `SetOpacity()` and `InvalidateMeasure()`, in the event-thread callback body.

## Advanced Embedding with ApplicationController

Most applications should use `Dali::Application`. `Dali::ApplicationController` is an integration-level entry point for hosts that drive the lifecycle themselves and need explicit pre/post lifecycle calls.

```cpp
#include <dali/integration-api/adaptor-framework/application-controller.h>

using namespace Dali;

class EmbeddedHost
{
public:
  EmbeddedHost()
  : mController(ApplicationController::New(PositionSize(0, 0, 1280, 720)))
  {
  }

  void Start()
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

  void Stop()
  {
    mController.PreTerminate();
    mController.PostTerminate();
  }

private:
  ApplicationController mController;
};
```

Call `PreInitialize()` during startup to initialize the window and adaptor, then call `PostInitialize()` to complete initialization. Pair `PrePause()` with `PostPause()`, `PreResume()` with `PostResume()`, and `PreTerminate()` with `PostTerminate()` so the adaptor lifecycle remains ordered.
