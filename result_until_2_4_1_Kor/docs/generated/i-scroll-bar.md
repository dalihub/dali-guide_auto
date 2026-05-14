---
title: I 스크롤 바
sidebar_label: I 스크롤 바
category: views-components
---

# I 스크롤 바 {#i-scroll-bar}

`Dali::Ui::IScrollBar`는 스크롤 가능한 `Dali::Ui::View`와 스크롤 표시기를 동기화하기 위한 dali-ui 인터페이스입니다.

## 목차 {#table-of-contents}

- [dali-ui View에서 `IScrollBar` 사용하기](#use-iscrollbar-from-a-dali-ui-view)
- [세로 및 가로 표시 여부 선택하기](#choose-vertical-and-horizontal-visibility)
- [콘텐츠 또는 뷰포트 크기가 변경될 때 바 업데이트하기](#update-the-bar-when-content-or-viewport-size-changes)
- [스크롤 위치와 바 동기화하기](#synchronize-the-bar-with-scroll-position)
- [스크롤이 끝난 뒤 자동 바 숨기기](#hide-automatic-bars-after-scrolling-ends)

## dali-ui View에서 `IScrollBar` 사용하기 {#use-iscrollbar-from-a-dali-ui-view}

`Dali::Ui::IScrollBar`는 인터페이스입니다. 애플리케이션 코드는 일반적으로 이 인터페이스를 구현한 객체를 사용하며, 뷰와 맞닿는 코드는 원시 액터가 아니라 `Dali::Ui::View`를 기준으로 유지합니다. 스크롤 바 구현에서 반환하는 애플리케이션 측 뷰가 필요할 때는 `Dali::Ui::IScrollBar::GetTarget`을 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/view.h>

void InspectScrollBarTarget(Dali::Ui::IScrollBar& scrollBar)
{
  Dali::Ui::View targetView = scrollBar.GetTarget();

  if(targetView)
  {
    // Keep application logic View-based.
  }
}
```

`Dali::Ui::ScrollBar`는 `Dali::Ui::IScrollBar`를 구현하는 공개 구체 스크롤 바 타입입니다. 재사용 가능한 애플리케이션 코드에서는 인터페이스를 사용하고, 뷰 구성 코드에서 스크롤 바 뷰가 필요할 때 구체 타입을 생성하거나 설정합니다.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scroll-bar.h>

void ConfigureAnyScrollBar(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

## 세로 및 가로 표시 여부 선택하기 {#choose-vertical-and-horizontal-visibility}

`Dali::Ui::IScrollBar`는 세로 바와 가로 바에 대해 별도의 표시 설정을 제공합니다. 뷰의 스크롤 방향에 맞게 `Dali::Ui::IScrollBar::SetVerticalScrollBarVisibility`와 `Dali::Ui::IScrollBar::SetHorizontalScrollBarVisibility`를 사용합니다.

`Dali::Ui::ScrollBarVisibility::Auto`는 스크롤 중에 바를 표시합니다. `Dali::Ui::ScrollBarVisibility::Always`는 바를 항상 보이게 유지합니다. `Dali::Ui::ScrollBarVisibility::Never`는 해당 축을 비활성화합니다.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void ConfigureVerticalListBar(Dali::Ui::IScrollBar& scrollBar)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
}
```

현재 정책은 `Dali::Ui::IScrollBar::GetVerticalScrollBarVisibility`와 `Dali::Ui::IScrollBar::GetHorizontalScrollBarVisibility`로 읽을 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

bool UsesPersistentVerticalBar(const Dali::Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetVerticalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Always;
}

bool HasHorizontalBarEnabled(const Dali::Ui::IScrollBar& scrollBar)
{
  return scrollBar.GetHorizontalScrollBarVisibility() != Dali::Ui::ScrollBarVisibility::Never;
}
```

## 콘텐츠 또는 뷰포트 크기가 변경될 때 바 업데이트하기 {#update-the-bar-when-content-or-viewport-size-changes}

스크롤 가능한 콘텐츠 크기 또는 보이는 뷰포트 크기가 변경되면 `Dali::Ui::IScrollBar::UpdateBarSize`를 호출합니다. 인수는 스크롤 가능한 너비, 스크롤 가능한 높이, 뷰포트 너비, 뷰포트 높이입니다.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void OnScrollableGeometryChanged(Dali::Ui::IScrollBar& scrollBar,
                                 float contentWidth,
                                 float contentHeight,
                                 float viewportWidth,
                                 float viewportHeight)
{
  scrollBar.UpdateBarSize(contentWidth, contentHeight, viewportWidth, viewportHeight);
}
```

가로 오버플로가 없는 세로 목록에서는 스크롤 가능한 너비와 뷰포트 너비 모두에 뷰포트 너비를 전달합니다. 이렇게 하면 가로 범위가 뷰포트 안에 들어온다는 정보를 기록하면서도, 세로 크기 계산에 필요한 치수를 바에 제공할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void ConfigureVerticalContent(Dali::Ui::IScrollBar& scrollBar,
                              float contentHeight,
                              float viewportWidth,
                              float viewportHeight)
{
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);

  scrollBar.UpdateBarSize(viewportWidth, contentHeight, viewportWidth, viewportHeight);
}
```

## 스크롤 위치와 바 동기화하기 {#synchronize-the-bar-with-scroll-position}

대상 뷰의 스크롤 위치가 변경되면 `Dali::Ui::IScrollBar::UpdateScrollPosition`을 호출합니다. 위치는 `Dali::Vector2`이며, `x`는 가로 스크롤 오프셋이고 `y`는 세로 스크롤 오프셋입니다.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void OnScrollPositionChanged(Dali::Ui::IScrollBar& scrollBar, const Dali::Vector2& position)
{
  scrollBar.UpdateScrollPosition(position);
}
```

세로 전용 콘텐츠에서는 `x`를 `0.0f`로 유지하고 세로 오프셋을 `y`로 전달합니다.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void UpdateVerticalOffset(Dali::Ui::IScrollBar& scrollBar, float scrollY)
{
  scrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, scrollY));
}
```

두 축으로 스크롤되는 콘텐츠에서는 대상 `Dali::Ui::View`를 구동하는 동일한 스크롤 상태에서 두 오프셋을 모두 전달합니다.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>

void UpdateTwoAxisOffset(Dali::Ui::IScrollBar& scrollBar, float scrollX, float scrollY)
{
  Dali::Vector2 position(scrollX, scrollY);
  scrollBar.UpdateScrollPosition(position);
}
```

## 스크롤이 끝난 뒤 자동 바 숨기기 {#hide-automatic-bars-after-scrolling-ends}

`Dali::Ui::IScrollBar::HideBar`는 `Dali::Ui::ScrollBarVisibility::Auto`를 사용하는 바를 페이드아웃합니다. 자체 스크롤 상호작용이 끝났고 자동 바를 즉시 숨기려면 이 함수를 호출합니다.

```cpp
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

void OnScrollGestureFinished(Dali::Ui::IScrollBar& scrollBar)
{
  if(scrollBar.GetVerticalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Auto ||
     scrollBar.GetHorizontalScrollBarVisibility() == Dali::Ui::ScrollBarVisibility::Auto)
  {
    scrollBar.HideBar();
  }
}
```

일반적인 수동 스크롤 바 업데이트 흐름에서는 표시 여부를 한 번 설정하고, 레이아웃이 변경될 때 크기를 업데이트하며, 스크롤 중에는 위치를 업데이트하고, 상호작용이 끝나면 자동 바를 숨깁니다.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/public-api/i-scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

class ScrollBarController
{
public:
  explicit ScrollBarController(Dali::Ui::IScrollBar& scrollBar)
  : mScrollBar(scrollBar)
  {
    mScrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
    mScrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);
  }

  void OnLayout(float contentHeight, float viewportWidth, float viewportHeight)
  {
    mScrollBar.UpdateBarSize(viewportWidth, contentHeight, viewportWidth, viewportHeight);
  }

  void OnScroll(float scrollY)
  {
    mScrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, scrollY));
  }

  void OnScrollFinished()
  {
    mScrollBar.HideBar();
  }

private:
  Dali::Ui::IScrollBar& mScrollBar;
};
```
