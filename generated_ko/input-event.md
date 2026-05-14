---
title: 입력 이벤트
sidebar_label: 입력 이벤트
category: input-interaction
---

# 입력 이벤트

`Dali::Ui::InputEvent`는 터치 이벤트, 키 이벤트, 제스처를 포함한 다양한 입력 타입에 대한 통합 래퍼를 제공합니다. 시그널 핸들러가 단일 인터페이스를 통해 원래 입력 소스를 수신하고 검사할 수 있게 합니다.

## 목차

- [이벤트 타입](#이벤트-타입)
- [입력 이벤트 생성](#입력-이벤트-생성)
- [이벤트 내용 검사](#이벤트-내용-검사)
- [시그널 핸들러에서 InputEvent 사용](#시그널-핸들러에서-inputevent-사용)

## 이벤트 타입

`InputEvent`는 `Dali::Ui::InputEventType` 열거형으로 정의된 여러 입력 카테고리를 래핑합니다:

| 타입 | 설명 |
|------|-------------|
| `InputEventType::NONE` | 구체적인 입력 이벤트 없음; 프로그래밍 방식 상태 변경이나 프레임워크 트리거 수명 주기 이벤트에 사용 |
| `InputEventType::TOUCH_EVENT` | `TouchEvent`에서 발생 |
| `InputEventType::KEY_EVENT` | `KeyEvent`에서 발생 |
| `InputEventType::TAP_GESTURE` | `TapGesture`에서 발생 |
| `InputEventType::LONG_PRESS_GESTURE` | `LongPressGesture`에서 발생 |
| `InputEventType::WHEEL_EVENT` | `WheelEvent`에서 발생 |

특정 이벤트 데이터에 접근하기 전에 `GetInputEventType()`으로 래핑된 이벤트 타입을 확인합니다.

## 입력 이벤트 생성

### 소스 이벤트에서 생성

`InputEvent`는 각 지원 소스 타입에 대한 정적 `New()` 오버로드를 제공합니다:

```cpp
// 터치 이벤트에서 생성
Dali::TouchEvent touchEvent = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(touchEvent);

// 키 이벤트에서 생성
Dali::KeyEvent keyEvent = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(keyEvent);

// 탭 제스처에서 생성
Dali::TapGesture tapGesture = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(tapGesture);

// 롱 프레스 제스처에서 생성
Dali::LongPressGesture longPressGesture = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(longPressGesture);

// 휠 이벤트에서 생성
Dali::WheelEvent wheelEvent = /* ... */;
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(wheelEvent);
```

### 공유 NONE 인스턴스 사용

사용자 입력이 아닌 프로그래밍 방식 상태 변경의 경우, 호출당 힙 할당을 피하기 위해 공유 `None()` 인스턴스를 사용합니다:

```cpp
// 효율적: 공유 정적 인스턴스 반환
const Dali::Ui::InputEvent& noInput = Dali::Ui::InputEvent::None();
```

이는 사용자 상호작용이 아닌 코드에서 상태 변경을 트리거할 때 유용합니다.

## 이벤트 내용 검사

### 이벤트 타입 확인

`GetInputEventType()`을 호출하여 어떤 종류의 이벤트가 래핑되어 있는지 확인합니다:

```cpp
void HandleInputEvent(Dali::Ui::InputEvent event)
{
    switch (event.GetInputEventType())
    {
        case Dali::Ui::InputEventType::TOUCH_EVENT:
        {
            const Dali::TouchEvent& touch = event.GetTouchEvent();
            // 터치 이벤트 처리
            break;
        }
        case Dali::Ui::InputEventType::KEY_EVENT:
        {
            const Dali::KeyEvent& key = event.GetKeyEvent();
            // 키 이벤트 처리
            break;
        }
        case Dali::Ui::InputEventType::TAP_GESTURE:
        {
            const Dali::TapGesture& tap = event.GetTapGesture();
            // 탭 제스처 처리
            break;
        }
        case Dali::Ui::InputEventType::LONG_PRESS_GESTURE:
        {
            const Dali::LongPressGesture& longPress = event.GetLongPressGesture();
            // 롱 프레스 제스처 처리
            break;
        }
        case Dali::Ui::InputEventType::WHEEL_EVENT:
        {
            const Dali::WheelEvent& wheel = event.GetWheelEvent();
            // 휠 이벤트 처리
            break;
        }
        case Dali::Ui::InputEventType::NONE:
        default:
            // 구체적인 입력 이벤트 없음
            break;
    }
}
```

### 소스 이벤트 조회

각 getter 메서드는 래핑된 이벤트에 대한 const 참조를 반환합니다. 현재 이벤트 타입과 일치하는 getter만 호출합니다:

```cpp
Dali::Ui::InputEvent inputEvent = Dali::Ui::InputEvent::New(keyEvent);

// 올바름: 이벤트 타입이 KEY_EVENT
const Dali::KeyEvent& key = inputEvent.GetKeyEvent();

// 잘못됨: 디버그 빌드에서 어설션 발생
// const Dali::TouchEvent& touch = inputEvent.GetTouchEvent();
```

## 시그널 핸들러에서 InputEvent 사용

### ClickedSignal 핸들러

`InteractiveTrait`의 `ClickedSignal`은 핸들러에 `InputEvent`를 전달합니다:

```cpp
class MyController
{
public:
    void SetupButton(Dali::Ui::View button)
    {
        button.ConnectClickedSignal(this, &MyController::OnButtonClicked);
    }

    void OnButtonClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
    {
        if (event.GetInputEventType() == Dali::Ui::InputEventType::TAP_GESTURE)
        {
            const Dali::TapGesture& tap = event.GetTapGesture();
            // 필요시 탭 세부 정보 접근
        }
        // 클릭 처리
    }
};
```

### PressedChangedSignal 핸들러

`PressedChangedSignal`은 눌림 상태와 원인 `InputEvent`를 제공합니다:

```cpp
void OnPressedChanged(Dali::Ui::View view, bool pressed, Dali::Ui::InputEvent event)
{
    if (pressed)
    {
        // 눌림 상태에 대한 시각적 피드백
        view.SetBackgroundColor(Dali::Ui::UiColor(0xCCCCCC));
    }
    else
    {
        // 정상 외관 복원
        view.SetBackgroundColor(Dali::Ui::UiColor(0xFFFFFF));
    }
}

// 연결
view.ConnectPressedChangedSignal(this, &OnPressedChanged);
```

### LongPressedSignal 핸들러

`LongPressedSignal` 핸들러는 클릭 이벤트를 억제할지 여부를 제어하는 불리언을 반환합니다:

```cpp
bool OnLongPressed(Dali::Ui::View view, Dali::Ui::InputEvent event)
{
    // 이벤트를 소비하고 ClickedSignal 방지하려면 true 반환
    // 이후 ClickedSignal이 발생하도록 하려면 false 반환
    return true;
}

// 연결
view.ConnectLongPressedSignal(this, &OnLongPressed);
```

### 람다 핸들러

람다 함수도 `InputEvent`를 받을 수 있습니다:

```cpp
view.ConnectClickedSignal(this, [](Dali::Ui::View v, Dali::Ui::InputEvent e) {
    // 클릭 처리, 선택적으로 입력 세부 정보를 위해 e 검사
});
```

이벤트 세부 정보가 필요 없는 경우 매개변수 이름을 생략할 수 있습니다:

```cpp
view.ConnectClickedSignal(this, [](Dali::Ui::View v, Dali::Ui::InputEvent) {
    // 이벤트 검사 없이 클릭 처리
});
```
