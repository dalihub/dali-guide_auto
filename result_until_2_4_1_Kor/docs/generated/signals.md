---
title: 시그널
sidebar_label: 시그널
category: utilities
---

# 시그널 {#signals}

`Dali::Signal`은 DALi 이벤트를 애플리케이션 콜백에 연결하고, `Dali::ConnectionTracker`는 콜백 소유자가 소멸될 때 이러한 연결이 정리되도록 합니다.

## 목차 {#table-of-contents}

- [dali-ui 컨트롤러에서 시그널 연결하기](#connect-signals-from-a-dali-ui-controller)
- [형식이 지정된 시그널 시그니처 사용하기](#use-typed-signal-signatures)
- [수명과 연결 해제 관리하기](#manage-lifetime-and-disconnection)
- [람다와 함수 객체 연결하기](#connect-lambdas-and-function-objects)
- [애플리케이션 수준 시그널 발생시키기](#emit-application-level-signals)
- [시그널 상태 확인하기](#inspect-signal-state)

## dali-ui 컨트롤러에서 시그널 연결하기 {#connect-signals-from-a-dali-ui-controller}

dali-ui 애플리케이션에서는 UI 로직을 소유한 객체에 시그널 콜백을 둡니다. 컨트롤러가 소멸될 때 추적 중인 시그널 콜백을 자동으로 연결 해제해야 한다면 `Dali::ConnectionTracker`를 상속합니다.

일반적인 연결 형식은 다음과 같습니다.

```cpp
signal.Connect(this, &Controller::OnSignal);
```

멤버 함수 연결에서는 콜백 시그니처가 시그널 시그니처와 일치해야 합니다.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class DetailsController : public Dali::ConnectionTracker
{
public:
  using ActivatedSignalType = Dali::Signal<void(Dali::Ui::View)>;

  void ConnectTo(ActivatedSignalType& activatedSignal)
  {
    activatedSignal.Connect(this, &DetailsController::OnActivated);
  }

private:
  void OnActivated(Dali::Ui::View view)
  {
    mLastActivatedView = view;
  }

  Dali::Ui::View mLastActivatedView;
};
```

`Dali::ConnectionTracker`는 애플리케이션 콜백을 소유할 때 일반적으로 사용하는 객체입니다. 연결된 슬롯을 추적하고, 트래커가 사라질 때 해당 슬롯의 연결을 해제하기 때문입니다.

## 형식이 지정된 시그널 시그니처 사용하기 {#use-typed-signal-signatures}

`Dali::Signal`은 함수 시그니처별로 특수화됩니다. 콜백이 무엇을 받고 무엇을 반환하는지 정확히 표현하는 시그니처를 선택합니다.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/dali-signal.h>

using ReadySignalType      = Dali::Signal<void()>;
using SelectedSignalType   = Dali::Signal<void(Dali::Ui::View)>;
using ChangedSignalType    = Dali::Signal<void(Dali::Ui::View, int)>;
using InterceptSignalType  = Dali::Signal<bool(Dali::Ui::View, int)>;
using CompletedSignalType  = Dali::Signal<bool()>;
```

`void` 시그널은 결과를 생성하지 않고 연결된 각 콜백을 호출합니다.

```cpp
class SelectionModel
{
public:
  using SelectedSignalType = Dali::Signal<void(Dali::Ui::View)>;

  SelectedSignalType& SelectedSignal()
  {
    return mSelectedSignal;
  }

  void Select(Dali::Ui::View view)
  {
    SelectedSignal().Emit(view);
  }

private:
  SelectedSignalType mSelectedSignal;
};
```

`void`가 아닌 시그널은 마지막으로 호출된 콜백의 값을 반환합니다. 연결된 콜백이 없으면 기본 생성된 값을 반환합니다.

```cpp
class NavigationModel
{
public:
  using CanLeaveSignalType = Dali::Signal<bool(Dali::Ui::View)>;

  CanLeaveSignalType& CanLeaveSignal()
  {
    return mCanLeaveSignal;
  }

  bool CanLeave(Dali::Ui::View view)
  {
    return CanLeaveSignal().Emit(view);
  }

private:
  CanLeaveSignalType mCanLeaveSignal;
};
```

인자가 두 개인 반환 시그널에서는 `EmitOr`가 콜백 결과를 비트 OR 대입으로 결합합니다. `bool` 시그널에서는 어떤 콜백이든 이벤트를 소비할 수 있는 이벤트 방식의 논리 OR 동작을 제공합니다.

```cpp
class InputRouter
{
public:
  using InputSignalType = Dali::Signal<bool(Dali::Ui::View, int)>;

  InputSignalType& InputSignal()
  {
    return mInputSignal;
  }

  bool RouteInput(Dali::Ui::View view, int inputCode)
  {
    return InputSignal().EmitOr(view, inputCode);
  }

private:
  InputSignalType mInputSignal;
};
```

## 수명과 연결 해제 관리하기 {#manage-lifetime-and-disconnection}

컨트롤러의 소멸자가 실행되기 전에 콜백 수신을 중단해야 한다면 `Dali::ConnectionTracker::DisconnectAll`을 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class PageController : public Dali::ConnectionTracker
{
public:
  using ClosedSignalType = Dali::Signal<void(Dali::Ui::View)>;

  void Attach(ClosedSignalType& closedSignal)
  {
    closedSignal.Connect(this, &PageController::OnClosed);
  }

  void Detach()
  {
    DisconnectAll();
  }

private:
  void OnClosed(Dali::Ui::View view)
  {
    mClosedView = view;
  }

  Dali::Ui::View mClosedView;
};
```

클래스가 `Dali::ConnectionTracker`를 상속할 수 없다면 `Dali::SlotDelegate`를 사용합니다. 콜백을 제공하는 객체가 `Dali::SlotDelegate`를 저장하고, 이 delegate가 내부 연결 트래커를 소유합니다.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/dali-signal.h>
#include <dali/public-api/signals/slot-delegate.h>

class PlainPageController
{
public:
  using ClosedSignalType = Dali::Signal<void(Dali::Ui::View)>;

  PlainPageController()
  : mSlotDelegate(this)
  {
  }

  void Attach(ClosedSignalType& closedSignal)
  {
    closedSignal.Connect(mSlotDelegate, &PlainPageController::OnClosed);
  }

  void Detach()
  {
    mSlotDelegate.DisconnectAll();
  }

private:
  void OnClosed(Dali::Ui::View view)
  {
    mClosedView = view;
  }

  Dali::SlotDelegate<PlainPageController> mSlotDelegate;
  Dali::Ui::View                          mClosedView;
};
```

컨트롤러가 활성 연결에 대한 진단 정보를 노출해야 한다면 `Dali::SlotDelegate::GetConnectionCount` 또는 `Dali::ConnectionTracker::GetConnectionCount`를 사용합니다.

## 람다와 함수 객체 연결하기 {#connect-lambdas-and-function-objects}

`Dali::Signal`은 함수 객체를 `Dali::ConnectionTrackerInterface*`와 함께 연결할 수 있습니다. 이는 컨트롤러가 연결 수명을 계속 소유하면서 dali-ui 뷰 콜백을 간결하게 작성할 때 유용합니다.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>

class ItemController : public Dali::ConnectionTracker
{
public:
  using PressedSignalType = Dali::Signal<void(Dali::Ui::View, bool)>;

  void Attach(PressedSignalType& pressedSignal)
  {
    pressedSignal.Connect(
      this,
      [this](Dali::Ui::View view, bool pressed)
      {
        OnPressed(view, pressed);
      });
  }

private:
  void OnPressed(Dali::Ui::View view, bool pressed)
  {
    mPressedView = view;
    mPressed     = pressed;
  }

  Dali::Ui::View mPressedView;
  bool           mPressed{false};
};
```

`Dali::FunctorDelegate::New`는 함수 객체 연결에 사용할 delegate 객체를 생성합니다. 시그널 연결은 새로 할당된 `Dali::FunctorDelegate`의 소유권을 가져갑니다.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali/public-api/signals/dali-signal.h>
#include <dali/public-api/signals/functor-delegate.h>

class RefreshController : public Dali::ConnectionTracker
{
public:
  using RefreshSignalType = Dali::Signal<void()>;

  void Attach(RefreshSignalType& refreshSignal)
  {
    refreshSignal.Connect(
      this,
      Dali::FunctorDelegate::New(
        [this]()
        {
          Refresh();
        }));
  }

private:
  void Refresh()
  {
    ++mRefreshCount;
  }

  int mRefreshCount{0};
};
```

## 애플리케이션 수준 시그널 발생시키기 {#emit-application-level-signals}

애플리케이션 코드는 재사용 가능한 dali-ui 컴포넌트 모델에서 상태 변경을 알리기 위해 자체 `Dali::Signal` 멤버를 정의할 수 있습니다. 시그널은 참조로 노출하고, 소유 클래스에서 발생시킵니다.

```cpp
#include <dali-ui-foundation/public-api/view.h>
#include <dali/public-api/signals/dali-signal.h>

class SelectionState
{
public:
  using SelectionChangedSignalType = Dali::Signal<void(Dali::Ui::View, int)>;

  SelectionChangedSignalType& SelectionChangedSignal()
  {
    return mSelectionChangedSignal;
  }

  void SetSelection(Dali::Ui::View selectedView, int selectedIndex)
  {
    mSelectedView  = selectedView;
    mSelectedIndex = selectedIndex;

    SelectionChangedSignal().Emit(mSelectedView, mSelectedIndex);
  }

private:
  SelectionChangedSignalType mSelectionChangedSignal;
  Dali::Ui::View             mSelectedView;
  int                        mSelectedIndex{0};
};
```

같은 `Dali::Signal`을 재귀적으로 발생시키지 마십시오. 시그널 구현은 `Dali::BaseSignal::EmitGuard`를 사용하여 같은 시그널의 중첩 발생을 감지합니다.

## 시그널 상태 확인하기 {#inspect-signal-state}

형식이 지정된 모든 `Dali::Signal`은 시그널 mixin이 제공하는 상태 조회 기능을 상속합니다. 연결된 콜백이 있는지 확인하려면 `Empty`를 사용하고, 진단이나 테스트 assertion에는 `GetConnectionCount`를 사용합니다.

```cpp
#include <cstdint>
#include <dali/public-api/signals/dali-signal.h>

class SaveModel
{
public:
  using SavedSignalType = Dali::Signal<void()>;

  SavedSignalType& SavedSignal()
  {
    return mSavedSignal;
  }

  const SavedSignalType& SavedSignal() const
  {
    return mSavedSignal;
  }

  bool HasSaveObservers() const
  {
    return !SavedSignal().Empty();
  }

  uint32_t GetSaveObserverCount() const
  {
    return SavedSignal().GetConnectionCount();
  }

  void Save()
  {
    if(!SavedSignal().Empty())
    {
      SavedSignal().Emit();
    }
  }

private:
  SavedSignalType mSavedSignal;
};
```

`Dali::BaseSignal`도 `Empty`, `GetConnectionCount`와 `Dali::Signal`이 사용하는 저수준 emit helper를 제공합니다. 애플리케이션 코드는 일반적으로 dali-ui 객체나 자체 컴포넌트 모델이 노출하는 형식 지정 `Dali::Signal<...>` 인터페이스를 통해 작업해야 합니다.
