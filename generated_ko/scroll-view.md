---
title: 스크롤 뷰
sidebar_label: 스크롤 뷰
category: views-components
---

# 스크롤 뷰

ScrollView는 뷰포트보다 큰 콘텐츠를 표시하는 스크롤 가능한 컨테이너로, 뷰의 가시 영역을 초과하는 콘텐츠 탐색을 위한 팬 제스처 지원을 제공합니다.

## 목차

- [ScrollView 생성](#scrollview-생성)
- [콘텐츠 설정](#콘텐츠-설정)
- [스크롤 방향 구성](#스크롤-방향-구성)
- [프로그래밍 방식 스크롤](#프로그래밍-방식-스크롤)
- [플링 동작](#플링-동작)
- [스크롤 바](#스크롤-바)
- [오버스크롤 모드](#오버스크롤-모드)
- [스크롤 이벤트에 응답](#스크롤-이벤트에-응답)

## ScrollView 생성

`ScrollView::New()` 정적 메서드로 ScrollView를 생성합니다. 반환된 핸들은 구성을 위한 플루언트 체이닝을 지원합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

// ScrollView 생성 및 구성
ScrollView scrollView = ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetRequestedWidth(600.0f)
    .SetRequestedHeight(800.0f);
```

ScrollView는 `View`를 상속하므로 `Add()`를 사용하여 부모 뷰나 윈도우에 추가할 수 있습니다.

## 콘텐츠 설정

콘텐츠 뷰는 ScrollView의 뷰포트보다 클 수 있는 스크롤 가능한 영역입니다. `SetContent()`로 콘텐츠 뷰를 할당합니다.

```cpp
// 스크롤 뷰보다 큰 콘텐츠 레이아웃 생성
StackLayout content = StackLayout::New(StackOrientation::VERTICAL);
content.SetRequestedWidth(MATCH_PARENT);
content.SetRequestedHeight(WRAP_CONTENT);
content.SetSpacing(10.0f);
content.SetPadding(Extents(20, 20, 20, 20));

// 콘텐츠에 자식 뷰 추가
for (int i = 0; i < 12; ++i)
{
    View item = View::New()
        .SetBackgroundColor(Color::BLUE)
        .SetRequestedWidth(MATCH_PARENT)
        .SetRequestedHeight(180.0f);
    content.Add(item);
}

// ScrollView에 콘텐츠 설정
scrollView.SetContent(content);
```

`GetContent()`로 현재 콘텐츠를 조회합니다:

```cpp
View currentContent = scrollView.GetContent();
```

## 스크롤 방향 구성

ScrollView는 `ScrollDirection` 열거형을 통해 세 가지 스크롤 방향을 지원합니다:

- `ScrollDirection::Vertical` — 수직 스크롤만
- `ScrollDirection::Horizontal` — 수평 스크롤만
- `ScrollDirection::Both` — 수평 및 수직 스크롤 모두

```cpp
scrollView.SetScrollDirection(ScrollDirection::Vertical);
```

`GetScrollDirection()`으로 현재 방향을 조회합니다:

```cpp
ScrollDirection direction = scrollView.GetScrollDirection();
```

## 프로그래밍 방식 스크롤

`ScrollTo()`, `ScrollToX()`, `ScrollToY()` 메서드로 프로그래밍 방식으로 스크롤 위치를 제어합니다.

### 위치로 스크롤

`ScrollTo()`로 특정 `Vector2` 위치로 스크롤합니다. 선택적 두 번째 매개변수가 애니메이션을 제어합니다.

```cpp
// 애니메이션으로 위치로 스크롤
scrollView.ScrollTo(Vector2(0.0f, 500.0f));

// 애니메이션 없이 위치로 스크롤
scrollView.ScrollTo(Vector2(0.0f, 500.0f), false);
```

단일 축 스크롤은 `ScrollToX()` 또는 `ScrollToY()`를 사용합니다:

```cpp
scrollView.ScrollToY(300.0f);        // 애니메이션 있음
scrollView.ScrollToY(300.0f, false); // 애니메이션 없음
```

### 현재 위치 가져오기

`GetScrollPosition()`으로 현재 스크롤 위치를 조회합니다:

```cpp
Vector2 position = scrollView.GetScrollPosition();
float currentY = position.y;
```

### 위치 직접 설정

`SetScrollPosition()`으로 스크롤 애니메이션을 트리거하지 않고 위치를 설정합니다:

```cpp
scrollView.SetScrollPosition(Vector2(0.0f, 100.0f));
```

### 스크롤 상태 확인

`IsScrolling()`으로 현재 스크롤 중인지 확인합니다:

```cpp
if (scrollView.IsScrolling())
{
    // 스크롤 애니메이션 실행 중
}
```

## 플링 동작

ScrollView는 구성 가능한 물리 매개변수로 플링(관성) 스크롤을 지원합니다.

### 최대 플링 거리

`SetMaxFlingDistance()`로 플링이 이동할 수 있는 최대 거리를 제어합니다:

```cpp
scrollView.SetMaxFlingDistance(6000.0f);
```

### 플링 지속 시간

플링 애니메이션의 최소 및 최대 지속 시간을 설정합니다:

```cpp
scrollView.SetMinimumFlingDuration(1000); // 1초 최소
scrollView.SetMaximumFlingDuration(2000); // 2초 최대
```

### 플링 감도

제스처 속도에 대한 플링 응답 감도를 조정합니다:

```cpp
scrollView.SetFlingSensitivity(1.0f); // 기본 감도
scrollView.SetFlingSensitivity(1.5f); // 더 민감하게
scrollView.SetFlingSensitivity(0.5f); // 덜 민감하게
```

### 감속률

플링 스크롤이 얼마나 빨리 감속하는지 제어합니다. 1.0에 가까울수록 더 긴 플링 거리가 됩니다.

```cpp
scrollView.SetDecelerationRate(0.998f); // 느린 감속, 긴 플링
scrollView.SetDecelerationRate(0.95f);  // 빠른 감속, 짧은 플링
```

현재 감속률을 조회합니다:

```cpp
float rate = scrollView.GetDecelerationRate();
```

## 스크롤 바

`ScrollBarVisibility` 열거형을 사용하여 수직 및 수평 축에 대해 스크롤 바 가시성을 독립적으로 구성합니다:

- `ScrollBarVisibility::Auto` — 스크롤 시에만 스크롤 바 표시
- `ScrollBarVisibility::Always` — 항상 스크롤 바 표시
- `ScrollBarVisibility::Never` — 스크롤 바 표시 안 함

```cpp
ScrollView scrollView = ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
```

현재 가시성 설정을 조회합니다:

```cpp
ScrollBarVisibility vVisibility = scrollView.GetVerticalScrollBarVisibility();
ScrollBarVisibility hVisibility = scrollView.GetHorizontalScrollBarVisibility();
```

## 오버스크롤 모드

`OverScrollMode` 열거형으로 오버스크롤 동작을 제어합니다:

- `OverScrollMode::Never` — 오버스크롤 허용 안 함
- `OverScrollMode::Always` — 항상 오버스크롤 허용
- `OverScrollMode::ContentScrolls` — 콘텐츠가 스크롤 가능할 때만 오버스크롤 허용

```cpp
scrollView.SetOverScrollMode(OverScrollMode::ContentScrolls);
```

현재 모드를 조회합니다:

```cpp
OverScrollMode mode = scrollView.GetOverScrollMode();
```

## 스크롤 이벤트에 응답

ScrollView는 스크롤 및 드래그 이벤트 모니터링을 위한 시그널을 제공합니다. 모든 스크롤 관련 시그널은 소스 `ScrollView`를 콜백에 전달합니다.

### 스크롤 시그널

스크롤 수명 주기 시그널에 연결합니다:

```cpp
class MyController : public ConnectionTracker
{
public:
    void SetupScrollView(ScrollView scrollView)
    {
        scrollView.ScrollStartedSignal().Connect(this, &MyController::OnScrollStarted);
        scrollView.ScrollingSignal().Connect(this, &MyController::OnScrolling);
        scrollView.ScrollFinishedSignal().Connect(this, &MyController::OnScrollFinished);
    }

    void OnScrollStarted(ScrollView scrollView)
    {
        Vector2 pos = scrollView.GetScrollPosition();
        // 스크롤 시작 처리
    }

    void OnScrolling(ScrollView scrollView)
    {
        Vector2 pos = scrollView.GetScrollPosition();
        // 지속적 스크롤 처리
    }

    void OnScrollFinished(ScrollView scrollView)
    {
        Vector2 pos = scrollView.GetScrollPosition();
        // 스크롤 종료 처리
    }
};
```

### 드래그 시그널

스크롤 애니메이션과 별도로 드래그 제스처를 모니터링합니다:

```cpp
class MyController : public ConnectionTracker
{
public:
    void SetupDragSignals(ScrollView scrollView)
    {
        scrollView.DragStartedSignal().Connect(this, &MyController::OnDragStarted);
        scrollView.DraggingSignal().Connect(this, &MyController::OnDragging);
        scrollView.DragFinishedSignal().Connect(this, &MyController::OnDragFinished);
    }

    void OnDragStarted(ScrollView scrollView)
    {
        // 사용자가 드래그 제스처 시작
    }

    void OnDragging(ScrollView scrollView, float deltaX, float deltaY)
    {
        // 사용자가 드래그 중; deltaX와 deltaY에 드래그 델타 포함
    }

    void OnDragFinished(ScrollView scrollView)
    {
        // 사용자가 드래그 후 놓음
    }
};
```

`DraggingSignal()` 콜백은 ScrollView 인스턴스 외에 스크롤 델타 값(`deltaX`, `deltaY`)을 받습니다.
