---
title: 포커스 관리자
sidebar_label: 포커스 관리자
category: input-interaction
---

# 포커스 관리자 {#focus-manager}

`Dali::Ui::FocusManager`는 dali-ui 애플리케이션에서 `Dali::Ui::View` 객체의 키보드 포커스를 관리합니다.

## 목차 {#table-of-contents}

- [포커스 관리자 가져오기](#getting-the-focus-manager)
- [포커스 요청하기](#requesting-focus)
- [포커스 이동하기](#moving-focus)
- [포커스 그룹](#focus-groups)
- [포커스 표시기](#focus-indicator)
- [포커스 변경 시그널](#focus-change-signals)
- [포커스 지우기와 유지하기](#clearing-and-preserving-focus)
- [마지막 포커스 변경 장치 확인하기](#inspecting-the-last-focus-change-device)

## 포커스 관리자 가져오기 {#getting-the-focus-manager}

애플리케이션 전체에서 사용하는 포커스 관리자에 접근하려면 `Dali::Ui::FocusManager::Get()`을 사용합니다. 반환되는 `Dali::Ui::FocusManager` 핸들은 포커스 설정, 포커스 이동, 포커스 변경 감지, 포커스 동작 구성의 진입점입니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void ConfigureFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(true);
}
```

일반적인 애플리케이션 작업에서는 `Dali::Ui::FocusManager::FocusManager()`로 독립적인 `Dali::Ui::FocusManager`를 생성하지 마세요. `Dali::Ui::FocusManager::Get()`이 반환하는 싱글턴을 사용하세요.

## 포커스 요청하기 {#requesting-focus}

일반적인 애플리케이션 포커스 요청에는 `Dali::Ui::FocusManager::RequestFocus()`를 사용합니다. 이 함수는 `Dali::Ui::View`를 인자로 받고, 포커스가 적용되면 `true`를 반환합니다.

```cpp
bool FocusContent(Dali::Ui::View contentView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.RequestFocus(contentView);
}
```

대상이 컨테이너 뷰일 수 있다면 `Dali::Ui::FocusManager::RequestFocus()`를 사용하는 것이 좋습니다. 요청된 뷰가 포커스를 받을 수 있는 하위 항목으로 포커스를 위임할 수 있으면, 해석된 하위 항목이 현재 포커스 뷰가 됩니다.

지정한 `Dali::Ui::View` 자체에 정확히 포커스를 두려는 경우에는 `Dali::Ui::FocusManager::SetCurrentFocusView()`를 사용합니다.

```cpp
bool FocusExactView(Dali::Ui::View view)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.SetCurrentFocusView(view);
}
```

어느 방식으로 요청했든, 포커스가 설정된 `Dali::Ui::View`를 가져오려면 `Dali::Ui::FocusManager::GetCurrentFocusView()`를 사용합니다.

```cpp
Dali::Ui::View FocusAndReturnCurrent(Dali::Ui::View requestedView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.RequestFocus(requestedView);

  return focusManager.GetCurrentFocusView();
}
```

## 포커스 이동하기 {#moving-focus}

특정 방향으로 포커스를 이동하려면 `Dali::Ui::FocusManager::MoveFocus()`를 사용합니다. 다음 포커스 대상이 발견되어 포커스가 이동하면 `true`를 반환합니다.

```cpp
bool MoveFocusRight()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.MoveFocus(Dali::Ui::FocusDirection::RIGHT);
}
```

방향 기반 이동은 화살표 키 방식의 탐색에 사용할 수 있습니다.

```cpp
bool MoveFocusForArrowKey(Dali::Ui::FocusDirection direction)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.MoveFocus(direction);
}
```

히스토리 방식의 탐색에는 `Dali::Ui::FocusManager::MoveFocusBackward()`를 사용합니다. 이 함수는 포커스 히스토리에서 이전에 포커스가 있던 뷰를 복원하려고 시도합니다.

```cpp
void ReturnToPreviousFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.MoveFocusBackward();
}
```

## 포커스 그룹 {#focus-groups}

포커스 그룹은 뷰 하위 트리 안에서 포커스 이동 범위를 제한합니다. `Dali::Ui::FocusManager::SetAsFocusGroup()`을 사용해 `Dali::Ui::View`를 그룹으로 표시합니다.

```cpp
void EnableFocusGroup(Dali::Ui::View panel)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetAsFocusGroup(panel, true);
}
```

뷰가 현재 포커스 그룹으로 구성되어 있는지 확인하려면 `Dali::Ui::FocusManager::IsFocusGroup()`을 사용합니다.

```cpp
bool PanelTrapsFocus(Dali::Ui::View panel)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.IsFocusGroup(panel);
}
```

뷰에 가장 가까운 포커스 그룹을 찾으려면 `Dali::Ui::FocusManager::GetFocusGroup()`을 사용합니다.

```cpp
Dali::Ui::View FindOwningFocusGroup(Dali::Ui::View focusedChild)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetFocusGroup(focusedChild);
}
```

포커스 그룹은 애플리케이션이 명시적으로 다른 위치에 포커스를 요청하기 전까지 `Dali::Ui::FocusManager::MoveFocus()`가 로컬 영역 안에 머물러야 하는 모달 패널, 팝업, 복합 컨트롤에서 특히 유용합니다.

## 포커스 표시기 {#focus-indicator}

`Dali::Ui::FocusManager`는 포커스 표시기 구성을 소유합니다. 포커스 표시기를 사용자 지정 `Dali::Ui::View`로 교체하려면 `Dali::Ui::FocusManager::SetFocusIndicatorActor()`를 사용합니다.

```cpp
void InstallFocusIndicator(Dali::Ui::View indicatorView)
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetFocusIndicatorActor(indicatorView);
}
```

현재 표시기 뷰를 가져오려면 `Dali::Ui::FocusManager::GetFocusIndicatorView()`를 사용합니다.

```cpp
Dali::Ui::View GetCurrentFocusIndicator()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetFocusIndicatorView();
}
```

설정 함수 이름에 `Actor`가 포함되어 있지만, 애플리케이션 코드는 표시기 객체로 `Dali::Ui::View`를 전달해야 합니다.

## 포커스 변경 시그널 {#focus-change-signals}

관리자 수준에서 포커스 변경을 관찰하려면 `Dali::Ui::FocusManager::FocusChangedSignal()`을 사용합니다. 콜백은 이전에 포커스가 있던 `Dali::Ui::View`와 새로 포커스가 설정된 `Dali::Ui::View`를 받습니다.

```cpp
class FocusController
{
public:
  void ConnectFocusSignal()
  {
    Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

    focusManager.FocusChangedSignal().Connect(
      this,
      &FocusController::OnFocusChanged);
  }

private:
  void OnFocusChanged(Dali::Ui::View previousFocus, Dali::Ui::View currentFocus)
  {
    mPreviousFocus = previousFocus;
    mCurrentFocus  = currentFocus;
  }

  Dali::Ui::View mPreviousFocus;
  Dali::Ui::View mCurrentFocus;
};
```

명령 사용 가능 상태를 갱신하거나 포커스에 의존하는 UI 상태를 동기화하는 경우처럼, 애플리케이션 상태가 전역 포커스 소유권에 따라 달라질 때는 관리자 수준 시그널을 사용합니다.

## 포커스 지우기와 유지하기 {#clearing-and-preserving-focus}

현재 뷰에서 포커스를 제거하려면 `Dali::Ui::FocusManager::ClearFocus()`를 사용합니다.

```cpp
void ClearApplicationFocus()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.ClearFocus();
}
```

창이 포커스를 잃을 때 포커스를 지울지 여부를 구성하려면 `Dali::Ui::FocusManager::SetClearFocusOnWindowFocusLost()`를 사용합니다. 현재 설정은 `Dali::Ui::FocusManager::GetClearFocusOnWindowFocusLost()`로 확인할 수 있습니다.

```cpp
bool PreserveFocusWhileWindowIsInactive()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  focusManager.SetClearFocusOnWindowFocusLost(false);

  return focusManager.GetClearFocusOnWindowFocusLost();
}
```

애플리케이션 창의 활성화가 해제된 뒤 포커스를 초기화해야 한다면 지우기를 활성화합니다. 복원을 위해 앱이 이전 포커스 상태를 유지해야 한다면 비활성화합니다.

## 마지막 포커스 변경 장치 확인하기 {#inspecting-the-last-focus-change-device}

가장 최근의 포커스 변경 원인을 확인하려면 `Dali::Ui::FocusManager::GetLastFocusChangeDevice()`를 사용합니다. 공개 포커스 관리자 호출을 통해 발생한 포커스 변경의 경우 장치는 `Dali::Ui::FocusDevice::PROGRAMMATIC`일 수 있습니다.

```cpp
bool LastFocusChangeWasProgrammatic()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetLastFocusChangeDevice() ==
         Dali::Ui::FocusDevice::PROGRAMMATIC;
}
```

장치별 포커스 동작에 장치 이름이 필요한 경우 `Dali::Ui::FocusManager::GetLastFocusChangeDeviceName()`을 사용합니다.

```cpp
const Dali::String& GetLastFocusDeviceName()
{
  Dali::Ui::FocusManager focusManager = Dali::Ui::FocusManager::Get();

  return focusManager.GetLastFocusChangeDeviceName();
}
```
