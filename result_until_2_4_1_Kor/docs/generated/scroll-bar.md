---
title: 스크롤 바
sidebar_label: 스크롤 바
category: views-components
---

# 스크롤 바 {#scroll-bar}

`Dali::Ui::ScrollBar`는 dali-ui 뷰 기반 스크롤 영역의 스크롤 위치와 스크롤 가능한 범위를 표시합니다.

## 목차 {#table-of-contents}

- [스크롤 바 생성 및 구성](#create-and-configure-a-scroll-bar)
- [바 표시 여부 제어](#control-bar-visibility)
- [크기와 스크롤 위치 동기화](#synchronize-size-and-scroll-position)
- [바 스타일 지정](#style-the-bar)
- [상태 읽기 및 핸들 재사용](#read-state-and-reuse-handles)

## 스크롤 바 생성 및 구성 {#create-and-configure-a-scroll-bar}

`Dali::Ui::ScrollBar::New`를 사용하여 초기화된 `Dali::Ui::ScrollBar`를 생성합니다. 스크롤 바는 dali-ui 뷰 타입이므로, 애플리케이션 코드는 원시 액터 프로퍼티 호출보다 타입이 지정된 setter와 `Dali::Ui::View` 핸들을 사용하는 것이 좋습니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);

scrollBar.SetBarThickness(8.0f);
scrollBar.SetBarMinSize(24.0f);
scrollBar.SetBarOffset(2.0f);
scrollBar.SetBarColor(Dali::Vector4(0.15f, 0.15f, 0.15f, 0.85f));
scrollBar.SetBarCornerRadius(4.0f);
```

`Dali::Ui::ScrollBar::GetTarget`은 스크롤 바를 애플리케이션에서 사용할 수 있는 `Dali::Ui::View`로 반환합니다. 주변 dali-ui 코드가 뷰 핸들을 요구할 때 유용합니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();
Dali::Ui::View scrollBarView = scrollBar.GetTarget();
```

## 바 표시 여부 제어 {#control-bar-visibility}

스크롤 바는 수직 및 수평 표시 여부를 독립적으로 설정할 수 있습니다. `Dali::Ui::ScrollBar::SetVerticalScrollBarVisibility`와 `Dali::Ui::ScrollBar::SetHorizontalScrollBarVisibility`를 `Dali::Ui::ScrollBarVisibility`와 함께 사용합니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);

Dali::Ui::ScrollBarVisibility vertical = scrollBar.GetVerticalScrollBarVisibility();
Dali::Ui::ScrollBarVisibility horizontal = scrollBar.GetHorizontalScrollBarVisibility();
```

`Dali::Ui::ScrollBarVisibility::Auto`는 스크롤 중일 때만 바를 표시하고, `Dali::Ui::ScrollBarVisibility::Always`는 항상 표시하며, `Dali::Ui::ScrollBarVisibility::Never`는 해당 축의 바를 화면에서 제거합니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Always);
scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
```

자동으로 표시되는 바를 스크롤이 끝났을 때 숨기려면 `Dali::Ui::ScrollBar::HideBar`를 호출합니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
scrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, 120.0f));
scrollBar.HideBar();
```

## 크기와 스크롤 위치 동기화 {#synchronize-size-and-scroll-position}

스크롤 가능한 콘텐츠 크기나 뷰포트 크기가 변경되면 `Dali::Ui::ScrollBar::UpdateBarSize`를 호출합니다. 인자는 스크롤 가능한 너비, 스크롤 가능한 높이, 뷰포트 너비, 뷰포트 높이 순서입니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

const float scrollWidth = 640.0f;
const float scrollHeight = 1800.0f;
const float viewportWidth = 640.0f;
const float viewportHeight = 480.0f;

scrollBar.UpdateBarSize(scrollWidth, scrollHeight, viewportWidth, viewportHeight);
```

연결된 스크롤 컴포넌트가 새 위치를 보고하면 `Dali::Ui::ScrollBar::UpdateScrollPosition`을 사용합니다. 애플리케이션 코드가 스크롤 바 위치를 직접 설정할 때는 `Dali::Ui::ScrollBar::SetScrollPosition`을 사용합니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.UpdateBarSize(640.0f, 1800.0f, 640.0f, 480.0f);
scrollBar.UpdateScrollPosition(Dali::Vector2(0.0f, 320.0f));

Dali::Vector2 currentPosition = scrollBar.GetScrollPosition();
```

상태를 직접 복원하려면 바 크기를 설정한 뒤 스크롤 위치를 설정합니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.UpdateBarSize(1024.0f, 2400.0f, 1024.0f, 600.0f);
scrollBar.SetScrollPosition(Dali::Vector2(0.0f, 600.0f));
```

## 바 스타일 지정 {#style-the-bar}

`Dali::Ui::ScrollBar`는 일반적인 시각적 세부 설정을 위한 타입 지정 setter를 제공합니다. 바의 교차 축 두께에는 `Dali::Ui::ScrollBar::SetBarThickness`, 색상에는 `Dali::Ui::ScrollBar::SetBarColor`, 최소 thumb 크기에는 `Dali::Ui::ScrollBar::SetBarMinSize`, 가장자리 간격에는 `Dali::Ui::ScrollBar::SetBarOffset`을 사용합니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetBarThickness(10.0f);
scrollBar.SetBarMinSize(32.0f);
scrollBar.SetBarOffset(4.0f);
scrollBar.SetBarColor(Dali::Vector4(0.0f, 0.45f, 0.85f, 1.0f));
```

하나의 값으로 모든 모서리를 둥글게 하려면 `Dali::Ui::ScrollBar::SetBarCornerRadius`를 사용하고, 각 모서리를 개별적으로 설정하려면 `Dali::Vector4`를 전달합니다. `Dali::Ui::ScrollBar::GetBarCornerRadius`는 왼쪽 위, 오른쪽 위, 오른쪽 아래, 왼쪽 아래 반지름 값을 담은 `Dali::Vector4`를 반환합니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetBarCornerRadius(6.0f);

Dali::Vector4 radius = scrollBar.GetBarCornerRadius();
```

디자인 설정 패널을 동기화하거나 스크롤 바 구성을 보존해야 할 때는 대응하는 getter로 값을 다시 읽습니다.

```cpp
Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

scrollBar.SetBarThickness(12.0f);
scrollBar.SetBarOffset(3.0f);
scrollBar.SetBarMinSize(28.0f);

float thickness = scrollBar.GetBarThickness();
float offset = scrollBar.GetBarOffset();
float minSize = scrollBar.GetBarMinSize();
Dali::Vector4 color = scrollBar.GetBarColor();
```

## 상태 읽기 및 핸들 재사용 {#read-state-and-reuse-handles}

기본 생성된 `Dali::Ui::ScrollBar`는 초기화되지 않은 핸들입니다. 멤버 함수를 사용하기 전에 `Dali::Ui::ScrollBar::New`로 생성한 초기화된 핸들을 할당합니다.

```cpp
Dali::Ui::ScrollBar scrollBar;

scrollBar = Dali::Ui::ScrollBar::New();
scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
```

`Dali::Ui::ScrollBar`는 일반적인 핸들 할당, 복사 생성, 이동 생성, 이동 할당을 지원하므로, 헬퍼 함수에서 구성된 핸들을 반환할 수 있습니다.

```cpp
Dali::Ui::ScrollBar CreateConfiguredScrollBar()
{
  Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::New();

  scrollBar.SetBarThickness(8.0f);
  scrollBar.SetBarColor(Dali::Vector4(0.2f, 0.2f, 0.2f, 1.0f));
  scrollBar.SetVerticalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Auto);
  scrollBar.SetHorizontalScrollBarVisibility(Dali::Ui::ScrollBarVisibility::Never);

  return scrollBar;
}

Dali::Ui::ScrollBar activeScrollBar;
activeScrollBar = CreateConfiguredScrollBar();
```

프레임워크 코드에서 일반 핸들을 전달받은 경우, 스크롤 바 전용 API를 호출하기 전에 `Dali::Ui::ScrollBar::DownCast`를 사용하여 `Dali::Ui::ScrollBar` 핸들을 복원합니다. 원본 핸들에 스크롤 바가 들어 있지 않을 수 있다면 사용하기 전에 결과를 확인합니다.

```cpp
Dali::Ui::ScrollBar original = Dali::Ui::ScrollBar::New();
Dali::BaseHandle handle(original);

Dali::Ui::ScrollBar scrollBar = Dali::Ui::ScrollBar::DownCast(handle);
scrollBar.SetScrollPosition(Dali::Vector2(0.0f, 160.0f));
```
