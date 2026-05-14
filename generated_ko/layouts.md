---
title: 레이아웃
sidebar_label: 레이아웃
category: layout-sizing
---

# 레이아웃

레이아웃은 자동 크기 조정 및 위치 지정 알고리즘을 사용하여 자식 뷰를 배치합니다. dali-ui는 네 가지 레이아웃 컨테이너를 제공합니다: `AbsoluteLayout`, `StackLayout`, `FlexLayout`, `GridLayout`.

## 목차

- [레이아웃 기본](#레이아웃-기본)
- [AbsoluteLayout](#absolutelayout)
- [StackLayout](#stacklayout)
- [FlexLayout](#flexlayout)
- [GridLayout](#gridlayout)

## 레이아웃 기본

모든 레이아웃 클래스는 `Dali::Ui::Layout`을 상속하며, 이는 `Dali::Ui::View`를 확장합니다. 레이아웃 자체가 뷰이므로 다른 레이아웃 내에 레이아웃을 중첩하여 복잡한 UI 계층을 구축할 수 있습니다.

각 레이아웃 타입은 해당 레이아웃 매개변수 클래스를 사용하여 개별 자식의 크기와 위치를 제어합니다:

| 레이아웃 | 매개변수 클래스 |
|--------|-----------------|
| `AbsoluteLayout` | `AbsoluteLayoutParams` |
| `StackLayout` | `StackLayoutParams` |
| `FlexLayout` | `FlexLayoutParams` |
| `GridLayout` | `GridLayoutParams` |

`WRAP_CONTENT`를 사용하여 뷰를 자연스러운 콘텐츠 크기로 조정하고, `MATCH_PARENT`를 사용하여 부모의 사용 가능한 공간을 채웁니다.

```cpp
using namespace Dali::Ui;

// 부모를 채우는 레이아웃 생성
StackLayout root = StackLayout::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT);
```

## AbsoluteLayout

`AbsoluteLayout`은 명시적인 픽셀 좌표에 자식을 배치합니다. `AbsoluteLayoutParams`를 사용하여 각 자식의 위치와 크기를 설정합니다.

### 자식 위치 지정

`SetX()`와 `SetY()`로 좌표를 설정하고, `SetWidth()`와 `SetHeight()`로 크기를 설정합니다:

```cpp
AbsoluteLayout root = AbsoluteLayout::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT);

View redBox = View::New();
redBox.SetBackgroundColor(Color::RED);
redBox.SetLayoutParams(AbsoluteLayoutParams::New()
  .SetWidth(100.0f).SetHeight(100.0f));
root.Add(redBox);

View greenBox = View::New();
greenBox.SetBackgroundColor(Color::GREEN);
greenBox.SetLayoutParams(AbsoluteLayoutParams::New()
  .SetX(100.0f).SetY(50.0f).SetWidth(100.0f).SetHeight(50.0f));
root.Add(greenBox);
```

### 비례 크기 조정

`AbsoluteLayoutFlags`를 사용하여 부모에 비례하여 자식을 위치나 크기를 조정합니다. 0.0~1.0 사이의 값은 부모 콘텐츠 영역의 비율을 나타냅니다.

```cpp
View proportionalChild = View::New();
proportionalChild.SetBackgroundColor(Color::BLUE);
proportionalChild.SetLayoutParams(AbsoluteLayoutParams::New()
  .SetBounds(LayoutRect(0.0f, 0.0f, 1.0f, 1.0f))
  .SetFlags(AbsoluteLayoutFlags::ALL));
root.Add(proportionalChild);
```

`AbsoluteLayoutFlags::ALL` 플래그는 모든 좌표를 비례적으로 만듭니다: 자식이 부모의 전체 콘텐츠 영역을 채웁니다.

### 경계 직접 설정

`SetBounds()`와 `LayoutRect`를 사용하여 단일 호출로 위치와 크기를 설정합니다:

```cpp
View box = View::New();
box.SetLayoutParams(AbsoluteLayoutParams::New()
  .SetBounds(LayoutRect(10.0f, 20.0f, 100.0f, 200.0f)));
```

## StackLayout

`StackLayout`은 자식을 단일 행 또는 열로 배치합니다. 위에서 아래로 쌓으려면 `StackOrientation::VERTICAL`을, 왼쪽에서 오른쪽으로 쌓으려면 `StackOrientation::HORIZONTAL`을 사용합니다.

### 스택 생성

```cpp
StackLayout stack = StackLayout::New(StackOrientation::VERTICAL)
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT)
  .SetSpacing(10.0f);
```

### 자식 가중치

`StackLayoutParams::SetWeight()`를 사용하여 남은 공간을 자식 간에 분배합니다. 가중치가 0보다 큰 자식들이 사용 가능한 주축 공간을 비례적으로 나눕니다.

```cpp
StackLayout stack = StackLayout::New(StackOrientation::VERTICAL)
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT);

View header = View::New();
header.SetRequestedHeight(50.0f);
stack.Add(header);

View content = View::New();
content.SetLayoutParams(StackLayoutParams::New()
  .SetWeight(1.0f).SetAlignment(LayoutAlignment::FILL));
stack.Add(content);
```

### 교차축 정렬

`StackLayoutParams`의 `SetAlignment()`를 사용하여 교차축 내에서 자식을 배치합니다:

```cpp
View centered = View::New();
centered.SetRequestedWidth(100.0f);
centered.SetLayoutParams(StackLayoutParams::New()
  .SetAlignment(LayoutAlignment::CENTER));
stack.Add(centered);
```

## FlexLayout

`FlexLayout`은 CSS Flexbox 알고리즘을 구현하여 래핑 및 정렬이 있는 유연한 행 및 열 레이아웃을 제공합니다.

### 방향 및 래핑

`SetDirection()`으로 주축 방향을 설정하고 `SetWrap()`으로 래핑을 활성화합니다:

```cpp
FlexLayout flex = FlexLayout::New()
  .SetDirection(FlexDirection::ROW)
  .SetWrap(FlexWrap::WRAP)
  .SetJustifyContent(FlexJustify::FLEX_START)
  .SetAlignItems(FlexAlign::CENTER);
```

사용 가능한 방향:
- `FlexDirection::ROW` — 왼쪽에서 오른쪽
- `FlexDirection::ROW_REVERSE` — 오른쪽에서 왼쪽
- `FlexDirection::COLUMN` — 위에서 아래
- `FlexDirection::COLUMN_REVERSE` — 아래에서 위

### Flex 성장 및 축소

`FlexLayoutParams`를 사용하여 자식의 성장 및 축소를 제어합니다:

```cpp
View box1 = View::New();
box1.SetRequestedHeight(100.0f);
box1.SetLayoutParams(FlexLayoutParams::New()
  .SetFlexBasis(50.0f).SetFlexGrow(1.0f));
flex.Add(box1);

View box2 = View::New();
box2.SetRequestedHeight(100.0f);
box2.SetLayoutParams(FlexLayoutParams::New()
  .SetFlexBasis(100.0f).SetFlexGrow(2.0f));
flex.Add(box2);
```

- `SetFlexBasis()`는 성장/축소 전 초기 주 크기를 설정합니다.
- `SetFlexGrow()`는 다른 항목에 비해 얼마나 성장해야 하는지 정의합니다.
- `SetFlexShrink()`는 공간이 부족할 때 얼마나 축소해야 하는지 정의합니다.

### 정렬

`SetJustifyContent()`로 주축을 따라 정렬하고 `SetAlignItems()`로 교차축을 따라 정렬을 제어합니다:

```cpp
FlexLayout flex = FlexLayout::New()
  .SetJustifyContent(FlexJustify::CENTER)
  .SetAlignItems(FlexAlign::STRETCH);
```

래핑된 레이아웃의 경우 `SetAlignContent()`로 여러 줄이 정렬되는 방식을 제어합니다:

```cpp
FlexLayout flex = FlexLayout::New()
  .SetWrap(FlexWrap::WRAP)
  .SetAlignContent(FlexAlign::CENTER);
```

### 개별 정렬

`SetAlignSelf()`를 사용하여 개별 자식의 교차축 정렬을 재정의합니다:

```cpp
View centered = View::New();
centered.SetLayoutParams(FlexLayoutParams::New()
  .SetAlignSelf(FlexAlign::CENTER));
flex.Add(centered);
```

## GridLayout

`GridLayout`은 행과 열의 그리드로 자식을 배치합니다. `GridLength`를 사용하여 행과 열 크기를 정의합니다.

### 행과 열 정의

`AddRowDefinition()`과 `AddColumnDefinition()`으로 행과 열 정의를 추가합니다:

```cpp
GridLayout grid = GridLayout::New()
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT)
  .SetRowSpacing(10.0f)
  .SetColumnSpacing(10.0f);

grid.AddRowDefinition(GridLength::Absolute(50.0f));
grid.AddRowDefinition(GridLength::Absolute(100.0f));
grid.AddRowDefinition(GridLength::Absolute(200.0f));
grid.AddColumnDefinition(GridLength::Absolute(50.0f));
grid.AddColumnDefinition(GridLength::Absolute(100.0f));
```

### 그리드 길이 타입

`GridLength`는 세 가지 크기 조정 모드를 지원합니다:

- `GridLength::Absolute(pixels)` — 픽셀 단위 고정 크기
- `GridLength::Star(factor)` — 비례 크기 조정, 남은 공간 분배
- `GridLength::Auto()` — 콘텐츠에 맞게 크기 조정

```cpp
// 절대, 별, 자동 크기 조정 혼합
grid.AddRowDefinition(GridLength::Auto());       // 자동 크기 행
grid.AddRowDefinition(GridLength::Star(1.0f));   // 남은 공간 차지
grid.AddRowDefinition(GridLength::Auto());       // 자동 크기 행
```

### 일괄 정의

`SetRowDefinitions()`와 `SetColumnDefinitions()`로 모든 행 또는 열 정의를 한 번에 설정합니다:

```cpp
Dali::Vector<GridLength> rowDefinition;
rowDefinition.Reserve(3);
rowDefinition.PushBack(GridLength::Auto());
rowDefinition.PushBack(GridLength::Star(1.0f));
rowDefinition.PushBack(GridLength::Auto());
grid.SetRowDefinitions(rowDefinition);
```

### 자식 위치 지정

`GridLayoutParams`를 사용하여 각 자식의 행과 열을 지정합니다:

```cpp
View cell00 = View::New();
cell00.SetBackgroundColor(Color::RED);
cell00.SetLayoutParams(GridLayoutParams::New());
grid.Add(cell00);

View cell01 = View::New();
cell01.SetBackgroundColor(Color::GREEN);
cell01.SetLayoutParams(GridLayoutParams::New().SetColumn(1));
grid.Add(cell01);

View cell10 = View::New();
cell10.SetBackgroundColor(Color::BLUE);
cell10.SetLayoutParams(GridLayoutParams::New().SetRow(1));
grid.Add(cell10);

View cell11 = View::New();
cell11.SetBackgroundColor(Color::YELLOW);
cell11.SetLayoutParams(GridLayoutParams::New().SetRow(1).SetColumn(1));
grid.Add(cell11);
```

자식은 기본적으로 첫 번째 사용 가능한 셀에 배치됩니다. 명시적 위치를 지정하려면 `SetRow()`와 `SetColumn()`을 사용합니다.
