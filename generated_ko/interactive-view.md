---
title: 인터랙티브 뷰
sidebar_label: 인터랙티브 뷰
category: views-components
---

# 인터랙티브 뷰

InteractiveView는 클릭 가능한 UI 컴포넌트 생성을 위한 내장 인터랙티브 동작이 있는 View 서브클래스입니다.

## 목차

- [InteractiveView 생성](#interactiveview-생성)
- [클릭 동작 구성](#클릭-동작-구성)
- [클릭 이벤트 처리](#클릭-이벤트-처리)
- [눌림 상태 변경에 응답](#눌림-상태-변경에-응답)
- [롱 프레스 제스처 감지](#롱-프레스-제스처-감지)
- [의사 비활성 상태로 상호작용 비활성화](#의사-비활성-상태로-상호작용-비활성화)
- [커스텀 인터랙티브 컴포넌트 파생](#커스텀-인터랙티브-컴포넌트-파생)

## InteractiveView 생성

정적 `New()` 메서드로 `InteractiveView`를 생성합니다. 뷰는 기본적으로 클릭 가능합니다.

```cpp
using namespace Dali::Ui;

InteractiveView view = InteractiveView::New();
view.SetRequestedWidth(200.0f)
    .SetRequestedHeight(60.0f);
```

베이스 핸들을 `InteractiveView`로 다운캐스트하려면 `DownCast()`를 사용합니다:

```cpp
InteractiveView view = InteractiveView::DownCast(someHandle);
if (view)
{
  // 다운캐스트 성공
}
```

## 클릭 동작 구성

### 클릭 활성화 및 비활성화

`SetClickable()`로 뷰가 클릭에 응답하는지 제어합니다. `IsClickable()`으로 현재 상태를 조회합니다.

```cpp
InteractiveView view = InteractiveView::New();

view.SetClickable(false);  // 클릭 비활성화
if (!view.IsClickable())
{
  // 뷰가 탭에 응답하지 않음
}

view.SetClickable(true);   // 클릭 다시 활성화
```

### 키 클릭 정책

키보드 탐색의 경우 `SetKeyClickPolicy()`로 키 누름이 클릭을 트리거하는 시점을 구성합니다. `GetKeyClickPolicy()`로 현재 정책을 조회합니다.

```cpp
view.SetKeyClickPolicy(KeyClickPolicy::ON_PRESS);
KeyClickPolicy policy = view.GetKeyClickPolicy();
```

## 클릭 이벤트 처리

사용자가 뷰를 탭하거나 클릭할 때 응답하려면 `ClickedSignal()`에 연결합니다. 시그널은 뷰와 클릭을 트리거한 입력 이벤트를 제공합니다.

### 플루언트 체이닝을 위한 ConnectClickedSignal 사용

`ConnectClickedSignal()` 메서드는 뷰의 참조를 반환하여 설정 중 메서드 체이닝을 가능하게 합니다:

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupView()
  {
    InteractiveView view = InteractiveView::New()
      .SetRequestedWidth(200.0f)
      .SetRequestedHeight(60.0f)
      .ConnectClickedSignal(this, &MyController::OnClicked);
  }

  void OnClicked(View view, InputEvent event)
  {
    // 클릭 처리
  }
};
```

### 직접 시그널 연결

또는 `ClickedSignal()`에 직접 연결합니다:

```cpp
view.ClickedSignal().Connect(this, &MyController::OnClicked);
```

동일한 시그널에 여러 핸들러를 연결할 수 있습니다:

```cpp
view.ConnectClickedSignal(this, &MyController::OnClickedFirst);
view.ConnectClickedSignal(this, &MyController::OnClickedSecond);
```

## 눌림 상태 변경에 응답

사용자가 누르거나 놓을 때 `PressedChangedSignal()`에 연결하여 추적합니다. 콜백은 뷰, 눌림 상태를 나타내는 불리언, 입력 이벤트를 받습니다.

```cpp
class ButtonController : public ConnectionTracker
{
public:
  void CreateButton()
  {
    mButton = InteractiveView::New()
      .SetRequestedWidth(150.0f)
      .SetRequestedHeight(50.0f)
      .ConnectPressedChangedSignal(this, &ButtonController::OnPressedChanged);
  }

  void OnPressedChanged(View view, bool pressed, InputEvent event)
  {
    if (pressed)
    {
      // 사용자가 누름 - 시각적 피드백 표시
    }
    else
    {
      // 사용자가 놓음 - 정상 외관 복원
    }
  }

private:
  InteractiveView mButton;
};
```

언제든지 `IsPressed()`로 현재 눌림 상태를 조회합니다:

```cpp
if (view.IsPressed())
{
  // 뷰가 현재 눌림 상태
}
```

## 롱 프레스 제스처 감지

사용자가 롱 프레스 제스처를 수행할 때 감지하려면 `LongPressedSignal()`에 연결합니다. 핸들러는 불리언을 반환해야 합니다: 후속 클릭 이벤트를 억제하려면 `true`를, 롱 프레스와 클릭 모두 발생하도록 하려면 `false`를 반환합니다.

```cpp
class LongPressController : public ConnectionTracker
{
public:
  void SetupView()
  {
    InteractiveView view = InteractiveView::New()
      .SetRequestedWidth(100.0f)
      .SetRequestedHeight(100.0f)
      .ConnectLongPressedSignal(this, &LongPressController::OnLongPressed);
  }

  bool OnLongPressed(View view, InputEvent event)
  {
    // 롱 프레스 처리
    return true;  // 후속 클릭 억제
  }
};
```

직접 시그널 연결:

```cpp
view.LongPressedSignal().Connect(this, &LongPressController::OnLongPressed);
```

## 의사 비활성 상태로 상호작용 비활성화

의사 비활성 상태를 사용하여 뷰 계층에 유지하면서 뷰가 비인터랙티브임을 시각적으로 표시합니다. 비활성화된 버튼이나 컨트롤을 표시하는 데 유용합니다.

```cpp
InteractiveView view = InteractiveView::New();

view.SetPseudoDisabled(true);  // 비활성화로 표시
if (view.IsPseudoDisabled())
{
  // 뷰가 의사 비활성 상태
}

view.SetPseudoDisabled(false);  // 다시 활성화
```

상태 변경에 반응하려면 `PseudoDisabledChangedSignal()`에 연결합니다:

```cpp
view.PseudoDisabledChangedSignal().Connect(this, &MyController::OnPseudoDisabledChanged);

void OnPseudoDisabledChanged(View view, bool disabled)
{
  if (disabled)
  {
    // 비활성 상태를 표시하도록 시각적 외관 업데이트
  }
}
```

## 커스텀 인터랙티브 컴포넌트 파생

`InteractiveView`는 커스텀 인터랙티브 컴포넌트의 기본 클래스로 설계되었습니다. 파생 클래스에서 `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS` 매크로를 사용하여 모든 체이닝 메서드를 상속합니다.

```cpp
class MyButton : public InteractiveView
{
public:
  MyButton() = default;

  static MyButton New(const Dali::String& text)
  {
    MyButton button(InteractiveView::New());
    button.Initialize(text);
    return button;
  }

  static MyButton DownCast(BaseHandle handle)
  {
    InteractiveView view = InteractiveView::DownCast(handle);
    return view ? MyButton(view) : MyButton();
  }

  void Initialize(const Dali::String& text)
  {
    // 버튼 외관 설정
  }

  // InteractiveView의 모든 체이닝 메서드 상속
  DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS(MyButton)

private:
  explicit MyButton(InteractiveView view)
  : InteractiveView(view)
  {
  }
};
```

매크로는 파생 타입을 반환하는 오버라이드된 `SetClickable()`, `SetPseudoDisabled()`, `SetKeyClickPolicy()`, 시그널 연결 메서드를 제공하여 플루언트 체이닝을 유지합니다:

```cpp
MyButton button = MyButton::New("Click Me")
  .SetClickable(true)
  .SetPseudoDisabled(false)
  .ConnectClickedSignal(this, &MyController::OnButtonClicked);
```
