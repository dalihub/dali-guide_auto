---
title: 어댑터 프레임워크
sidebar_label: 어댑터 프레임워크
category: application-framework
---

# 어댑터 프레임워크 {#adaptor-framework}

어댑터 프레임워크는 DALi 애플리케이션 런타임을 초기화하고, 애플리케이션 메인 루프를 제공하며, `Dali::Ui::View`를 기반으로 dali-ui 콘텐츠를 구성하는 동안 생명주기 및 시스템 이벤트를 전달합니다.

## 목차 {#table-of-contents}

- [dali-ui 애플리케이션 시작](#starting-a-dali-ui-application)
- [초기 뷰 트리 구성](#building-the-initial-view-tree)
- [생명주기 변경 처리](#handling-lifecycle-changes)
- [디바이스 상태에 대응](#reacting-to-device-status)
- [고급 어댑터 소유권](#advanced-adaptor-ownership)

## dali-ui 애플리케이션 시작 {#starting-a-dali-ui-application}

dali-ui 앱의 일반적인 진입점으로 `Dali::Application`을 사용합니다. `Dali::Application::New`로 애플리케이션을 만들고, 초기 설정 작업을 `InitSignal`에 연결한 다음 `MainLoop`로 진입합니다.

초기 DALi 및 dali-ui UI 객체는 `InitSignal` 콜백에서 생성합니다. 이 콜백은 `Dali::Application`을 전달받으며, `GetWindow`는 루트 `Dali::Ui::View`를 담을 수 있는 기본 `Dali::Window`를 반환합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void OnInit(Application application)
{
  Window window = application.GetWindow();

  View root = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT)
    .SetBackgroundColor(UiColor(0.08f, 0.10f, 0.12f, 1.0f));

  window.Add(root);
}

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);
  application.InitSignal().Connect(&OnInit);
  application.MainLoop();
  return 0;
}
```

`Dali::Application::New`는 `argc`와 `argv`를 포인터로 받습니다. 어댑터가 지원되는 DALi 명령줄 옵션을 제거하고 애플리케이션이 계속 실행되기 전에 인자 목록을 갱신할 수 있기 때문입니다. `MainLoop`는 애플리케이션 이벤트 루프를 시작하며, 애플리케이션이 종료되면 반환됩니다.

## 초기 뷰 트리 구성 {#building-the-initial-view-tree}

dali-ui 애플리케이션에서는 `Dali::Ui::View`를 앱에서 다루는 객체 모델로 취급합니다. 어댑터는 윈도우와 생명주기를 제공하고, UI 콘텐츠는 타입이 지정된 setter와 fluent chaining을 사용해 `View` 트리로 표현하는 것이 좋습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

View CreateContent()
{
  View header = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(72.0f)
    .SetBackgroundColor(UiColor(0.12f, 0.22f, 0.35f, 1.0f));

  View body = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT)
    .SetBackgroundColor(UiColor(0.96f, 0.96f, 0.94f, 1.0f));

  View root = View::New()
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(MATCH_PARENT);

  root.Add(header);
  root.Add(body);

  return root;
}

void OnInit(Application application)
{
  application.GetWindow().Add(CreateContent());
}
```

`View::New`는 초기화된 뷰 핸들을 생성합니다. `SetRequestedWidth`, `SetRequestedHeight`, `SetBackgroundColor`는 타입이 지정된 `View` setter이므로, 이 예제는 raw property 쓰기를 사용하지 않습니다. `Window::Add`는 루트 뷰를 기본 윈도우에 연결합니다. 이후의 구성은 부모 뷰에서 `View::Add`를 통해 수행해야 합니다.

## 생명주기 변경 처리 {#handling-lifecycle-changes}

`Dali::Application`은 작업을 일시 중지하거나 재개하거나 리소스를 해제해야 하는 앱 코드를 위해 생명주기 신호를 제공합니다. `PauseSignal`, `ResumeSignal`, `TerminateSignal`, `ResetSignal`, `LanguageChangedSignal`, `RegionChangedSignal`은 `Dali::Application`을 전달받는 콜백을 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;

void OnPause(Application application)
{
  // Pause app-owned work that should not run in the background.
}

void OnResume(Application application)
{
  // Resume app-owned work and refresh state if needed.
}

void OnTerminate(Application application)
{
  // Release app-owned resources before shutdown.
}

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);

  application.PauseSignal().Connect(&OnPause);
  application.ResumeSignal().Connect(&OnResume);
  application.TerminateSignal().Connect(&OnTerminate);

  application.MainLoop();
  return 0;
}
```

애플리케이션 전역 로케일을 갱신할 때는 텍스트나 로케일에 민감한 콘텐츠를 다시 구성하는 신호 콜백에서 `GetLanguage` 또는 `GetRegion`을 호출합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;

void OnLanguageChanged(Application application)
{
  Dali::String language = application.GetLanguage();
  Dali::String region = application.GetRegion();

  // Rebuild or refresh app text using language and region.
}

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);
  application.LanguageChangedSignal().Connect(&OnLanguageChanged);
  application.RegionChangedSignal().Connect(&OnLanguageChanged);
  application.MainLoop();
  return 0;
}
```

## 디바이스 상태에 대응 {#reacting-to-device-status}

어댑터 프레임워크는 `LowBatterySignal`, `LowMemorySignal`, `DeviceOrientationChangedSignal`을 통해 디바이스 수준의 상태도 보고합니다.

전달된 값에 따라 분기하려면 `Dali::DeviceStatus::Battery::Status`, `Dali::DeviceStatus::Memory::Status`, `Dali::DeviceStatus::Orientation::Status`를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;

void OnLowMemory(DeviceStatus::Memory::Status status)
{
  if(status == DeviceStatus::Memory::CRITICALLY_LOW)
  {
    // Drop optional cached data or defer expensive app work.
  }
}

void OnLowBattery(DeviceStatus::Battery::Status status)
{
  if(status == DeviceStatus::Battery::POWER_OFF)
  {
    // Save critical app state immediately.
  }
}

void OnDeviceOrientationChanged(DeviceStatus::Orientation::Status status)
{
  if(status == DeviceStatus::Orientation::ORIENTATION_90 ||
     status == DeviceStatus::Orientation::ORIENTATION_270)
  {
    // Update app layout state for a landscape device orientation.
  }
}

int main(int argc, char** argv)
{
  Application application = Application::New(&argc, &argv);

  application.LowMemorySignal().Connect(&OnLowMemory);
  application.LowBatterySignal().Connect(&OnLowBattery);
  application.DeviceOrientationChangedSignal().Connect(&OnDeviceOrientationChanged);

  application.MainLoop();
  return 0;
}
```

이 콜백들은 앱이 소유한 리소스와 정책 결정을 처리할 때 유용합니다. 표시되는 UI는 `Dali::Ui::View` 객체로 표현하고, 뷰 갱신은 `View` API를 통해 수행합니다.

## 고급 어댑터 소유권 {#advanced-adaptor-ownership}

대부분의 dali-ui 애플리케이션은 `Dali::Application`을 사용하는 것이 좋습니다. `Dali::Adaptor`는 자체 플랫폼 메인 루프와 윈도우 소유권을 제공하는 환경을 위한 더 낮은 수준의 통합 객체입니다.

`Dali::Adaptor`를 사용할 때는 `Dali::Window`를 생성하거나 전달받고, `Dali::Adaptor::New`로 어댑터를 만든 뒤 `Start`를 호출한 다음 자체 플랫폼 루프를 실행합니다. 어댑터가 시작되면 앱 코드는 `Dali::Ui::View` 트리를 윈도우에 연결할 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>
#include <dali/integration-api/adaptor-framework/adaptor.h>

using namespace Dali;
using namespace Dali::Ui;

void CreateUi(Window window)
{
  window.Add(
    View::New()
      .SetRequestedWidth(MATCH_PARENT)
      .SetRequestedHeight(MATCH_PARENT)
      .SetBackgroundColor(UiColor(0.18f, 0.18f, 0.20f, 1.0f)));
}

void StartWithExternalLoop(Window window)
{
  Adaptor& adaptor = Adaptor::New(window);
  adaptor.Start();

  CreateUi(window);

  // Run the platform event loop owned by the embedding environment.
}
```

이 모델은 애플리케이션이 이미 플랫폼 루프를 소유하고 있을 때만 사용합니다. 일반적인 dali-ui 앱에서는 `Dali::Application`이 앱에서 다루기 더 단순하고 권장되는 진입점입니다.
