---
title: I Scroll Bar
sidebar_label: I Scroll Bar
category: views-components
---

# I Scroll Bar

`IScrollBar`는 dali-ui 애플리케이션에서 대상 뷰의 스크롤 표시 및 제어를 담당하는 스크롤 바 컴포넌트의 계약을 정의하는 인터페이스입니다.

## 목차

- [스크롤 바 생성](#스크롤-바-생성)
- [스크롤 바 가시성 구성](#스크롤-바-가시성-구성)
- [스크롤 바 상태 업데이트](#스크롤-바-상태-업데이트)
- [스크롤 바 숨기기](#스크롤-바-숨기기)

## 스크롤 바 생성

`ScrollBar` 클래스가 `IScrollBar` 인터페이스를 구현합니다. 정적 `ScrollBar::New()` 메서드로 스크롤 바를 생성합니다. `ScrollBar`는 `AbsoluteLayout`을 상속하므로 다른 레이아웃처럼 뷰 계층에 추가할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>

// 스크롤 바 생성
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

// 부모 뷰에 추가
parentView.Add(scrollBar);
```

`ScrollBar::DownCast()`를 사용하여 핸들을 `ScrollBar`로 다운캐스트할 수도 있습니다:

```cpp
Dali::BaseHandle handle = scrollBar;
Dali::Ui::ScrollBar scrollBar2 = Dali::Ui::ScrollBar::DownCast(handle);
if (scrollBar2) {
  // 다운캐스트 성공
}
```

## 스크롤 바 가시성 구성

`IScrollBar` 인터페이스는 수직 및 수평 스크롤 바의 가시성을 독립적으로 제어하는 메서드를 제공합니다. `ScrollBarVisibility` 열거형을 사용하여 원하는 동작을 설정합니다.

### 가시성 모드

`ScrollBarVisibility` 열거형은 세 가지 가시성 모드를 정의합니다:

- `ScrollBarVisibility::Auto` - 스크롤 중일 때만 스크롤 바 표시
- `ScrollBarVisibility::Always` - 항상 스크롤 바 표시
- `ScrollBarVisibility::Never` - 스크롤 바 표시 안 함

### 가시성 설정

```cpp
#include <dali-ui-foundation/public-api/scroll-bar.h>
#include <dali-ui-foundation/public-api/scrollable-enum.h>

Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

// 수직 스크롤 바를 항상 표시로 설정
scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Always);

// 수평 스크롤 바를 자동으로 설정 (스크롤 중에만 표시)
scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
```

### 현재 가시성 가져오기

```cpp
// 현재 가시성 설정 가져오기
Dali::Ui::ScrollBarVisibility verticalVisibility = scrollBar.GetVerticalScrollBarVisibility();
Dali::Ui::ScrollBarVisibility horizontalVisibility = scrollBar.GetHorizontalScrollBarVisibility();
```

기본적으로 수직 및 수평 스크롤 바 모두 `ScrollBarVisibility::Auto`를 사용합니다.

## 스크롤 바 상태 업데이트

`IScrollBar` 인터페이스는 스크롤 가능한 콘텐츠와 현재 스크롤 위치를 기반으로 스크롤 바 크기와 위치를 업데이트하는 메서드를 제공합니다.

### 바 크기 업데이트

`UpdateBarSize()`를 호출하여 스크롤 가능한 콘텐츠 크기와 뷰포트 크기를 기반으로 스크롤 바 썸 크기를 다시 계산합니다. 썸 크기는 전체 스크롤 영역 내에서 가시 콘텐츠의 비율을 나타냅니다.

```cpp
// 스크롤 가능한 콘텐츠와 뷰포트 크기 정의
float scrollWidth = 1000.0f;  // 전체 스크롤 가능한 콘텐츠 너비
float scrollHeight = 2000.0f; // 전체 스크롤 가능한 콘텐츠 높이
float viewportWidth = 400.0f;  // 가시 뷰포트 너비
float viewportHeight = 600.0f; // 가시 뷰포트 높이

// 현재 콘텐츠-뷰포트 비율을 반영하도록 스크롤 바 크기 업데이트
scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
```

### 스크롤 위치 업데이트

`UpdateScrollPosition()`을 호출하여 콘텐츠의 현재 스크롤 위치를 반영하도록 스크롤 바 썸을 이동합니다.

```cpp
// 현재 스크롤 오프셋을 기반으로 스크롤 바 위치 업데이트
Dali::Vector2 scrollPosition(150.0f, 300.0f);
scrollBar.UpdateScrollPosition(scrollPosition);
```

### 대상 뷰 가져오기

`GetTarget()`을 사용하여 이 스크롤 바가 제어하는 뷰를 조회합니다:

```cpp
Dali::Ui::View targetView = scrollBar.GetTarget();
if (targetView) {
  // 스크롤 바에 연결된 대상 뷰가 있음
}
```

## 스크롤 바 숨기기

`HideBar()`를 호출하여 스크롤 바의 페이드 아웃 애니메이션을 트리거합니다. 일반적으로 스크롤이 끝날 때 호출합니다. 숨기기 동작은 `ScrollBarVisibility::Auto` 가시성 모드의 스크롤 바에만 영향을 줍니다.

```cpp
// 스크롤 바 숨기기 (페이드 아웃 애니메이션)
scrollBar.HideBar();
```

이 메서드는 사용자가 스크롤을 멈춘 후 스크롤 바를 숨기려는 커스텀 스크롤 컨테이너를 구현할 때 유용합니다.
