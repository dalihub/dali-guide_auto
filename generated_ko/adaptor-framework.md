---
title: 어댑터 프레임워크
sidebar_label: 어댑터 프레임워크
category: application-framework
---

# 어댑터 프레임워크

어댑터 프레임워크는 DALi 애플리케이션을 위한 핵심 인프라를 제공하며, 애플리케이션 수명 주기 관리, 윈도우 처리, 타이머 기반 이벤트, 그리고 장치 상태 모니터링을 포함합니다.

## 목차

- [애플리케이션 수명 주기](#애플리케이션-수명-주기)
- [윈도우 관리](#윈도우-관리)
- [타이머 및 주기적 이벤트](#타이머-및-주기적-이벤트)
- [장치 상태 모니터링](#장치-상태-모니터링)

## 애플리케이션 수명 주기

모든 DALi 애플리케이션은 DALi 런타임을 초기화하기 위해 `Application` 인스턴스를 생성해야 합니다. `Application` 클래스는 메인 이벤트 루프를 관리하고 초기화, 일시 정지, 재개, 종료와 같은 수명 주기 이벤트에 대한 시그널을 발생시킵니다.

### 애플리케이션 생성

main 함수에서 `Application` 인스턴스를 생성하고 `InitSignal()`에 연결하여 UI를 설정합니다:

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
    // 여기서 UI를 구축합니다
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

### 수명 주기 시그널

`Application` 클래스는 모든 주요 수명 주기 이벤트에 대한 시그널을 제공합니다:

| 시그널 | 설명 |
|--------|------|
| `InitSignal()` | 애플리케이션이 초기화 준비가 되었을 때 발생 |
| `PauseSignal()` | 애플리케이션이 일시 정지될 때 발생 |
| `ResumeSignal()` | 애플리케이션이 재개될 때 발생 |
| `TerminateSignal()` | 애플리케이션이 종료될 때 발생 |
| `ResetSignal()` | 시스템이 재초기화를 요구할 때 발생 |

이 시그널들에 연결하여 애플리케이션 상태를 관리합니다:

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

  void OnInit(Application app) { /* 리소스 초기화 */ }
  void OnPause(Application app) { /* 애니메이션 일시 정지, 상태 저장 */ }
  void OnResume(Application app) { /* 애니메이션 재개 */ }
  void OnTerminate(Application app) { /* 리소스 정리 */ }

private:
  Application& mApplication;
};
```

### 종료 및 최소화

`Quit()`을 사용하여 애플리케이션을 완전히 종료하거나, `Lower()`를 사용하여 백그라운드로 최소화합니다:

```cpp
void OnKeyEvent(Window window, KeyEvent event)
{
  if (event.GetState() == KeyEvent::DOWN)
  {
    if (IsKey(event, Dali::DALI_KEY_ESCAPE) || IsKey(event, Dali::DALI_KEY_BACK))
    {
      mApplication.Quit();  // 완전 종료
      // 또는: mApplication.Lower();  // 백그라운드로 전환
    }
  }
}
```

### UI 스레드 모드

UI를 차단하지 않고 시스템 이벤트를 처리해야 하는 애플리케이션의 경우 UI 스레드 모드를 활성화합니다:

```cpp
WindowData windowData;
windowData.SetTransparency(true);
Application application = Application::New(&argc, &argv, "", true, windowData);
```

UI 스레드 모드가 활성화되면, 작업 시그널(`TaskInitSignal()`, `TaskTerminateSignal()` 등)은 메인 스레드에서 발생하고 일반 시그널은 UI 스레드에서 발생합니다.

## 윈도우 관리

`Window` 클래스는 그리기 가능한 표면을 나타냅니다. DALi는 `Application::GetWindow()`를 통해 접근 가능한 기본 메인 윈도우를 생성합니다.

### 메인 윈도우 접근

```cpp
void OnInitialize(Application application)
{
  Window window = application.GetWindow();
  window.SetBackgroundColor(Color::WHITE);
}
```

### 윈도우에 뷰 추가

윈도우에 `View` 객체를 추가하여 UI를 구축합니다:

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

### 추가 윈도우 생성

`Window::New()`로 보조 윈도우를 생성합니다:

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

### 윈도우 속성

윈도우 외관과 동작을 구성합니다:

```cpp
Window window = application.GetWindow();

// 배경색
window.SetBackgroundColor(Vector4(0.9f, 0.9f, 0.9f, 1.0f));

// 크기와 위치
window.SetSize(Window::WindowSize(800, 600));
window.SetPosition(Window::WindowPosition(100, 100));

// 포커스 처리
window.SetAcceptFocus(true);

// 가시성
window.Show();
bool visible = window.IsVisible();
```

### 윈도우 시그널

시그널을 통해 윈도우 이벤트를 처리합니다:

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
      // 윈도우가 포커스를 획득
    } else {
      // 윈도우가 포커스를 상실
    }
  }

  void OnResize(Window window, Window::WindowSize size)
  {
    int width = size.GetX();
    int height = size.GetY();
    // 크기 변경 처리
  }

  void OnKeyEvent(Window window, KeyEvent event)
  {
    if (event.GetState() == KeyEvent::DOWN) {
      // 키 누름 처리
    }
  }
};
```

### 방향

`AddAvailableOrientation()`과 `SetPreferredOrientation()`으로 윈도우 방향을 제어합니다:

```cpp
Window window = application.GetWindow();
window.AddAvailableOrientation(WindowOrientation::PORTRAIT);
window.AddAvailableOrientation(WindowOrientation::LANDSCAPE);
window.SetPreferredOrientation(WindowOrientation::PORTRAIT);
```

## 타이머 및 주기적 이벤트

`Timer` 클래스는 애니메이션, 폴링 또는 예약된 업데이트를 위한 주기적 콜백을 제공합니다.

### 타이머 생성

밀리초 단위로 지정된 간격으로 타이머를 생성합니다:

```cpp
Timer timer = Timer::New(1000);  // 1초 간격
timer.TickSignal().Connect(this, &MyClass::OnTimerTick);
timer.Start();
```

### 타이머 콜백

타이머를 계속하려면 콜백에서 `true`를 반환하고, 중지하려면 `false`를 반환합니다:

```cpp
bool OnTimerTick()
{
  // 애니메이션 프레임 업데이트, 상태 확인 등
  return true;  // 타이머 계속
}
```

### 타이머 제어

```cpp
Timer timer = Timer::New(500);

timer.Start();   // 타이머 시작 또는 재시작
timer.Stop();    // 타이머 정지
timer.Pause();   // 타이머 일시 정지
timer.Resume();  // 일시 정지에서 재개

bool running = timer.IsRunning();
uint32_t interval = timer.GetInterval();
timer.SetInterval(1000);  // 새 간격 설정 및 재시작
```

### 일회성 타이머

단일 지연 콜백을 위해 첫 번째 틱 후 타이머를 중지합니다:

```cpp
Timer oneShot = Timer::New(2000);
oneShot.TickSignal().Connect(this, &MyClass::OnOneShot);
oneShot.Start();

// 콜백에서:
bool OnOneShot()
{
  DoDelayedAction();
  return false;  // 첫 실행 후 중지
}
```

## 장치 상태 모니터링

`Application` 클래스는 배터리 부족, 메모리 부족, 방향 변경과 같은 장치 수준 이벤트에 대한 시그널을 발생시킵니다.

### 배터리 상태

배터리 수준 변경에 대응하려면 `LowBatterySignal()`에 연결합니다:

```cpp
mApplication.LowBatterySignal().Connect(this, &MyApp::OnLowBattery);

void OnLowBattery(DeviceStatus::Battery::Status status)
{
  switch (status)
  {
    case DeviceStatus::Battery::NORMAL:
      // 배터리 5% 이상
      break;
    case DeviceStatus::Battery::CRITICALLY_LOW:
      // 배터리 5% 미만, 활동 감소
      break;
    case DeviceStatus::Battery::POWER_OFF:
      // 배터리 1% 미만, 종료 준비
      break;
  }
}
```

### 메모리 상태

메모리가 부족할 때 리소스를 해제하려면 `LowMemorySignal()`에 연결합니다:

```cpp
mApplication.LowMemorySignal().Connect(this, &MyApp::OnLowMemory);

void OnLowMemory(DeviceStatus::Memory::Status status)
{
  switch (status)
  {
    case DeviceStatus::Memory::NORMAL:
      // 정상 동작
      break;
    case DeviceStatus::Memory::LOW:
      // 필수적이지 않은 캐시 해제
      ClearImageCaches();
      break;
    case DeviceStatus::Memory::CRITICALLY_LOW:
      // 가능한 모든 리소스 해제
      ClearAllCaches();
      ReduceTextureQuality();
      break;
  }
}
```

### 장치 방향

물리적 장치 회전에 대응하려면 `DeviceOrientationChangedSignal()`에 연결합니다:

```cpp
mApplication.DeviceOrientationChangedSignal().Connect(this, &MyApp::OnOrientationChanged);

void OnOrientationChanged(DeviceStatus::Orientation::Status orientation)
{
  switch (orientation)
  {
    case DeviceStatus::Orientation::ORIENTATION_0:
      // 장치가 자연스러운 위치
      break;
    case DeviceStatus::Orientation::ORIENTATION_90:
      // 장치의 왼쪽면이 위쪽
      break;
    case DeviceStatus::Orientation::ORIENTATION_180:
      // 장치가 거꾸로
      break;
    case DeviceStatus::Orientation::ORIENTATION_270:
      // 장치의 오른쪽면이 위쪽
      break;
  }
}
```

### 로케일 변경

언어 및 지역 변경을 처리합니다:

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
