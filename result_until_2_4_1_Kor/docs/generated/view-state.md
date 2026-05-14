---
title: 뷰 상태
sidebar_label: 뷰 상태
category: styling-theme-config
---

# 뷰 상태 {#view-state}

`Dali::Ui::ViewState`는 `Dali::Ui::View`의 하나 이상의 시각적 상태와 상호작용 상태를 나타내는 간결한 값입니다. 이 값은 조합하고, 조회하고, 비교할 수 있습니다.

## 목차 {#table-of-contents}

- [뷰 상태 표현](#representing-view-state)
- [내장 상태 결합](#combining-built-in-states)
- [스타일링 결정을 위한 상태 검사](#testing-state-for-styling-decisions)
- [사용자 지정 상태 생성](#creating-custom-states)
- [상태 전환 감지](#detecting-state-transitions)
- [상태 값 디버깅](#debugging-state-values)

## 뷰 상태 표현 {#representing-view-state}

애플리케이션 코드에서 `Dali::Ui::View`의 상태를 판단해야 할 때 `Dali::Ui::ViewState`를 사용합니다. 기본 생성된 `Dali::Ui::ViewState`는 설정된 상태 비트가 없는 `Dali::Ui::ViewState::NORMAL`과 같습니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

void CheckInitialState()
{
  ViewState state;

  if(state.IsNormal())
  {
    // The state is ViewState::NORMAL.
  }

  if(state == ViewState::NORMAL)
  {
    // Default construction and ViewState::NORMAL are equivalent.
  }

  if(!static_cast<bool>(state))
  {
    // ViewState::NORMAL has no active bits.
  }
}
```

상태 값에 활성 비트가 하나라도 있는지만 확인하면 되는 경우 `Dali::Ui::ViewState::operator bool`이 유용합니다. 코드에서 `Dali::Ui::ViewState::NORMAL` 여부를 명확히 검사하는 경우에는 `IsNormal()`을 사용하는 것이 좋습니다.

## 내장 상태 결합 {#combining-built-in-states}

`Dali::Ui::ViewState`는 비트마스크 값으로 설계되었습니다. `Dali::Ui::ViewState::FOCUSED`, `Dali::Ui::ViewState::PRESSED`, `Dali::Ui::ViewState::DISABLED`, `Dali::Ui::ViewState::PSEUDO_DISABLED`, `Dali::Ui::ViewState::SELECTED` 같은 내장 상태는 `operator+` 또는 `operator|`로 결합할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

ViewState MakeInteractiveState(bool focused, bool pressed, bool selected)
{
  ViewState state = ViewState::NORMAL;

  if(focused)
  {
    state = state + ViewState::FOCUSED;
  }

  if(pressed)
  {
    state = state + ViewState::PRESSED;
  }

  if(selected)
  {
    state = state + ViewState::SELECTED;
  }

  return state;
}
```

자주 쓰는 몇 가지 조합은 이미 제공됩니다. 스타일링하려는 뷰 동작이 해당 조합과 정확히 일치한다면 `Dali::Ui::ViewState::SELECTED_PRESSED`, `Dali::Ui::ViewState::DISABLED_SELECTED`, `Dali::Ui::ViewState::SELECTED_FOCUSED`를 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

void UseCompositeStates()
{
  ViewState selectedPressed = ViewState::SELECTED_PRESSED;

  if(selectedPressed.Contains(ViewState::SELECTED) &&
     selectedPressed.Contains(ViewState::PRESSED))
  {
    // Apply selected-and-pressed styling.
  }

  ViewState selectedFocused = ViewState::SELECTED_FOCUSED;

  if(selectedFocused.Contains(ViewState::FOCUSED))
  {
    // Apply focused styling for a selected view.
  }
}
```

상태 비트를 제거하려면 `Dali::Ui::ViewState::operator-`를 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

ViewState ClearPressed(ViewState state)
{
  return state - ViewState::PRESSED;
}
```

## 스타일링 결정을 위한 상태 검사 {#testing-state-for-styling-decisions}

스타일링 규칙에서 다른 `Dali::Ui::ViewState`의 모든 비트가 존재해야 한다면 `Contains()`를 사용합니다. 일부라도 겹치면 충분한 경우에는 `HasIntersectionWith()`를 사용합니다. `Dali::Ui::ViewState::NORMAL`에는 비트가 없으므로 `Contains(Dali::Ui::ViewState::NORMAL)`은 모든 상태에서 참입니다. 활성 상태 비트가 전혀 없는지 검사해야 할 때는 `IsNormal()`을 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

enum class VisualRole
{
  Normal,
  Focused,
  Pressed,
  Disabled,
  SelectedPressed
};

VisualRole ChooseVisualRole(ViewState state)
{
  if(state.IsAnyDisabled())
  {
    return VisualRole::Disabled;
  }

  if(state.Contains(ViewState::SELECTED_PRESSED))
  {
    return VisualRole::SelectedPressed;
  }

  if(state.Contains(ViewState::PRESSED))
  {
    return VisualRole::Pressed;
  }

  if(state.Contains(ViewState::FOCUSED))
  {
    return VisualRole::Focused;
  }

  return VisualRole::Normal;
}
```

`Dali::Ui::ViewState::IsAnyDisabled()`는 `Dali::Ui::ViewState::DISABLED`와 `Dali::Ui::ViewState::PSEUDO_DISABLED`를 모두 검사합니다. 테마에서 두 비활성화 형태에 동일한 기본 시각 처리를 적용하려는 경우에 유용합니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

bool ShouldUseDimmedPalette(ViewState state)
{
  return state.IsAnyDisabled();
}
```

마스크 방식의 검사에서는 `HasIntersectionWith()`를 사용하면 조건을 간결하게 유지할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

bool NeedsAttentionStyle(ViewState state)
{
  const ViewState attentionMask = ViewState::FOCUSED + ViewState::PRESSED;
  return state.HasIntersectionWith(attentionMask);
}
```

## 사용자 지정 상태 생성 {#creating-custom-states}

애플리케이션이나 컴포넌트에서 내장 집합에 없는 이름 있는 상태가 필요할 때 `Dali::Ui::ViewState::Create`를 사용합니다. 같은 이름으로 `Create`를 다시 호출하면 동일한 상태 값이 반환되므로, 사용자 지정 상태를 한 번 선언한 뒤 스타일링 로직에서 재사용할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

ViewState LoadingState()
{
  static const ViewState loading = ViewState::Create("Loading");
  return loading;
}

ViewState MakeLoadingSelectionState()
{
  return LoadingState() + ViewState::SELECTED;
}
```

이름 `"Normal"`과 `"All"`은 예약되어 있습니다. `Dali::Ui::ViewState::Create("Normal")`은 `Dali::Ui::ViewState::NORMAL`을 반환하고, `Dali::Ui::ViewState::Create("All")`은 `Dali::Ui::ViewState::ALL`을 반환합니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

void CheckReservedNames()
{
  ViewState normal = ViewState::Create("Normal");
  ViewState all    = ViewState::Create("All");

  if(normal == ViewState::NORMAL)
  {
    // Reserved normal state.
  }

  if(all == ViewState::ALL)
  {
    // Reserved all-states mask.
  }
}
```

## 상태 전환 감지 {#detecting-state-transitions}

애플리케이션 로직에 이전 `Dali::Ui::ViewState`와 현재 `Dali::Ui::ViewState`가 있을 때는 관심 있는 상태에 전환 헬퍼를 사용합니다. `WasAdded()`는 이전 상태에는 없고 현재 상태에는 있는 비트를 감지합니다. `WasRemoved()`는 그 반대의 경우를 감지합니다. `WasChanged()`는 단일 상태 또는 정확한 복합 마스크에 대해 어느 방향의 변경이든 감지합니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

void UpdatePressedVisual(ViewState previous, ViewState current)
{
  if(ViewState::PRESSED.WasAdded(previous, current))
  {
    // Start pressed visual feedback.
  }

  if(ViewState::PRESSED.WasRemoved(previous, current))
  {
    // End pressed visual feedback.
  }
}
```

앱 코드에서는 `Dali::Ui::View::StateChangedSignal()`이 `Dali::Ui::StateEvent`를 통해 이전 상태와 현재 상태를 제공합니다. 뷰 콜백을 직접 처리하는 경우에는 이벤트 헬퍼를 사용합니다.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/view.h>

using Dali::ConnectionTracker;
using Dali::Ui::StateEvent;
using Dali::Ui::View;
using Dali::Ui::ViewState;

void ConnectPressedStateHandler(View view, ConnectionTracker& tracker)
{
  view.StateChangedSignal().Connect(&tracker, [](View, const StateEvent& event) {
    if(event.Added(ViewState::PRESSED))
    {
      // Start pressed visual feedback.
    }

    if(event.Removed(ViewState::PRESSED))
    {
      // End pressed visual feedback.
    }
  });
}
```

복합 감시 마스크에는 `AnyChanged()`를 사용합니다. 이 함수는 수신자에 포함된 비트 중 하나라도 이전 값과 현재 값 사이에서 변경되면 참을 반환합니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

bool InteractionVisualNeedsRefresh(ViewState previous, ViewState current)
{
  const ViewState interactionMask = ViewState::FOCUSED +
                                    ViewState::PRESSED +
                                    ViewState::SELECTED;

  return interactionMask.AnyChanged(previous, current);
}
```

`WasChanged()`는 `Dali::Ui::ViewState::FOCUSED` 같은 단일 상태에 가장 적합합니다. 수신자가 여러 상태로 만든 마스크라면 `AnyChanged()`가 더 적합합니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

bool FocusOrPressChanged(ViewState previous, ViewState current)
{
  const ViewState focusOrPress = ViewState::FOCUSED + ViewState::PRESSED;
  return focusOrPress.AnyChanged(previous, current);
}
```

## 상태 값 디버깅 {#debugging-state-values}

읽기 쉬운 진단 정보를 만들려면 `ToString()`을 사용합니다. `Dali::Ui::ViewState::NORMAL`은 `"Normal"`로, `Dali::Ui::ViewState::ALL`은 `"All"`로 형식화되며, 복합 상태에는 등록된 상태 이름이 포함됩니다. 복합 문자열에서 이름이 나오는 순서에 의존하지 마십시오.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

auto DescribeState(ViewState state)
{
  return state.ToString();
}
```

`operator&`, `operator^`, `operator~` 같은 연산자는 더 직접적인 비트마스크 동작이 필요할 때 진단과 마스크 계산에 유용합니다.

```cpp
#include <dali-ui-foundation/public-api/view-state.h>

using Dali::Ui::ViewState;

ViewState ChangedBits(ViewState previous, ViewState current)
{
  return previous ^ current;
}

ViewState InteractionBits(ViewState state)
{
  const ViewState interactionMask = ViewState::FOCUSED +
                                    ViewState::PRESSED +
                                    ViewState::SELECTED;

  return state & interactionMask;
}

bool HasNoFocusedBit(ViewState state)
{
  return !static_cast<bool>(state & ViewState::FOCUSED);
}
```
