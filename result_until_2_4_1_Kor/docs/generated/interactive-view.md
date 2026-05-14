---
title: 인터랙티브 뷰
sidebar_label: 인터랙티브 뷰
category: views-components
---

# 인터랙티브 뷰 {#interactive-view}

`Dali::Ui::InteractiveView`는 클릭 가능하고 눌림 상태를 인식하는 UI 표면을 위한 dali-ui `View` 타입입니다.

## 목차 {#table-of-contents}

- [인터랙티브 표면 만들기](#create-an-interactive-surface)
- [클릭 처리하기](#handle-clicks)
- [키보드 클릭 타이밍 제어하기](#control-keyboard-click-timing)
- [눌림 상태와 의사 비활성 상태 추적하기](#track-pressed-and-pseudo-disabled-state)
- [길게 누르기 처리하기](#handle-long-press)
- [타입 지정 핸들에서 InteractiveView 사용하기](#use-interactiveview-in-typed-handles)

## 인터랙티브 표면 만들기 {#create-an-interactive-surface}

일반 인터랙티브 컨테이너가 상호작용 이벤트를 직접 받아야 할 때는 `Dali::Ui::InteractiveView::New()`를 사용합니다. `Dali::Ui::InteractiveView`는 기본적으로 클릭 가능하며, `SetClickable()`을 사용하면 객체 모델을 바꾸지 않고 클릭 생성 여부를 켜거나 끌 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/interactive-view.h>

class PanelController
{
public:
  void Create()
  {
    Dali::Ui::InteractiveView panel =
      Dali::Ui::InteractiveView::New()
        .SetClickable(true)
        .SetPseudoDisabled(false)
        .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE);

    const bool canClick = panel.IsClickable();
    const bool disabled = panel.IsPseudoDisabled();

    (void)canClick;
    (void)disabled;
  }
};
```

`SetClickable(false)`는 `Dali::Ui::InteractiveView` 핸들과 설정된 시그널을 그대로 유지하면서 탭 및 키 클릭 활성화를 막습니다. 표면이 사용할 수 없는 것처럼 보여야 하고 클릭 활성화도 중지해야 한다면 `SetPseudoDisabled()`와 함께 사용합니다.

```cpp
void SetUnavailable(Dali::Ui::InteractiveView view, bool unavailable)
{
  view
    .SetClickable(!unavailable)
    .SetPseudoDisabled(unavailable);
}
```

## 클릭 처리하기 {#handle-clicks}

`ClickedSignal()`은 콜백 형태가 `void(Dali::Ui::View, Dali::Ui::InputEvent)`인 `Dali::Signal`을 반환합니다. 첫 번째 인자는 소스 `Dali::Ui::View`이므로, 핸들러를 애플리케이션에서 사용하는 뷰 모델 기준으로 작성할 수 있습니다.

간결하게 설정하려면 `Dali::Ui::InteractiveView`에서 `ConnectClickedSignal()`을 사용하는 편이 좋습니다. 이 함수는 동일한 `Dali::Ui::InteractiveView`를 반환하므로 플루언트 방식의 생성 코드에 자연스럽게 연결됩니다.

```cpp
class ClickController
{
public:
  void Create()
  {
    mButton =
      Dali::Ui::InteractiveView::New()
        .SetClickable(true)
        .ConnectClickedSignal(this, &ClickController::OnClicked);
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    ++mClickCount;

    (void)view;
    (void)event;
  }

private:
  Dali::Ui::InteractiveView mButton;
  int                       mClickCount{0};
};
```

반환된 시그널 객체를 직접 다루고 싶다면 `ClickedSignal()`에 바로 연결할 수도 있습니다.

```cpp
class DirectSignalController
{
public:
  void Create()
  {
    mButton = Dali::Ui::InteractiveView::New();

    mButton.ClickedSignal().Connect(
      this,
      &DirectSignalController::OnClicked);
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    mLastClicked = view;
    (void)event;
  }

private:
  Dali::Ui::InteractiveView mButton;
  Dali::Ui::View            mLastClicked;
};
```

## 키보드 클릭 타이밍 제어하기 {#control-keyboard-click-timing}

`SetKeyClickPolicy()`는 D-Pad, 리모컨 또는 키보드 입력이 클릭으로 전환되는 방식을 제어합니다. 놓을 때 clicked를 발생시키려면 `Dali::Ui::KeyClickPolicy::ON_RELEASE`, 누를 때 clicked를 발생시키려면 `Dali::Ui::KeyClickPolicy::ON_PRESS`, 키 입력이 클릭 이벤트를 만들지 않게 하려면 `Dali::Ui::KeyClickPolicy::DISABLED`를 사용합니다.

```cpp
class KeyClickController
{
public:
  void UseReleaseActivation()
  {
    mActionView.SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE);
  }

  void UseImmediateActivation()
  {
    mActionView.SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_PRESS);
  }

  void DisableKeyActivation()
  {
    mActionView.SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::DISABLED);
  }

  bool IsImmediateActivation() const
  {
    return mActionView.GetKeyClickPolicy() == Dali::Ui::KeyClickPolicy::ON_PRESS;
  }

private:
  Dali::Ui::InteractiveView mActionView{Dali::Ui::InteractiveView::New()};
};
```

키보드 정책과 포인터 클릭 가능 여부는 별도로 제어됩니다. 뷰는 포인터로는 계속 클릭 가능하게 두면서, 키로 트리거되는 클릭만 비활성화할 수 있습니다.

```cpp
Dali::Ui::InteractiveView MakePointerOnlyAction()
{
  return Dali::Ui::InteractiveView::New()
    .SetClickable(true)
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::DISABLED);
}
```

## 눌림 상태와 의사 비활성 상태 추적하기 {#track-pressed-and-pseudo-disabled-state}

`PressedChangedSignal()`은 눌림 상태로 들어가거나 눌림 상태에서 벗어나는 전환을 콜백 형태 `void(Dali::Ui::View, bool, Dali::Ui::InputEvent)`로 알립니다. 이후 코드에서 현재 상태가 필요하다면 `IsPressed()`를 사용합니다.

```cpp
class PressController
{
public:
  void Create()
  {
    mSurface =
      Dali::Ui::InteractiveView::New()
        .ConnectPressedChangedSignal(this, &PressController::OnPressedChanged);
  }

  void OnPressedChanged(
    Dali::Ui::View       view,
    bool                 pressed,
    Dali::Ui::InputEvent event)
  {
    mPressed = pressed;

    const bool currentState = mSurface.IsPressed();

    (void)view;
    (void)event;
    (void)currentState;
  }

private:
  Dali::Ui::InteractiveView mSurface;
  bool                      mPressed{false};
};
```

`SetPseudoDisabled()`는 시각적인 의사 비활성 상태를 변경하고, `PseudoDisabledChangedSignal()`은 해당 상태를 콜백 형태 `void(Dali::Ui::View, bool)`로 알립니다. 의사 비활성 뷰는 비활성처럼 표시되지만 여전히 상호작용을 받을 수 있습니다. 뷰가 눌린 상태일 때 의사 비활성이 설정되면 눌림 상태가 해제됩니다.

```cpp
class DisabledStateController
{
public:
  void Create()
  {
    mSurface = Dali::Ui::InteractiveView::New();

    mSurface.PseudoDisabledChangedSignal().Connect(
      this,
      &DisabledStateController::OnPseudoDisabledChanged);
  }

  void SetUnavailable(bool unavailable)
  {
    mSurface.SetPseudoDisabled(unavailable);
  }

  void OnPseudoDisabledChanged(Dali::Ui::View view, bool pseudoDisabled)
  {
    mUnavailable = pseudoDisabled;

    (void)view;
  }

private:
  Dali::Ui::InteractiveView mSurface;
  bool                      mUnavailable{false};
};
```

## 길게 누르기 처리하기 {#handle-long-press}

`LongPressedSignal()`은 콜백 형태 `bool(Dali::Ui::View, Dali::Ui::InputEvent)`를 사용합니다. 길게 누르기가 상호작용을 소비하여 이후 클릭을 억제해야 한다면 `true`를 반환합니다. 클릭이 계속 발생해도 된다면 `false`를 반환합니다.

```cpp
class LongPressController
{
public:
  void Create()
  {
    mSurface =
      Dali::Ui::InteractiveView::New()
        .ConnectClickedSignal(this, &LongPressController::OnClicked)
        .ConnectLongPressedSignal(this, &LongPressController::OnLongPressed);
  }

  void OnClicked(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    ++mClickCount;

    (void)view;
    (void)event;
  }

  bool OnLongPressed(Dali::Ui::View view, Dali::Ui::InputEvent event)
  {
    ++mLongPressCount;

    (void)view;
    (void)event;

    return true;
  }

private:
  Dali::Ui::InteractiveView mSurface;
  int                       mClickCount{0};
  int                       mLongPressCount{0};
};
```

길게 누르기 동작이 배타적인 동작이 아니라 보조 동작이라면 `false`를 반환합니다.

```cpp
bool ShowPreviewWithoutConsuming(
  Dali::Ui::View       view,
  Dali::Ui::InputEvent event)
{
  (void)view;
  (void)event;

  return false;
}
```

## 타입 지정 핸들에서 InteractiveView 사용하기 {#use-interactiveview-in-typed-handles}

`Dali::Ui::InteractiveView::DownCast()`는 호환되는 기본 핸들을 다시 `Dali::Ui::InteractiveView`로 변환합니다. 제네릭 핸들이 인터랙티브 뷰를 생성하는 API에서 온다는 것을 알고 있을 때 사용합니다.

```cpp
Dali::Ui::InteractiveView AsInteractiveView(Dali::BaseHandle handle)
{
  Dali::Ui::InteractiveView view =
    Dali::Ui::InteractiveView::DownCast(handle);

  if(view)
  {
    view.SetClickable(true);
  }

  return view;
}
```

생성된 헤더 `interactive-view.autogen.h`는 `Dali::Ui::InteractiveView`에서 파생되는 dali-ui 핸들 타입을 위해 `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS`를 제공합니다. 이 매크로는 인터랙티브 setter와 시그널 헬퍼를 전달하여 파생 핸들이 자체 플루언트 반환 타입을 유지할 수 있게 합니다.

```cpp
class ToolbarAction : public Dali::Ui::InteractiveView
{
public:
  DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS(ToolbarAction)
};
```

애플리케이션 코드는 일반적으로 구체 핸들을 통해 결과로 제공되는 타입 지정 플루언트 API를 사용합니다. 상속된 메서드는 여전히 `Dali::Ui::InteractiveView` 동작에 매핑됩니다.

```cpp
void ConfigureToolbarAction(ToolbarAction action)
{
  action
    .SetClickable(true)
    .SetPseudoDisabled(false)
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE);
}
```
