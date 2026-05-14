---
title: 스크롤 바
sidebar_label: 스크롤 바
category: views-components
---

# 스크롤 바

스크롤 바 컴포넌트는 스크롤 가능한 콘텐츠 위치에 대한 시각적 피드백을 제공하고 사용자가 스크롤 가능한 뷰를 탐색할 수 있게 합니다.

## 목차

- [스크롤 바 생성](#스크롤-바-생성)
- [바 외관 구성](#바-외관-구성)
- [가시성 제어](#가시성-제어)
- [스크롤 위치 관리](#스크롤-위치-관리)
- [ScrollView와 함께 사용](#scrollview와-함께-사용)

## 스크롤 바 생성

정적 `New()` 메서드로 `ScrollBar` 인스턴스를 생성합니다:

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>

using namespace Dali::Ui;

ScrollBar scrollBar = ScrollBar::New();
```

`ScrollBar` 클래스는 `AbsoluteLayout`을 상속하므로 다른 뷰처럼 위치와 크기를 지정할 수 있습니다. 기본 핸들을 `ScrollBar`로 다운캐스트하려면 `ScrollBar::DownCast()`를 사용합니다:

```cpp
BaseHandle handle = scrollBar;
ScrollBar casted = ScrollBar::DownCast(handle);
if (casted)
{
  // 다운캐스트 성공
}
```

## 바 외관 구성

### 두께 및 색상

`SetBarThickness()`로 스크롤 바 두께를 설정합니다:

```cpp
scrollBar.SetBarThickness(10.0f);
float thickness = scrollBar.GetBarThickness(); // 10.0f 반환
```

`SetBarColor()`로 스크롤 바 색상을 설정합니다:

```cpp
scrollBar.SetBarColor(Vector4(0.2f, 0.4f, 0.8f, 1.0f)); // RGBA
Vector4 color = scrollBar.GetBarColor();
```

### 모서리 반경

스크롤 바에 둥근 모서리를 적용합니다. 모든 모서리에 동일한 반경을 설정하거나 개별 값을 지정할 수 있습니다:

```cpp
// 모든 모서리에 동일한 반경
scrollBar.SetBarCornerRadius(4.0f);

// 각 모서리에 개별 반경 (왼쪽상단, 오른쪽상단, 오른쪽하단, 왼쪽하단)
scrollBar.SetBarCornerRadius(Vector4(2.0f, 4.0f, 4.0f, 2.0f));

Vector4 radius = scrollBar.GetBarCornerRadius();
```

### 최소 크기 및 오프셋

콘텐츠가 클 때도 스크롤 바가 보이도록 최소 크기를 설정합니다:

```cpp
scrollBar.SetBarMinSize(20.0f);
float minSize = scrollBar.GetBarMinSize();
```

## 가시성 제어

스크롤 바의 표시 동작을 제어합니다:

```cpp
// 스크롤 바 표시
scrollBar.SetVisible(true);

// 스크롤 시에만 표시
scrollBar.SetShowOnScroll(true);

// 페이드 효과 설정
scrollBar.SetFadeDuration(500); // 500ms
```

## 스크롤 위치 관리

스크롤 위치를 설정하고 조회합니다:

```cpp
// 현재 스크롤 위치 설정
scrollBar.SetScrollPosition(0.5f); // 50% 위치

// 현재 위치 가져오기
float position = scrollBar.GetScrollPosition();

// 스크롤 범위 설정
scrollBar.SetScrollRange(0.0f, 100.0f);
```

## ScrollView와 함께 사용

ScrollView는 내장 스크롤 바를 제공하지만, 커스텀 스크롤 바를 연결할 수 있습니다:

```cpp
ScrollView scrollView = ScrollView::New();
ScrollBar scrollBar = ScrollBar::New();

// 스크롤 바를 ScrollView에 연결
scrollView.SetVerticalScrollBar(scrollBar);
```
