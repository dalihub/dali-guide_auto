---
title: View (기본 UI 객체)
sidebar_label: View (기본 UI 객체)
category: views-components
---

# View (기본 UI 객체)

`View`는 dali-ui의 기본 UI 컴포넌트 클래스로, 레이아웃, 렌더링, 입력 처리, 상태 관리를 포함한 사용자 인터페이스 구축을 위한 필수 기능을 제공합니다.

## 목차

- [View 생성](#view-생성)
- [레이아웃 및 크기](#레이아웃-및-크기)
- [시각적 스타일링](#시각적-스타일링)
- [뷰 계층](#뷰-계층)
- [입력 및 인터랙션](#입력-및-인터랙션)
- [포커스 탐색](#포커스-탐색)
- [상태 관리](#상태-관리)
- [애니메이션](#애니메이션)

## View 생성

정적 `New()` 메서드로 `View`를 생성합니다:

```cpp
using namespace Dali::Ui;

View view = View::New();
```

클릭에 응답하는 인터랙티브 뷰는 `InteractiveView::New()`를 사용하거나 기본 `View`에 `InteractiveTrait`를 첨부합니다:

```cpp
View view = View::New()
  .AsInteractive()
  .SetBackgroundColor(UiColor(0x4488FF));
```

## 레이아웃 및 크기

### 요청 크기

`SetRequestedWidth()`와 `SetRequestedHeight()`로 원하는 크기를 설정합니다. 특수 값으로 콘텐츠 크기에 맞추는 `WRAP_CONTENT`와 부모 컨테이너를 채우는 `MATCH_PARENT`가 있습니다:

```cpp
view.SetRequestedWidth(200.0f);
view.SetRequestedHeight(MATCH_PARENT);
```

### 여백 및 패딩

`SetMargin()`으로 외부 간격을, `SetPadding()`으로 내부 간격을 구성합니다:

```cpp
view.SetMargin(Extents(10.0f, 10.0f, 5.0f, 5.0f));  // start, end, top, bottom
view.SetPadding(Extents(20.0f, 20.0f, 15.0f, 15.0f));
```

### 크기 제약

최소 및 최대 크기 제한을 적용합니다:

```cpp
view.SetMinimumWidth(100.0f);
view.SetMaximumWidth(400.0f);
view.SetMinimumHeight(50.0f);
view.SetMaximumHeight(300.0f);
```

### 레이아웃 매개변수

`SetLayoutParams()`로 레이아웃별 매개변수를 첨부합니다. 매개변수 타입은 부모 컨테이너에 따라 다릅니다:

```cpp
using namespace Dali::Ui;

View child = View::New();
child.SetLayoutParams(
  AbsoluteLayoutParams::New()
    .SetBounds(LayoutRect(50.0f, 50.0f, 100.0f, 100.0f)));
parent.Add(child);
```

### 레이아웃 모드

`SetLayoutMode()`로 뷰가 부모 레이아웃에 참여하는 방식을 제어합니다. `LayoutMode::STANDALONE`은 부모 누적에서 뷰를 제외하며, 오버레이와 절대 위치 지정에 유용합니다:

```cpp
overlayView.SetLayoutMode(LayoutMode::STANDALONE);
overlayView.SetRequestedPositionX(20.0f);
overlayView.SetRequestedPositionY(30.0f);
```

## 시각적 스타일링

### 배경색

`SetBackgroundColor()`로 단색 배경을 설정합니다. 색상 ID(테마 인식) 또는 직접 RGBA 값이 있는 `UiColor`를 전달합니다:

```cpp
view.SetBackgroundColor(UiColor(0xFF6600));       // 직접 RGBA
view.SetBackgroundColor(UiColor::PRIMARY);        // 테마 색상
```

### 모서리 반경

`SetCornerRadius()`로 둥근 모서리를 적용합니다. `SetCornerRadiusPolicy()`로 값이 절대값인지 상대값인지 제어합니다:

```cpp
view.SetCornerRadius(20.0f);  // 모든 모서리에 균일한 반경

// 개별 모서리: 왼쪽상단, 오른쪽상단, 오른쪽하단, 왼쪽하단
view.SetCornerRadius(10.0f, 20.0f, 10.0f, 20.0f);

// 상대 정책: 값이 짧은 쪽의 비율 [0.0, 0.5]
view.SetCornerRadiusPolicy(CornerRadiusPolicy::RELATIVE);
view.SetCornerRadius(0.2f);
```

### 테두리

`SetBorderlineWidth()`, `SetBorderlineColor()`, `SetBorderlineOffset()`으로 테두리를 추가합니다:

```cpp
view.SetBorderlineWidth(2.0f);
view.SetBorderlineColor(UiColor(0x333333));
view.SetBorderlineOffset(0.0f);  // 가장자리에 중앙 정렬
```

### 가시성 및 불투명도

`SetVisibility()`로 가시성을, `SetOpacity()`로 투명도를 제어합니다:

```cpp
view.SetVisibility(true);
view.SetOpacity(0.8f);

bool visible = view.IsVisible();
float opacity = view.GetOpacity();
```

## 뷰 계층

### 자식 추가

`Add()` 또는 선언적 `Children()` 메서드로 자식 뷰를 추가합니다:

```cpp
parent.Add(childView);

// 선언적 스타일
View container = View::New().Children({
  View::New().SetBackgroundColor(UiColor(0xFF0000)),
  View::New().SetBackgroundColor(UiColor(0x00FF00)),
  View::New().SetBackgroundColor(UiColor(0x0000FF))
});
```

### 자식 접근

인덱스 또는 개수로 자식을 조회합니다:

```cpp
uint32_t count = parent.GetChildCount();
View first = parent.GetChildAt(0);
int32_t index = parent.IndexOfChild(someChild);
```

### 자식 제거

모든 자식 또는 특정 뷰를 제거합니다:

```cpp
parent.Remove(childView);
parent.RemoveAllChildren();
```

### 형제 순서

`Raise()`, `Lower()` 및 관련 메서드로 시각적 z-순서를 제어합니다:

```cpp
view.Raise(LayoutOrderPolicy::PRESERVE);      // 시각만
view.RaiseToTop(LayoutOrderPolicy::UPDATE);    // 레이아웃 순서도 업데이트
view.Lower(LayoutOrderPolicy::PRESERVE);
view.LowerToBottom(LayoutOrderPolicy::PRESERVE);
```

## 입력 및 인터랙션

### 인터랙티브 Trait

`AsInteractive()`로 `InteractiveTrait`를 첨부하여 클릭, 롱 프레스, 눌림 상태 처리를 활성화합니다:

```cpp
View button = View::New()
  .AsInteractive([](InteractiveTrait trait) {
    trait.SetClickable(true);
  });
```

### 클릭 처리

`ConnectClickedSignal()`로 클릭 시그널에 연결합니다:

```cpp
view.AsInteractive();
InteractiveTrait trait = view.EnsureInteractiveTrait();
trait.ConnectClickedSignal(this, &MyClass::OnClicked);

// 핸들러 시그니처: void(View, InputEvent)
void OnClicked(View view, InputEvent event) {
  // 클릭 처리
}
```

## 포커스 탐색

### 포커스 가능

`SetFocusable()`로 키보드 포커스를 활성화합니다:

```cpp
view.SetFocusable(true);
view.SetTouchFocusable(true);  // 터치로 포커스

bool focusable = view.IsFocusable();
```

### 방향 탐색

방향 탐색을 위해 포커스 가능한 이웃을 명시적으로 설정합니다:

```cpp
view.SetLeftFocusableView(leftView);
view.SetRightFocusableView(rightView);
view.SetUpFocusableView(upView);
view.SetDownFocusableView(downView);
```

### 포커스 그룹

뷰를 포커스 그룹 경계로 표시합니다:

```cpp
view.SetProperty(View::Property::FOCUS_GROUP, true);
```

## 상태 관리

### ViewState

`GetState()`로 현재 상태를 조회하며, 결합된 상태 플래그가 포함된 `ViewState`를 반환합니다:

```cpp
const ViewState& state = view.GetState();
```

### 상태 변경

`StateChangedSignal()`에 연결하여 상태 전환에 반응합니다:

```cpp
view.StateChangedSignal().Connect(this, &MyClass::OnStateChanged);

// 핸들러 시그니처: void(View, StateEvent)
void OnStateChanged(View view, StateEvent event) {
  if (event.Added(ViewState::FOCUSED)) {
    // 뷰가 포커스 획득
  }
  if (event.Removed(ViewState::FOCUSED)) {
    // 뷰가 포커스 상실
  }
}
```

### 활성화 상태

`SetEnabled()`로 활성화 상태를 제어합니다:

```cpp
view.SetEnabled(false);  // 뷰 비활성화
bool enabled = view.IsEnabled();
bool effectivelyEnabled = view.IsEffectivelyEnabled();  // 조상도 확인
```

## 애니메이션

### 뷰 우선 애니메이션

`Animate()`로 직접 애니메이션을 위한 `ViewAnimationBridge`를 생성합니다:

```cpp
auto anim = Animation::New();
view.Animate(anim)
  .Opacity(0.0f, 300_ms, AlphaFunction::EASE_OUT)
  .PositionY(50.0f, 200_ms);
anim.Play();
```

### 애니메이션 가능 속성

일반적인 애니메이션 가능 속성에는 불투명도, 위치, 크기가 포함됩니다:

```cpp
view.Animate(anim)
  .Opacity(0.5f, 300_ms)
  .PositionX(100.0f, 200_ms)
  .Scale(1.2f, 150_ms);
```

## 선택 (SelectableTrait)

### 선택 활성화

`AsSelectable()`로 `SelectableTrait`를 첨부합니다:

```cpp
View checkbox = View::New()
  .AsSelectable([](SelectableTrait trait) {
    trait.EnableToggleByClick(true);
  });
```

### 선택 상태

프로그래밍 방식으로 선택을 조회하고 설정합니다:

```cpp
SelectableTrait trait = view.EnsureSelectableTrait();
trait.SetSelected(true);
bool selected = trait.IsSelected();
```

### 선택 변경

`SelectionChangedSignal()`에 연결합니다:

```cpp
trait.SelectionChangedSignal().Connect(this, &MyClass::OnSelectionChanged);

// 핸들러 시그니처: void(View, bool selected, InputEvent)
void OnSelectionChanged(View view, bool selected, InputEvent event) {
  if (selected) {
    // 선택 상태에 대한 시각적 업데이트
  }
}
```

## 레이아웃 방향

`SetLayoutDirection()`로 텍스트와 레이아웃 방향을 제어합니다:

```cpp
view.SetLayoutDirection(Dali::LayoutDirection::RIGHT_TO_LEFT);
view.ClearLayoutDirection();  // 상속으로 되돌리기

bool inheriting = view.IsLayoutDirectionInherited();
Dali::LayoutDirection::Type effective = view.GetEffectiveLayoutDirection();
```

## 유틸리티 메서드

### 이름 및 식별

디버그 이름을 설정합니다:

```cpp
view.SetName("submitButton");
Dali::String name = view.GetName();
```

### 장면 연결

뷰가 장면에 있는지 확인합니다:

```cpp
bool onScene = view.IsOnScene();
```

### 리소스 준비

리소스가 로드되었는지 확인합니다:

```cpp
if (view.IsResourceReady()) {
  // 리소스 로드됨
} else {
  view.ResourceReadySignal().Connect(this, &MyClass::OnResourceReady);
}
```
