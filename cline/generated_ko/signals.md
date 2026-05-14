---
title: 시그널
sidebar_label: 시그널
category: utilities
---

# 시그널

시그널은 dali-ui 애플리케이션에서 이벤트에 응답하기 위한 타입 안전한 발행-구독 메커니즘을 제공합니다. 이벤트 소스와 핸들러 간의 느슨한 결합을 가능하게 하면서 슬롯 소유자가 파괴될 때 자동 연결 해제를 보장합니다.

## 목차

- [시그널 연결](#시그널-연결)
- [시그널 발행](#시그널-발행)
- [연결 수명 주기 관리](#연결-수명-주기-관리)
- [시그널 변형](#시그널-변형)

## 시그널 연결

시그널 방출을 수신하려면 콜백 함수나 멤버 함수를 시그널에 연결합니다. 연결은 시그널-슬롯 연결 수명을 관리하는 `ConnectionTrackerInterface` 구현이 필요합니다.

### 멤버 함수 연결

가장 일반적인 패턴은 `ConnectionTracker`를 상속하고 멤버 함수를 연결하는 것입니다:

```cpp
#include <dali/public-api/signals/connection-tracker.h>

class MyController : public ConnectionTracker
{
public:
  void OnInit(Application application)
  {
    Window window = application.GetWindow();
    
    // 윈도우 키 이벤트 시그널에 연결
    window.KeyEventSignal().Connect(this, &MyController::OnKeyEvent);
  }

  void OnKeyEvent(Window window, KeyEvent event)
  {
    if(event.GetState() == KeyEvent::DOWN)
    {
      // 키 누름 처리
    }
  }
};
```

`ConnectionTracker` 기본 클래스는 객체가 파괴될 때 모든 시그널을 자동으로 연결 해제하여 댕글링 콜백을 방지합니다.

### 정적 함수 연결

객체 컨텍스트 없는 단순 콜백의 경우 정적 함수를 연결합니다:

```cpp
void OnApplicationReady()
{
  // 애플리케이션 준비 이벤트 처리
}

// 정적 함수 연결
someSignal.Connect(&OnApplicationReady);

// 나중에 더 이상 필요 없을 때 연결 해제
someSignal.Disconnect(&OnApplicationReady);
```

### 뷰 시그널 연결

dali-ui 뷰는 사용자 인터랙션과 상태 변경을 위한 시그널을 노출합니다:

```cpp
class WebViewController : public ConnectionTracker
{
public:
  void SetupWebView()
  {
    mWebView = WebView::New();
    
    // 페이지 로드 시그널에 연결
    mWebView.PageLoadStartedSignal().Connect(this, &WebViewController::OnPageLoadStarted);
    mWebView.PageLoadFinishedSignal().Connect(this, &WebViewController::OnPageLoadFinished);
  }

  void OnPageLoadStarted(WebView view, const Dali::String& url)
  {
    // 페이지 로드 시작 처리
  }

  void OnPageLoadFinished(WebView view, const Dali::String& url)
  {
    // 페이지 로드 완료 처리
  }

private:
  WebView mWebView;
};
```

## 시그널 발행

시그널을 정의하려면 `Signal` 템플릿을 사용합니다:

```cpp
// 매개변수 없는 시그널
Signal<> OnComplete;

// 매개변수가 있는 시그널
Signal<int, const String&> OnDataChanged;

// 시그널 발행
OnComplete.Emit();
OnDataChanged.Emit(42, "updated");
```

## 연결 수명 주기 관리

### 자동 연결 해제

`ConnectionTracker`를 상속하면 소멸 시 모든 연결이 자동으로 해제됩니다:

```cpp
class Controller : public ConnectionTracker
{
public:
  Controller()
  {
    someSignal.Connect(this, &Controller::OnEvent);
  }
  // 소멸 시 자동 연결 해제
};
```

### 수동 연결 해제

특정 연결을 수동으로 해제할 수 있습니다:

```cpp
// 멤버 함수 연결 해제
signal.Disconnect(this, &MyClass::OnEvent);

// 정적 함수 연결 해제
signal.Disconnect(&StaticCallback);

// 모든 연결 해제
signal.ClearConnections();
```

## 시그널 변형

### 반환 값이 있는 시그널

시그널 핸들러가 값을 반환할 수 있습니다:

```cpp
Signal<bool, int> OnValidate;

// 핸들러가 bool을 반환
bool OnValidate(int value)
{
  return value > 0;
}

// Emit()은 마지막 핸들러의 반환 값을 반환
bool result = OnValidate.Emit(10);
```

### 연결 확인

시그널에 연결이 있는지 확인합니다:

```cpp
if (signal.GetConnectionCount() > 0)
{
  // 연결이 있음
}
```
