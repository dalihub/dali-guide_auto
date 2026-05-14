---
title: 포커스 매니저
sidebar_label: 포커스 매니저
category: input-interaction
---

# 포커스 매니저

포커스 매니저는 dali-ui 애플리케이션을 위한 키보드 포커스 탐색을 제공하고 포커스 체인을 유지합니다. 여러 방향으로 포커스 이동을 처리하고, 포커스 그룹을 관리하며, 포커스가 변경될 때 시그널을 발생시킵니다.

## 목차

- [포커스 매니저 가져오기](#포커스-매니저-가져오기)
- [포커스 설정](#포커스-설정)
- [포커스 이동](#포커스-이동)
- [포커스 그룹](#포커스-그룹)
- [포커스 표시기](#포커스-표시기)
- [포커스 변경 시그널](#포커스-변경-시그널)
- [윈도우 포커스 동작](#윈도우-포커스-동작)
- [포커스 장치 정보](#포커스-장치-정보)

## 포커스 매니저 가져오기

`FocusManager`는 싱글톤입니다. `FocusManager::Get()`으로 접근합니다:

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali::Ui;

// 싱글톤 인스턴스 가져오기
FocusManager focusManager = FocusManager::Get();
```

## 포커스 설정

### 포커스 요청

`RequestFocus()`를 사용하여 뷰에 포커스를 요청합니다. 이 메서드는 자식 위임을 지원합니다: 대상이 포커스 가능한 자식이 있는 컨테이너면, 포커스가 첫 번째 적격 자식에게 위임됩니다.

```cpp
View view = View::New().SetFocusable(true);
scene.Add(view);

// 포커스 요청 - 성공하면 true 반환
bool success = FocusManager::Get().RequestFocus(view);
```

뷰는 포커스 가능하고 장면에 있어야 합니다. `SetFocusable(true)`를 호출하여 뷰를 포커스 가능하게 만듭니다:

```cpp
View button = View::New().SetFocusable(true);
```

### 직접 포커스 설정

자식 위임 없이 직접 포커스를 설정하려면 `SetCurrentFocusView()`를 사용합니다:

```cpp
View view = View::New().SetFocusable(true);
scene.Add(view);

// 지정된 뷰에 직접 포커스 설정
bool success = FocusManager::Get().SetCurrentFocusView(view);
```

### 현재 포커스 조회

`GetCurrentFocusView()`로 현재 포커스된 뷰를 조회합니다:

```cpp
View currentFocus = FocusManager::Get().GetCurrentFocusView();
if (currentFocus)
{
  // 현재 포커스된 뷰가 있음
}
```

### 포커스 해제

`ClearFocus()`로 모든 뷰에서 포커스를 제거합니다:

```cpp
FocusManager::Get().ClearFocus();
```

## 포커스 이동

### 방향성 포커스 이동

`MoveFocus()`와 `FocusDirection`을 사용하여 프로그래밍 방식으로 포커스를 이동합니다:

```cpp
// 포커스를 오른쪽으로 이동
bool moved = FocusManager::Get().MoveFocus(FocusDirection::RIGHT);

// 포커스를 왼쪽으로 이동
FocusManager::Get().MoveFocus(FocusDirection::LEFT);

// 포커스를 위로 이동
FocusManager::Get().MoveFocus(FocusDirection::UP);

// 포커스를 아래로 이동
FocusManager::Get().MoveFocus(FocusDirection::DOWN);
```

사용 가능한 `FocusDirection` 값:

- `FocusDirection::LEFT` - 포커스를 왼쪽으로 이동
- `FocusDirection::RIGHT` - 포커스를 오른쪽으로 이동
- `FocusDirection::UP` - 포커스를 위로 이동
- `FocusDirection::DOWN` - 포커스를 아래로 이동
- `FocusDirection::FORWARD` - 탐색 순서대로 앞으로 이동
- `FocusDirection::BACKWARD` - 탐색 순서의 뒤로 이동
- `FocusDirection::PAGE_UP` - 이전 페이지로 이동
- `FocusDirection::PAGE_DOWN` - 다음 페이지로 이동
- `FocusDirection::CLOCKWISE` - 시계 방향으로 이동
- `FocusDirection::COUNTER_CLOCKWISE` - 반시계 방향으로 이동

### 앞으로 및 뒤로 탐색

순차 탐색을 위해 `FORWARD`와 `BACKWARD` 방향을 사용합니다:

```cpp
// 다음 포커스 가능한 뷰로 이동
FocusManager::Get().MoveFocus(FocusDirection::FORWARD);

// 이전 포커스 가능한 뷰로 이동
FocusManager::Get().MoveFocus(FocusDirection::BACKWARD);
```

### 포커스 뒤로 이동

`MoveFocusBackward()`로 이전에 포커스된 뷰로 돌아갑니다:

```cpp
FocusManager::Get().MoveFocusBackward();
```

## 포커스 그룹

포커스 그룹은 뷰의 서브트리 내에 포커스를 가둡니다. 뷰가 포커스 그룹으로 설정되면 키보드 탐색이 그 경계를 벗어날 수 없습니다.

### 포커스 그룹 생성

```cpp
View groupContainer = View::New();
FocusManager::Get().SetAsFocusGroup(groupContainer, true);
```

### 포커스 그룹 상태 확인

```cpp
bool isGroup = FocusManager::Get().IsFocusGroup(view);
```

### 포함하는 포커스 그룹 가져오기

뷰를 포함하는 포커스 그룹을 찾습니다:

```cpp
View focusGroup = FocusManager::Get().GetFocusGroup(view);
```

## 포커스 표시기

포커스 매니저는 포커스된 뷰에 시각적 표시기를 표시합니다. 이 표시기를 커스터마이즈할 수 있습니다.

### 커스텀 포커스 표시기 설정

```cpp
View customIndicator = View::New();
// 표시기 외관 구성...
FocusManager::Get().SetFocusIndicatorActor(customIndicator);
```

### 포커스 표시기 가져오기

```cpp
View indicator = FocusManager::Get().GetFocusIndicatorView();
```

## 포커스 변경 시그널

포커스 변경에 반응하려면 `FocusChangedSignal()`에 연결합니다:

```cpp
void OnFocusChanged(View previouslyFocused, View currentlyFocused)
{
  if (previouslyFocused)
  {
    // 포커스 상실 처리
  }
  if (currentlyFocused)
  {
    // 포커스 획득 처리
  }
}

// 콜백 연결
FocusManager::Get().FocusChangedSignal().Connect(&OnFocusChanged);
```

시그널은 이전에 포커스된 뷰와 새로 포커스된 뷰를 모두 제공합니다.

## 윈도우 포커스 동작

윈도우가 포커스를 잃을 때 포커스를 해제할지 여부를 제어합니다:

### 윈도우 포커스 상실 시 해제 설정

```cpp
// 윈도우가 포커스를 잃을 때 포커스 해제 활성화 (기본값)
FocusManager::Get().SetClearFocusOnWindowFocusLost(true);

// 포커스 상태 유지를 위해 비활성화
FocusManager::Get().SetClearFocusOnWindowFocusLost(false);
```

### 현재 설정 가져오기

```cpp
bool clearsOnLost = FocusManager::Get().GetClearFocusOnWindowFocusLost();
```

## 포커스 장치 정보

마지막 포커스 변경을 일으킨 입력 장치를 확인합니다:

### 포커스 장치 타입 가져오기

```cpp
FocusDevice device = FocusManager::Get().GetLastFocusChangeDevice();

switch (device)
{
  case FocusDevice::KEYBOARD:
    // 키보드가 포커스 변경을 시작
    break;
  case FocusDevice::MOUSE:
    // 마우스 클릭이 포커스 변경을 시작
    break;
  case FocusDevice::TOUCH:
    // 터치가 포커스 변경을 시작
    break;
  case FocusDevice::PROGRAMMATIC:
    // API 호출이 포커스 변경을 시작
    break;
  // 기타 장치 타입...
}
```

사용 가능한 `FocusDevice` 값:

- `FocusDevice::UNKNOWN` - 알 수 없는 장치
- `FocusDevice::KEYBOARD` - 키보드 또는 연결된 버튼
- `FocusDevice::MOUSE` - 마우스, 트랙볼 또는 터치패드
- `FocusDevice::TOUCH` - 터치스크린 또는 스타일러스
- `FocusDevice::PEN` - 펜 장치
- `FocusDevice::POINTER` - 레이저 또는 적외선 포인터
- `FocusDevice::GAMEPAD` - 게임패드 또는 조이스틱
- `FocusDevice::WHEEL` - 마우스 휠
- `FocusDevice::PROGRAMMATIC` - 장치가 아닌 API 호출

### 장치 이름 가져오기

```cpp
const Dali::String& deviceName = FocusManager::Get().GetLastFocusChangeDeviceName();
```
