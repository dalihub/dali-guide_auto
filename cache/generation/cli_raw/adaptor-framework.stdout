---
title: Adaptor Framework
sidebar_label: Adaptor Framework
category: application-framework
---

# Adaptor Framework

`Dali::Application` starts the DALi event loop, exposes application lifecycle signals, and gives a dali-ui app the window where its `Dali::Ui::View` tree is attached.

## Table of Contents

- [Starting a dali-ui Application](#starting-a-dali-ui-application)
- [Creating the Initial View Tree](#creating-the-initial-view-tree)
- [Handling Lifecycle and Device State](#handling-lifecycle-and-device-state)
- [Using Window Services from the Application](#using-window-services-from-the-application)
- [Accessing Application Environment Data](#accessing-application-environment-data)

## Starting a dali-ui Application

Every dali-ui application creates one `Dali::Application`, connects to `InitSignal()`, and then enters `MainLoop()`. Create `Dali::Ui::View` objects from the init callback, not before the application has initialized.

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
    window.SetBackgroundColor(Vector4(1.0f, 1.0f, 1.0f, 1.0f));

    View root = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(UiColor(0xF7F8FA));

    window.Add(root);
    mRoot = root;
  }

private:
  Application& mApplication;
  View         mRoot;
};

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);
  GuideApp controller(application);
  application.MainLoop();
  return 0;
}
```

`Application::New()` consumes DALi-supported command-line options such as width, height, and DPI options. `MainLoop()` blocks until the application is lowered or quit through APIs such as `Quit()`.

## Creating the Initial View Tree

Use `Dali::Ui::View` as the application-facing object model. The adaptor framework supplies the initialized `Application` and main window; dali-ui supplies the view tree that your app owns.

```cpp
void BuildContent(Application application)
{
  auto window = application.GetWindow();

  View root = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT)
    .SetBackgroundColor(UiColor(0xFFFFFF));

  View header = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(72.0f)
    .SetBackgroundColor(UiColor(0x1F2937));

  View content = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT)
    .SetBackgroundColor(UiColor(0xEEF2F7));

  root.Add(header);
  root.Add(content);

  window.Add(root);
}
```

For app-level code, keep layout and visual state on `View` through typed setters such as `SetRequestedWidth()`, `SetRequestedHeight()`, and `SetBackgroundColor()`. The window is only the scene entry point; the actual UI should remain a `View` hierarchy.

## Handling Lifecycle and Device State

`Dali::Application` owns lifecycle signals such as `InitSignal()`, `PauseSignal()`, `ResumeSignal()`, `TerminateSignal()`, and `ResetSignal()`. These callbacks receive the `Application` handle, so they can update retained `View` handles or query application state as needed.

```cpp
class LifecycleAwareApp : public ConnectionTracker
{
public:
  explicit LifecycleAwareApp(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &LifecycleAwareApp::OnInit);
    mApplication.PauseSignal().Connect(this, &LifecycleAwareApp::OnPause);
    mApplication.ResumeSignal().Connect(this, &LifecycleAwareApp::OnResume);
    mApplication.LowMemorySignal().Connect(this, &LifecycleAwareApp::OnLowMemory);
  }

  void OnInit(Application application)
  {
    auto window = application.GetWindow();

    mRoot = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(UiColor(0xFFFFFF));

    window.Add(mRoot);
  }

  void OnPause(Application)
  {
    mRoot.SetOpacity(0.6f);
  }

  void OnResume(Application)
  {
    mRoot.SetOpacity(1.0f);
  }

  void OnLowMemory(DeviceStatus::Memory::Status status)
  {
    if(status == DeviceStatus::Memory::CRITICALLY_LOW)
    {
      mRoot.RemoveAllChildren();
    }
  }

private:
  Application& mApplication;
  View         mRoot;
};
```

Use `LowBatterySignal()`, `LowMemorySignal()`, and `DeviceOrientationChangedSignal()` for device-state responses that affect UI. Their callback parameter types are owned by the adaptor framework: `DeviceStatus::Battery::Status`, `DeviceStatus::Memory::Status`, and `DeviceStatus::Orientation::Status`.

## Using Window Services from the Application

`Application::GetWindow()` returns the main window after initialization. In a dali-ui app, use it for application shell concerns such as background color, visibility, focus acceptance, and attaching the root `View`.

```cpp
class WindowShell : public ConnectionTracker
{
public:
  explicit WindowShell(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &WindowShell::OnInit);
  }

  void OnInit(Application application)
  {
    auto window = application.GetWindow();

    window.SetBackgroundColor(Vector4(0.0f, 0.0f, 0.0f, 1.0f));
    window.SetAcceptFocus(true);
    window.Show();

    View root = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(UiColor(0x101820));

    window.Add(root);
    mRoot = root;
  }

private:
  Application& mApplication;
  View         mRoot;
};
```

Window signals are also useful at the app shell boundary. For example, `FocusChangeSignal()` reports whether the window gained or lost focus, and `ResizeSignal()` reports the new `Window::WindowSize`.

```cpp
class WindowSignals : public ConnectionTracker
{
public:
  explicit WindowSignals(Application& application)
  {
    application.InitSignal().Connect(this, &WindowSignals::OnInit);
  }

  void OnInit(Application application)
  {
    auto window = application.GetWindow();

    mRoot = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT);

    window.Add(mRoot);
    window.FocusChangeSignal().Connect(this, &WindowSignals::OnFocusChanged);
    window.ResizeSignal().Connect(this, &WindowSignals::OnResized);
  }

  void OnFocusChanged(Window, bool focusIn)
  {
    mRoot.SetOpacity(focusIn ? 1.0f : 0.5f);
  }

  void OnResized(Window, Window::WindowSize)
  {
    mRoot.InvalidateMeasure();
    mRoot.InvalidateArrange();
  }

private:
  View mRoot;
};
```

## Accessing Application Environment Data

`Dali::Application` also provides environment information that is useful when building a dali-ui app shell. `GetResourcePath()` is static and can be used to locate bundled resources. `GetLanguage()` and `GetRegion()` return the current device language and region from an initialized application.

```cpp
class EnvironmentAwareApp : public ConnectionTracker
{
public:
  explicit EnvironmentAwareApp(Application& application)
  : mApplication(application)
  {
    mApplication.InitSignal().Connect(this, &EnvironmentAwareApp::OnInit);
    mApplication.LanguageChangedSignal().Connect(this, &EnvironmentAwareApp::OnLanguageChanged);
    mApplication.RegionChangedSignal().Connect(this, &EnvironmentAwareApp::OnRegionChanged);
  }

  void OnInit(Application application)
  {
    auto resourcePath = Application::GetResourcePath();
    auto language     = application.GetLanguage();
    auto region       = application.GetRegion();

    auto window = application.GetWindow();
    mRoot = View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(UiColor(0xFFFFFF));

    window.Add(mRoot);

    UpdateLocalizedUi(language, region, resourcePath);
  }

  void OnLanguageChanged(Application application)
  {
    UpdateLocalizedUi(application.GetLanguage(), application.GetRegion(), Application::GetResourcePath());
  }

  void OnRegionChanged(Application application)
  {
    UpdateLocalizedUi(application.GetLanguage(), application.GetRegion(), Application::GetResourcePath());
  }

  void UpdateLocalizedUi(const String&, const String&, const String&)
  {
    mRoot.InvalidateMeasure();
  }

private:
  Application& mApplication;
  View         mRoot;
};
```

Keep environment handling separate from view construction: `Application` owns the process and device-facing information, while `Dali::Ui::View` owns the app-facing UI structure and visual state.
