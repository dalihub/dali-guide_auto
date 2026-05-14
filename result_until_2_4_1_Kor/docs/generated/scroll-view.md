---
title: 스크롤 뷰
sidebar_label: 스크롤 뷰
category: views-components
---

# 스크롤 뷰 {#scroll-view}

`Dali::Ui::ScrollView`는 보이는 뷰포트보다 큰 콘텐츠를 패닝할 수 있게 해 주는 `dali-ui` 뷰 컨테이너입니다.

## 목차 {#table-of-contents}

- [콘텐츠를 감싸는 스크롤 뷰 만들기](#create-a-scroll-view-around-content)
- [스크롤 방향 선택하기](#choose-the-scroll-direction)
- [스크롤 위치 설정하고 읽기](#set-and-read-the-scroll-position)
- [플링과 오버 스크롤 동작 조정하기](#tune-fling-and-over-scroll-behavior)
- [스크롤 바 표시 제어하기](#control-scroll-bar-visibility)
- [스크롤 및 드래그 시그널에 반응하기](#react-to-scroll-and-drag-signals)
- [`ScrollView` 핸들을 안전하게 사용하기](#use-scrollview-handles-safely)
- [플루언트 체이닝과 파생 컴포넌트](#fluent-chaining-and-derived-components)

## 콘텐츠를 감싸는 스크롤 뷰 만들기 {#create-a-scroll-view-around-content}

`Dali::Ui::ScrollView::New()`로 스크롤 컨테이너를 만든 다음, `Dali::Ui::ScrollView::SetContent()`로 콘텐츠를 연결합니다. 콘텐츠는 `Dali::Ui::View`이므로, 애플리케이션 코드는 원시 액터 객체를 직접 다루는 대신 앱 관점의 모델을 `dali-ui` 뷰 트리 안에 유지할 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

ScrollView CreateVerticalScroller(View content)
{
  ScrollView scrollView = ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetContent(content);

  return scrollView;
}
```

`Dali::Ui::ScrollView::GetContent()`는 현재 콘텐츠 뷰를 반환합니다.

```cpp
void ReplaceScrollableContent(ScrollView scrollView, View newContent)
{
  View previousContent = scrollView.GetContent();

  scrollView
    .SetScrollPosition(Vector2(0.0f, 0.0f))
    .SetContent(newContent);
}
```

## 스크롤 방향 선택하기 {#choose-the-scroll-direction}

`Dali::Ui::ScrollView::SetScrollDirection()`을 사용해 패닝과 플링 이동을 제한합니다. `ScrollDirection::Vertical`은 Y축으로 스크롤하고, `ScrollDirection::Horizontal`은 X축으로 스크롤하며, `ScrollDirection::Both`는 2차원 스크롤을 활성화합니다.

```cpp
ScrollView CreateGalleryScroller(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Horizontal)
    .SetContent(content);
}

ScrollView CreateCanvasScroller(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Both)
    .SetContent(content);
}
```

컴포넌트가 설정된 축에 맞춰 동작을 조정해야 할 때는 `Dali::Ui::ScrollView::GetScrollDirection()`으로 현재 모드를 읽습니다.

```cpp
bool UsesVerticalMotion(ScrollView scrollView)
{
  ScrollDirection direction = scrollView.GetScrollDirection();

  return direction == ScrollDirection::Vertical ||
         direction == ScrollDirection::Both;
}
```

## 스크롤 위치 설정하고 읽기 {#set-and-read-the-scroll-position}

`Dali::Ui::ScrollView::SetScrollPosition()`은 애니메이션 없이 스크롤 위치를 적용합니다. 명령형 이동에는 `Dali::Ui::ScrollView::ScrollTo()`, `Dali::Ui::ScrollView::ScrollToX()` 또는 `Dali::Ui::ScrollView::ScrollToY()`를 사용합니다. scroll-to 메서드는 선택적 `bool animation` 인자를 받으며, 기본값은 `true`입니다.

```cpp
void ResetImmediately(ScrollView scrollView)
{
  scrollView.SetScrollPosition(Vector2(0.0f, 0.0f));
}

void ScrollToTopAnimated(ScrollView scrollView)
{
  scrollView.ScrollToY(0.0f);
}

void ScrollToPointWithoutAnimation(ScrollView scrollView)
{
  scrollView.ScrollTo(Vector2(240.0f, 480.0f), false);
}
```

`Dali::Ui::ScrollView::ScrollTo()`에는 자식 뷰를 요청한 위치로 스크롤하는 오버로드도 있습니다. 기본적으로 `ScrollToPosition::MakeVisible`을 사용합니다.

```cpp
void RevealChild(ScrollView scrollView, View child)
{
  scrollView.ScrollTo(child);
}

void CenterChildWithoutAnimation(ScrollView scrollView, View child)
{
  scrollView.ScrollTo(child, false, ScrollToPosition::Center);
}
```

스크롤 시그널 콜백 안을 포함해 현재 오프셋을 조회하려면 `Dali::Ui::ScrollView::GetScrollPosition()`을 사용합니다.

```cpp
Vector2 ReadCurrentOffset(ScrollView scrollView)
{
  return scrollView.GetScrollPosition();
}

void MoveRightByPage(ScrollView scrollView, float pageWidth)
{
  Vector2 position = scrollView.GetScrollPosition();
  scrollView.ScrollToX(position.x + pageWidth);
}
```

`Dali::Ui::ScrollView::IsScrolling()`은 현재 스크롤이 진행 중인지 보고합니다.

```cpp
void SnapOnlyWhenIdle(ScrollView scrollView, float snapY)
{
  if(!scrollView.IsScrolling())
  {
    scrollView.ScrollToY(snapY);
  }
}
```

## 플링과 오버 스크롤 동작 조정하기 {#tune-fling-and-over-scroll-behavior}

플링 동작은 `Dali::Ui::ScrollView`의 타입이 지정된 setter로 구성합니다. setter는 `ScrollView&`를 반환하므로, 관련 설정을 하나의 플루언트 체인으로 묶을 수 있습니다.

```cpp
ScrollView CreateTunedScroller(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetMaxFlingDistance(6000.0f)
    .SetMinimumFlingDuration(1000)
    .SetMaximumFlingDuration(2000)
    .SetFlingSensitivity(1.0f)
    .SetDecelerationRate(0.998f)
    .SetOverScrollMode(OverScrollMode::ContentScrolls)
    .SetContent(content);
}
```

대응되는 getter를 사용하면 재사용 가능한 컴포넌트가 모션 설정을 복사하거나 검사할 수 있습니다.

```cpp
void CopyFlingPolicy(ScrollView source, ScrollView target)
{
  target
    .SetMaxFlingDistance(source.GetMaxFlingDistance())
    .SetMinimumFlingDuration(source.GetMinimumFlingDuration())
    .SetMaximumFlingDuration(source.GetMaximumFlingDuration())
    .SetFlingSensitivity(source.GetFlingSensitivity())
    .SetDecelerationRate(source.GetDecelerationRate())
    .SetOverScrollMode(source.GetOverScrollMode());
}
```

`Dali::Ui::OverScrollMode`는 오버 스크롤 동작을 위한 공개 정책 값입니다. 오버 스크롤을 사용하지 않으려면 `OverScrollMode::Never`를 사용하고, 항상 허용하려면 `OverScrollMode::Always`를 사용하며, 콘텐츠가 스크롤 가능한 경우에만 허용하려면 `OverScrollMode::ContentScrolls`를 사용합니다.

```cpp
void DisableEdgeOverScroll(ScrollView scrollView)
{
  scrollView.SetOverScrollMode(OverScrollMode::Never);
}

bool UsesContentDependentOverScroll(ScrollView scrollView)
{
  return scrollView.GetOverScrollMode() == OverScrollMode::ContentScrolls;
}
```

## 스크롤 바 표시 제어하기 {#control-scroll-bar-visibility}

스크롤 바 표시는 `Dali::Ui::ScrollView::SetVerticalScrollBarVisibility()`와 `Dali::Ui::ScrollView::SetHorizontalScrollBarVisibility()`로 각 축에 대해 독립적으로 제어합니다.

```cpp
ScrollView CreateListScroller(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never)
    .SetContent(content);
}

ScrollView CreateAlwaysVisibleTwoAxisScroller(View content)
{
  return ScrollView::New()
    .SetScrollDirection(ScrollDirection::Both)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Always)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Always)
    .SetContent(content);
}
```

스크롤 중에 스크롤 바를 표시하려면 `Dali::Ui::ScrollBarVisibility::Auto`를 사용하고, 계속 표시하려면 `Dali::Ui::ScrollBarVisibility::Always`를 사용하며, 숨기려면 `Dali::Ui::ScrollBarVisibility::Never`를 사용합니다. 구성된 상태는 `Dali::Ui::ScrollView::GetVerticalScrollBarVisibility()`와 `Dali::Ui::ScrollView::GetHorizontalScrollBarVisibility()`로 읽습니다.

```cpp
bool HasAnyVisibleScrollBarPolicy(ScrollView scrollView)
{
  return scrollView.GetVerticalScrollBarVisibility() != ScrollBarVisibility::Never ||
         scrollView.GetHorizontalScrollBarVisibility() != ScrollBarVisibility::Never;
}
```

## 스크롤 및 드래그 시그널에 반응하기 {#react-to-scroll-and-drag-signals}

`Dali::Ui::ScrollView`는 스크롤 세션 시그널과 드래그 전용 시그널을 노출합니다. `Dali::Ui::ScrollView::ScrollStartedSignal()`, `Dali::Ui::ScrollView::ScrollingSignal()`, `Dali::Ui::ScrollView::ScrollFinishedSignal()`은 `void(Dali::Ui::ScrollView)` 시그니처의 콜백을 사용합니다.

```cpp
class ScrollStatus : public Dali::ConnectionTracker
{
public:
  ScrollStatus()
  : Dali::ConnectionTracker()
  {
  }

  void Attach(ScrollView scrollView)
  {
    scrollView.ScrollStartedSignal().Connect(this, &ScrollStatus::OnScrollStarted);
    scrollView.ScrollingSignal().Connect(this, &ScrollStatus::OnScrolling);
    scrollView.ScrollFinishedSignal().Connect(this, &ScrollStatus::OnScrollFinished);
  }

  void OnScrollStarted(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }

  void OnScrolling(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }

  void OnScrollFinished(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }
};
```

`Dali::Ui::ScrollView::DragStartedSignal()`과 `Dali::Ui::ScrollView::DragFinishedSignal()`도 `void(Dali::Ui::ScrollView)`를 사용합니다. `Dali::Ui::ScrollView::DraggingSignal()`은 `void(Dali::Ui::ScrollView, float, float)`를 사용하며, 두 `float` 값은 시그널이 제공하는 드래그 델타 값입니다.

```cpp
class DragObserver : public Dali::ConnectionTracker
{
public:
  DragObserver()
  : Dali::ConnectionTracker()
  {
  }

  void Attach(ScrollView scrollView)
  {
    scrollView.DragStartedSignal().Connect(this, &DragObserver::OnDragStarted);
    scrollView.DraggingSignal().Connect(this, &DragObserver::OnDragging);
    scrollView.DragFinishedSignal().Connect(this, &DragObserver::OnDragFinished);
  }

  void OnDragStarted(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }

  void OnDragging(ScrollView scrollView, float deltaX, float deltaY)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }

  void OnDragFinished(ScrollView scrollView)
  {
    Vector2 position = scrollView.GetScrollPosition();
  }
};
```

## `ScrollView` 핸들을 안전하게 사용하기 {#use-scrollview-handles-safely}

기본 생성된 `Dali::Ui::ScrollView`는 초기화되지 않은 핸들입니다. 사용할 수 있는 스크롤 뷰를 만들 때는 `Dali::Ui::ScrollView::New()`를 사용합니다.

```cpp
ScrollView CreateInitializedScrollView(View content)
{
  ScrollView scrollView = ScrollView::New();
  scrollView.SetContent(content);
  return scrollView;
}
```

컴포넌트가 일반 핸들을 받아 이를 `ScrollView`로 다뤄야 할 때는 `Dali::Ui::ScrollView::DownCast()`를 사용합니다.

```cpp
ScrollView AsScrollView(BaseHandle handle)
{
  return ScrollView::DownCast(handle);
}
```

`Dali::Ui::ScrollView`는 `Dali::Ui::ScrollView::operator=`를 통한 이동 대입을 지원하므로, 핸들 값을 변수 사이에서 이동할 수 있습니다.

```cpp
void StoreScroller(ScrollView& target, ScrollView replacement)
{
  target = std::move(replacement);
}
```

## 플루언트 체이닝과 파생 컴포넌트 {#fluent-chaining-and-derived-components}

`Dali::Ui::ScrollView::SetContent()`, `Dali::Ui::ScrollView::SetScrollDirection()`, `Dali::Ui::ScrollView::SetOverScrollMode()` 및 스크롤 바 표시 setter와 같은 `Dali::Ui::ScrollView` setter는 `ScrollView&`를 반환합니다. 이를 통해 원시 속성 접근 없이 간결하고 타입이 지정된 설정을 작성할 수 있습니다.

```cpp
ScrollView CreateConfiguredScroller(View content)
{
  return ScrollView::New()
    .SetContent(content)
    .SetScrollPosition(Vector2(0.0f, 0.0f))
    .SetScrollDirection(ScrollDirection::Vertical)
    .SetOverScrollMode(OverScrollMode::ContentScrolls)
    .SetVerticalScrollBarVisibility(ScrollBarVisibility::Auto)
    .SetHorizontalScrollBarVisibility(ScrollBarVisibility::Never);
}
```

생성된 헤더 `scroll-view.autogen.h`는 `Dali::Ui::ScrollView`에서 파생된 dali-ui 컴포넌트를 위해 `DALI_UI_CHAIN_SCROLLVIEW_METHODS`를 제공합니다. 애플리케이션 개발자는 보통 그 결과를 `ScrollView`의 일반적인 플루언트 setter로 사용합니다. 컴포넌트 작성자는 상속된 스크롤 뷰 setter가 파생 타입을 계속 반환하도록 이 매크로를 사용할 수 있습니다.

```cpp
class FeedScrollView : public ScrollView
{
public:
  DALI_UI_CHAIN_SCROLLVIEW_METHODS(FeedScrollView)
};
```
