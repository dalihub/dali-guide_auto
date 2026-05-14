---
title: View(기본 UI 객체)
sidebar_label: View(기본 UI 객체)
category: views-components
---

# View(기본 UI 객체) {#view-base-ui-object}

`Dali::Ui::View`는 dali-ui 애플리케이션에서 앱이 직접 다루는 기본 UI 객체입니다.

## 목차 {#table-of-contents}

- [View 핸들 생성 및 유지](#create-and-keep-view-handles)
- [View 트리 구성](#build-view-trees)
- [레이아웃 및 시각 상태 읽기](#read-layout-and-visual-state)
- [클릭, 누름, 길게 누름 처리](#handle-click-press-and-long-press)
- [선택 가능 상태 사용](#use-selectable-state)
- [View 애니메이션 적용](#animate-a-view)

## View 핸들 생성 및 유지 {#create-and-keep-view-handles}

초기화된 범용 UI 객체가 필요할 때는 `Dali::Ui::View::New`를 사용합니다. `Dali::Ui::View` 핸들은 내부 View 객체를 참조하므로, 핸들을 복사하면 같은 View에 대한 또 다른 참조가 유지됩니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

View CreatePanel()
{
  View panel = View::New();
  return panel;
}
```

나중에 검사하거나 업데이트할 View는 `Dali::Ui::View` 핸들로 저장합니다.

```cpp
class PageController
{
public:
  void Create()
  {
    mHeader = View::New();
    mContent = View::New();
  }

  Dali::String HeaderName() const
  {
    return mHeader.GetName();
  }

private:
  View mHeader;
  View mContent;
};
```

애플리케이션 코드에서 View의 식별 정보나 색상 상태를 확인해야 할 때는 `Dali::Ui::View::GetName`, `Dali::Ui::View::GetColor`, `Dali::Ui::View::GetCurrentColor`, `Dali::Ui::View::GetBackgroundColor`가 유용합니다.

```cpp
void ReadViewIdentity(View view)
{
  Dali::String name = view.GetName();
  UiColor color = view.GetColor();
  UiColor currentColor = view.GetCurrentColor();
  UiColor background = view.GetBackgroundColor();
}
```

## View 트리 구성 {#build-view-trees}

UI 계층 구조를 다룰 때는 `Dali::Ui::View`를 앱이 직접 다루는 객체 모델로 사용합니다. 부모 View는 `Dali::Ui::View::GetChildCount`와 `Dali::Ui::View::GetChildAt`을 통해 자식 조회 기능을 제공합니다.

```cpp
void InspectChildren(View container)
{
  const uint32_t childCount = container.GetChildCount();

  for(uint32_t index = 0u; index < childCount; ++index)
  {
    View child = container.GetChildAt(index);
    Dali::String childName = child.GetName();
  }
}
```

View를 헬퍼 함수에 전달할 때는 자식 핸들을 `Dali::Ui::View` 객체로 사용합니다.

```cpp
View FindFirstChild(View container)
{
  if(container.GetChildCount() == 0u)
  {
    return View();
  }

  return container.GetChildAt(0u);
}
```

코드가 일반 `Dali::BaseHandle`을 받는 경우, 해당 핸들이 실제로 View를 참조할 때만 `Dali::Ui::View::DownCast`로 `Dali::Ui::View`를 복구할 수 있습니다.

```cpp
View ResolveView(BaseHandle handle)
{
  View view = View::DownCast(handle);
  return view;
}
```

## 레이아웃 및 시각 상태 읽기 {#read-layout-and-visual-state}

`Dali::Ui::View`는 요청된 레이아웃 입력과 측정 및 현재 결과를 구분합니다. 마지막 측정 패스의 크기는 `Dali::Ui::View::GetMeasuredSize`로, 현재 렌더링된 크기는 `Dali::Ui::View::GetCurrentSize`로 가져옵니다.

```cpp
void ReadSizeState(View view)
{
  MeasuredSize measured = view.GetMeasuredSize();
  MeasuredSize current = view.GetCurrentSize();
}
```

경계 제약 조건을 확인하려면 `Dali::Ui::View::GetMinimumWidth`, `Dali::Ui::View::GetMinimumHeight`, `Dali::Ui::View::GetMaximumWidth`, `Dali::Ui::View::GetMaximumHeight`를 읽습니다.

```cpp
bool HasWidthRange(View view)
{
  const float minimumWidth = view.GetMinimumWidth();
  const float maximumWidth = view.GetMaximumWidth();

  return minimumWidth <= maximumWidth;
}
```

배치와 스케일을 확인하려면 `Dali::Ui::View::GetCurrentPosition`, `Dali::Ui::View::GetCurrentScale`, `Dali::Ui::View::GetCurrentScaleX`, `Dali::Ui::View::GetCurrentScaleY`를 사용합니다.

```cpp
void ReadTransformState(View view)
{
  Vector2 position = view.GetCurrentPosition();
  Vector2 scale = view.GetCurrentScale();

  const float scaleX = view.GetCurrentScaleX();
  const float scaleY = view.GetCurrentScaleY();
}
```

레이아웃 참여 상태를 확인하려면 `Dali::Ui::View::GetLayoutMode`, `Dali::Ui::View::GetMargin`, `Dali::Ui::View::GetEffectiveLayoutDirection`을 검사합니다.

```cpp
void ReadLayoutState(View view)
{
  LayoutMode mode = view.GetLayoutMode();
  Extents margin = view.GetMargin();
  Dali::LayoutDirection::Type direction = view.GetEffectiveLayoutDirection();
}
```

시각적 장식 상태는 `Dali::Ui::View::GetCornerRadius`, `Dali::Ui::View::GetCornerRadiusPolicy`, `Dali::Ui::View::GetCornerSquareness`, `Dali::Ui::View::GetBorderlineWidth`, `Dali::Ui::View::GetBorderlineColor`, `Dali::Ui::View::GetBorderlineOffset`처럼 View가 소유한 API를 통해 확인할 수 있습니다.

```cpp
void ReadDecorationState(View view)
{
  Vector4 radius = view.GetCornerRadius();
  CornerRadiusPolicy radiusPolicy = view.GetCornerRadiusPolicy();
  Vector4 squareness = view.GetCornerSquareness();

  const float borderWidth = view.GetBorderlineWidth();
  UiColor borderColor = view.GetBorderlineColor();
  const float borderOffset = view.GetBorderlineOffset();
}
```

## 클릭, 누름, 길게 누름 처리 {#handle-click-press-and-long-press}

`Dali::Ui::View::AsInteractive`를 통해 `Dali::Ui::View`에 상호작용 동작을 연결한 다음, 반환되는 `Dali::Ui::InteractiveTrait`를 구성합니다. 이 Trait는 해당 View에 클릭, 누름, 길게 누름, pseudo-disabled, 키 클릭 동작을 제공합니다.

```cpp
class ButtonController
{
public:
  void Create()
  {
    mButton = View::New();

    mButton.AsInteractive([this](InteractiveTrait& trait)
    {
      trait.SetClickable(true);
      trait.ConnectClickedSignal(this, &ButtonController::OnClicked);
      trait.ConnectPressedChangedSignal(this, &ButtonController::OnPressedChanged);
      trait.ConnectLongPressedSignal(this, &ButtonController::OnLongPressed);
    });
  }

  void OnClicked(View view, InputEvent event)
  {
    mLastClicked = view;
  }

  void OnPressedChanged(View view, bool pressed, InputEvent event)
  {
    mPressed = pressed;
  }

  bool OnLongPressed(View view, InputEvent event)
  {
    return true;
  }

private:
  View mButton;
  View mLastClicked;
  bool mPressed{false};
};
```

`Dali::Ui::InteractiveTrait::ClickedSignal`은 `void(View, InputEvent)`를 내보냅니다. `Dali::Ui::InteractiveTrait::PressedChangedSignal`은 `void(View, bool, InputEvent)`를 내보냅니다. `Dali::Ui::InteractiveTrait::LongPressedSignal`은 `bool(View, InputEvent)`를 내보내며, 여기서 `true`를 반환하면 뒤따르는 클릭이 억제됩니다.

애플리케이션 상태가 현재 상호작용 상태에 따라 달라질 때는 쿼리 메서드를 사용합니다.

```cpp
void ReadInteractionState(InteractiveTrait trait)
{
  const bool clickable = trait.IsClickable();
  const bool pressed = trait.IsPressed();
  const bool pseudoDisabled = trait.IsPseudoDisabled();
  KeyClickPolicy keyClickPolicy = trait.GetKeyClickPolicy();
}
```

View가 상호작용은 계속 받되 비활성화된 것처럼 보이는 상태를 표현해야 할 때는 `Dali::Ui::InteractiveTrait::SetPseudoDisabled`를 사용합니다.

```cpp
void ConfigureGuidedDisabledState(InteractiveTrait trait)
{
  trait.SetClickable(true);
  trait.SetPseudoDisabled(true);
}
```

## 선택 가능 상태 사용 {#use-selectable-state}

`Dali::Ui::SelectableTrait`는 View의 선택 상태를 관리합니다. `Dali::Ui::View::AsSelectable`을 통해 연결한 다음 `Dali::Ui::SelectableTrait::SetSelected`, `Dali::Ui::SelectableTrait::IsSelected`, `Dali::Ui::SelectableTrait::SelectionChangedSignal`을 사용합니다.

```cpp
class SelectionController
{
public:
  void Create()
  {
    mItem = View::New();

    mItem.AsSelectable([this](SelectableTrait& trait)
    {
      trait.SetSelected(false);
      trait.EnableToggleByClick(true);
      trait.SelectionChangedSignal().Connect(this, &SelectionController::OnSelectionChanged);
    });
  }

  void OnSelectionChanged(View view, bool selected, InputEvent event)
  {
    mSelectedView = view;
    mSelected = selected;
  }

private:
  View mItem;
  View mSelectedView;
  bool mSelected{false};
};
```

`Dali::Ui::SelectableTrait::EnableToggleByClick`로 클릭 토글을 활성화하면 소유 View를 클릭할 때 선택 상태가 전환됩니다. 선택 가능 Trait는 같은 `Dali::Ui::View`에서 `Dali::Ui::InteractiveTrait`와 함께 사용할 수 있습니다. View에 Interactive Trait가 아직 없는 상태에서 클릭 토글을 먼저 활성화하면 dali-ui가 해당 View에 Interactive Trait를 생성합니다.

```cpp
void ConfigureSelectableButton(View view)
{
  view.AsInteractive([](InteractiveTrait& interactive)
  {
    interactive.SetClickable(true);
  });

  view.AsSelectable([](SelectableTrait& selectable)
  {
    selectable.EnableToggleByClick(true);
  });
}
```

클릭으로 토글할 수 있는 상태인지 확인하려면 `Dali::Ui::SelectableTrait::IsToggleByClickEnabled`를 사용합니다.

```cpp
bool IsUserToggleable(SelectableTrait trait)
{
  return trait.IsToggleByClickEnabled();
}
```

## View 애니메이션 적용 {#animate-a-view}

`Dali::Ui::View`에 애니메이션을 적용할 때는 `Dali::Ui::View::Animate`를 사용합니다. 애니메이션 대상 객체는 계속 View이며, 속성 소유권이 중요할 때는 `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION`, `Dali::Actor::Property::SCALE` 같은 상속된 transform 속성 enum으로 핵심 transform 속성을 식별합니다.

```cpp
void FadeAndMove(View view)
{
  Animation animation = Animation::New();

  view.Animate(animation)
    .Opacity(0.3f, 300_ms)
    .PositionY(50.0f, 300_ms, AlphaFunction::EASE_OUT);

  animation.Play();
}
```

같은 View 애니메이션을 여러 View에 재사용해야 할 때는 `Dali::Ui::View::NewAnimationSpec`을 사용합니다.

```cpp
class FadeController
{
public:
  void Create()
  {
    mFadeIn = View::NewAnimationSpec()
      .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);
  }

  void Play(View target)
  {
    Animation animation = Animation::New();
    mFadeIn.ApplyTo(animation, target);
    animation.Play();
  }

private:
  ViewAnimationSpec mFadeIn;
};
```

애니메이션은 dali-ui 애니메이션 브리지를 통해 배경색, 모서리 반경, 모서리 각짐 정도, 경계선 너비, 경계선 색상, 경계선 오프셋처럼 View가 소유한 시각 속성도 대상으로 지정할 수 있습니다.

```cpp
void Highlight(View view)
{
  Animation animation = Animation::New();

  view.Animate(animation)
    .BackgroundColor(UiColor::PRIMARY, 250_ms)
    .CornerRadius(Vector4(12.0f, 12.0f, 12.0f, 12.0f), 250_ms)
    .BorderlineWidth(2.0f, 250_ms);

  animation.Play();
}
```
