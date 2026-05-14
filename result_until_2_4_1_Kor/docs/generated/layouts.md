---
title: 레이아웃
sidebar_label: 레이아웃
category: layout-sizing
---

# 레이아웃 {#layouts}

레이아웃은 형식이 지정된 dali-ui 레이아웃 객체와 자식별 `LayoutParams`를 사용해 `Dali::Ui::View` 자식을 배치합니다.

## 목차 {#table-of-contents}

- [`Dali::Ui::View`로 레이아웃 트리 구성](#build-layout-trees-with-daliuiview)
- [`Dali::Ui::AbsoluteLayout`을 사용한 절대 배치](#absolute-placement-with-daliuiabsolutelayout)
- [`Dali::Ui::FlexLayout`을 사용한 유연한 행과 열](#flexible-rows-and-columns-with-daliuiflexlayout)
- [`Dali::Ui::GridLayout`을 사용한 행 및 열 크기 지정](#row-and-column-sizing-with-daliuigridlayout)

## `Dali::Ui::View`로 레이아웃 트리 구성 {#build-layout-trees-with-daliuiview}

`Dali::Ui::Layout`은 레이아웃 관리자를 소유하는 `Dali::Ui::View` 특수화입니다. 애플리케이션 코드는 보통 `Dali::Ui::AbsoluteLayout`, `Dali::Ui::FlexLayout`, `Dali::Ui::GridLayout` 같은 구체적인 레이아웃을 만든 뒤, 각 자식 `Dali::Ui::View`에 자식별 `Dali::Ui::LayoutParams`를 연결합니다.

매개변수를 연결하려면 `Dali::Ui::View::SetLayoutParams()`를 사용합니다. 부모 레이아웃에 맞는 매개변수 타입을 사용해야 합니다. `Dali::Ui::AbsoluteLayout`에는 `Dali::Ui::AbsoluteLayoutParams`, `Dali::Ui::FlexLayout`에는 `Dali::Ui::FlexLayoutParams`, `Dali::Ui::GridLayout`에는 `Dali::Ui::GridLayoutParams`를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

FlexLayout row = FlexLayout::New()
  .SetDirection(FlexDirection::ROW)
  .SetJustifyContent(FlexJustify::CENTER)
  .SetAlignItems(FlexAlign::CENTER);

View primary = View::New();
primary.SetLayoutParams(
  FlexLayoutParams::New()
    .SetFlexGrow(1.0f)
    .SetFlexShrink(1.0f));

View secondary = View::New();
secondary.SetLayoutParams(
  FlexLayoutParams::New()
    .SetFlexBasis(120.0f)
    .SetFlexShrink(0.0f));

row.Children({primary, secondary});
```

일반 핸들을 받는 공유 코드에서는 `Dali::Ui::LayoutParams::DownCast()`로 레이아웃 매개변수 핸들인지 확인한 다음, `Dali::Ui::FlexLayoutParams::DownCast()` 같은 더 구체적인 다운캐스트를 사용할 수 있습니다.

```cpp
void InspectFlexParams(BaseHandle handle)
{
  LayoutParams params = LayoutParams::DownCast(handle);
  FlexLayoutParams flexParams = FlexLayoutParams::DownCast(params);

  if(flexParams)
  {
    const float grow = flexParams.GetFlexGrow();
    const float basis = flexParams.GetFlexBasis();
    (void)grow;
    (void)basis;
  }
}
```

## `Dali::Ui::AbsoluteLayout`을 사용한 절대 배치 {#absolute-placement-with-daliuiabsolutelayout}

`Dali::Ui::AbsoluteLayout`은 명시적인 경계값을 기준으로 자식을 배치합니다. 자식에 완전한 사각형 영역이 있으면 `Dali::Ui::AbsoluteLayoutParams::SetBounds()`를 사용하고, 개별 축만 갱신하려면 `SetX()`, `SetY()`, `SetWidth()`, `SetHeight()`를 조합해 사용합니다.

`Dali::Ui::AbsoluteLayoutParams`에서 너비나 높이가 `-1`이면 해당 축은 자식 뷰 자체의 크기를 사용합니다. 음수가 아닌 경계 크기는 해당 축의 명시적 크기를 설정합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

AbsoluteLayout canvas = AbsoluteLayout::New();

View panel = View::New();
panel.SetLayoutParams(
  AbsoluteLayoutParams::New()
    .SetBounds(LayoutRect(24.0f, 32.0f, 280.0f, 160.0f)));

View badge = View::New();
badge.SetLayoutParams(
  AbsoluteLayoutParams::New()
    .SetX(256.0f)
    .SetY(16.0f)
    .SetWidth(48.0f)
    .SetHeight(48.0f));

canvas.Children({panel, badge});
```

위치나 크기를 부모 콘텐츠 영역에 대한 비율로 해석해야 할 때는 `Dali::Ui::AbsoluteLayoutFlags`를 사용합니다. `Dali::Ui::AbsoluteLayoutFlags::POSITION_PROPORTIONAL`은 `x`와 `y`를 비율값으로 만들고, `Dali::Ui::AbsoluteLayoutFlags::SIZE_PROPORTIONAL`은 `width`와 `height`를 비율값으로 만듭니다.

```cpp
AbsoluteLayout overlay = AbsoluteLayout::New();

View centeredRegion = View::New();
centeredRegion.SetLayoutParams(
  AbsoluteLayoutParams::New()
    .SetBounds(LayoutRect(0.25f, 0.25f, 0.5f, 0.5f))
    .SetFlags(AbsoluteLayoutFlags::POSITION_PROPORTIONAL |
              AbsoluteLayoutFlags::SIZE_PROPORTIONAL));

overlay.Children({centeredRegion});
```

`Dali::Ui::AbsoluteLayoutParams::New(const AbsoluteLayoutParams&)`로 기존 `Dali::Ui::AbsoluteLayoutParams` 객체를 복사한 뒤, 달라지는 필드만 조정할 수 있습니다.

```cpp
AbsoluteLayoutParams baseParams = AbsoluteLayoutParams::New()
  .SetWidth(96.0f)
  .SetHeight(96.0f);

View first = View::New();
first.SetLayoutParams(
  AbsoluteLayoutParams::New(baseParams)
    .SetX(16.0f)
    .SetY(16.0f));

View second = View::New();
second.SetLayoutParams(
  AbsoluteLayoutParams::New(baseParams)
    .SetX(128.0f)
    .SetY(16.0f));
```

## `Dali::Ui::FlexLayout`을 사용한 유연한 행과 열 {#flexible-rows-and-columns-with-daliuiflexlayout}

`Dali::Ui::FlexLayout`은 flexbox 스타일의 행 또는 열 규칙으로 자식을 배치합니다. 컨테이너는 `SetDirection()`, `SetWrap()`, `SetJustifyContent()`, `SetAlignItems()`, `SetAlignContent()`로 구성합니다.

자식에는 `Dali::Ui::FlexLayoutParams`를 사용해 유연한 크기 지정을 제어합니다. `SetFlexBasis()`는 주 축의 초기 크기를 설정하고, `SetFlexGrow()`는 남은 공간을 분배하며, `SetFlexShrink()`는 축소 동작을 제어합니다. `SetAlignSelf()`는 한 자식에 대해 부모의 교차 축 정렬을 재정의합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

FlexLayout toolbar = FlexLayout::New()
  .SetDirection(FlexDirection::ROW)
  .SetWrap(FlexWrap::NO_WRAP)
  .SetJustifyContent(FlexJustify::SPACE_BETWEEN)
  .SetAlignItems(FlexAlign::CENTER);

View title = View::New();
title.SetLayoutParams(
  FlexLayoutParams::New()
    .SetFlexGrow(1.0f)
    .SetFlexShrink(1.0f)
    .SetFlexBasis(160.0f));

View action = View::New();
action.SetLayoutParams(
  FlexLayoutParams::New()
    .SetFlexGrow(0.0f)
    .SetFlexShrink(0.0f)
    .SetFlexBasis(96.0f)
    .SetAlignSelf(FlexAlign::CENTER));

toolbar.Children({title, action});
```

줄바꿈되는 콘텐츠에서는 `Dali::Ui::FlexLayout::SetAlignContent()`가 교차 축에서 flex 라인을 정렬하는 방식을 제어합니다. 이는 각 라인 안의 항목을 정렬하는 `Dali::Ui::FlexLayout::SetAlignItems()`와는 별개입니다.

```cpp
FlexLayout chips = FlexLayout::New()
  .SetDirection(FlexDirection::ROW)
  .SetWrap(FlexWrap::WRAP)
  .SetJustifyContent(FlexJustify::FLEX_START)
  .SetAlignItems(FlexAlign::CENTER)
  .SetAlignContent(FlexAlign::FLEX_START);

View chipA = View::New();
chipA.SetLayoutParams(FlexLayoutParams::New().SetFlexBasis(96.0f));

View chipB = View::New();
chipB.SetLayoutParams(FlexLayoutParams::New().SetFlexBasis(128.0f));

View chipC = View::New();
chipC.SetLayoutParams(FlexLayoutParams::New().SetFlexBasis(112.0f));

chips.Children({chipA, chipB, chipC});
```

getter 메서드는 setter 메서드와 대응되므로, 컴포넌트가 레이아웃 정책을 보존하거나 복사해야 할 때 유용합니다.

```cpp
FlexLayout source = FlexLayout::New()
  .SetDirection(FlexDirection::COLUMN)
  .SetWrap(FlexWrap::NO_WRAP)
  .SetJustifyContent(FlexJustify::CENTER)
  .SetAlignItems(FlexAlign::STRETCH);

FlexLayout copy = FlexLayout::New()
  .SetDirection(source.GetDirection())
  .SetWrap(source.GetWrap())
  .SetJustifyContent(source.GetJustifyContent())
  .SetAlignItems(source.GetAlignItems());
```

## `Dali::Ui::GridLayout`을 사용한 행 및 열 크기 지정 {#row-and-column-sizing-with-daliuigridlayout}

`Dali::Ui::GridLayout`은 자식을 행과 열에 배치합니다. `Dali::Ui::GridLayout::AddRowDefinition()`과 `Dali::Ui::GridLayout::AddColumnDefinition()`으로 그리드를 정의합니다. 각 정의는 `Dali::Ui::GridLength`를 사용합니다. 고정 픽셀에는 `GridLength::Absolute()`, 남은 공간의 비례 배분에는 `GridLength::Star()`, 콘텐츠 기반 크기 지정에는 `GridLength::Auto()`를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

GridLayout form = GridLayout::New()
  .SetRowSpacing(12.0f)
  .SetColumnSpacing(16.0f);

form.AddColumnDefinition(GridLength::Absolute(120.0f));
form.AddColumnDefinition(GridLength::Star(1.0f));
form.AddRowDefinition(GridLength::Auto());
form.AddRowDefinition(GridLength::Auto());

View label = View::New();
label.SetLayoutParams(
  GridLayoutParams::New()
    .SetRow(0u)
    .SetColumn(0u));

View field = View::New();
field.SetLayoutParams(
  GridLayoutParams::New()
    .SetRow(0u)
    .SetColumn(1u)
    .SetHorizontalAlignment(LayoutAlignment::FILL)
    .SetVerticalAlignment(LayoutAlignment::CENTER));

form.Children({label, field});
```

하나의 뷰가 여러 셀을 차지해야 할 때는 span을 사용합니다. `Dali::Ui::GridLayoutParams::SetRowSpan()`과 `Dali::Ui::GridLayoutParams::SetColumnSpan()`은 개수를 받고, `SetRow()`와 `SetColumn()`은 0부터 시작하는 시작 인덱스를 받습니다.

```cpp
GridLayout dashboard = GridLayout::New()
  .SetRowSpacing(8.0f)
  .SetColumnSpacing(8.0f);

dashboard.AddColumnDefinition(GridLength::Star(1.0f));
dashboard.AddColumnDefinition(GridLength::Star(1.0f));
dashboard.AddColumnDefinition(GridLength::Star(1.0f));
dashboard.AddRowDefinition(GridLength::Absolute(80.0f));
dashboard.AddRowDefinition(GridLength::Star(1.0f));

View header = View::New();
header.SetLayoutParams(
  GridLayoutParams::New()
    .SetRow(0u)
    .SetColumn(0u)
    .SetColumnSpan(3u)
    .SetHorizontalAlignment(LayoutAlignment::FILL)
    .SetVerticalAlignment(LayoutAlignment::FILL));

View content = View::New();
content.SetLayoutParams(
  GridLayoutParams::New()
    .SetRow(1u)
    .SetColumn(0u)
    .SetColumnSpan(2u));

dashboard.Children({header, content});
```

그리드를 다시 구성해야 할 때는 새 행 또는 열 모델을 추가하기 전에 기존 정의를 지웁니다. `GetRowCount()`, `GetColumnCount()`, `GetRowSpacing()`, `GetColumnSpacing()`로 현재 레이아웃 구성을 확인할 수 있습니다.

```cpp
void ConfigureCompactGrid(GridLayout grid)
{
  grid.ClearRowDefinitions();
  grid.ClearColumnDefinitions();

  grid.AddColumnDefinition(GridLength::Star(1.0f));
  grid.AddRowDefinition(GridLength::Auto());
  grid.AddRowDefinition(GridLength::Auto());

  const uint32_t rows = grid.GetRowCount();
  const uint32_t columns = grid.GetColumnCount();
  const float rowGap = grid.GetRowSpacing();
  const float columnGap = grid.GetColumnSpacing();

  (void)rows;
  (void)columns;
  (void)rowGap;
  (void)columnGap;
}
```
