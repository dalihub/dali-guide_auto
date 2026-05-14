---
title: 애니메이션
sidebar_label: 애니메이션
category: animation-motion
---

# 애니메이션 {#animation}

타입이 지정된 dali-ui 애니메이션 브리지와 재사용 가능한 애니메이션 사양을 사용해 `Dali::Ui::View` 객체에 애니메이션을 적용합니다.

## 목차 {#table-of-contents}

- [뷰 직접 애니메이션하기](#animating-a-view-directly)
- [재사용 가능한 애니메이션 사양](#reusable-animation-specs)
- [타이밍, 지연, 이징](#timing-delays-and-easing)
- [재생 제어](#playback-control)
- [프로퍼티 소유권](#property-ownership)

## 뷰 직접 애니메이션하기 {#animating-a-view-directly}

앱 코드에서는 이동하거나 페이드되어야 하는 `Dali::Ui::View`에서 시작합니다. `Dali::Animation`을 만들고, `Dali::Ui::View::Animate`를 통해 타입이 지정된 애니메이션 항목을 추가한 다음 `Dali::Animation::Play`를 호출합니다.

`Dali::Ui::ViewAnimationBridge`는 `Dali::Ui::View::Animate`가 반환하는 가벼운 스택 할당 프록시입니다. 이 프록시는 `Opacity`, `PositionX`, `PositionY`, `ScaleX`, `ScaleY` 같은 fluent 호출을 지원합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void FadeAndMove(View panel)
{
  Animation animation = Animation::New(0.6f);

  panel.Animate(animation)
    .Opacity(1.0f, Duration(0.3f))
    .PositionY(80.0f, Duration(0.6f), AlphaFunction(AlphaFunction::EASE_OUT));

  animation.Play();
}
```

목표 값이 현재 값에 대한 상대값이어야 할 때는 `OpacityBy`, `PositionXBy`, `PositionYBy`, `ScaleXBy`, `ScaleYBy`를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void Nudge(View item)
{
  Animation animation = Animation::New(0.25f);

  item.Animate(animation)
    .PositionXBy(24.0f, Duration(0.25f))
    .OpacityBy(-0.2f, Duration(0.25f));

  animation.Play();
}
```

## 재사용 가능한 애니메이션 사양 {#reusable-animation-specs}

같은 애니메이션 레시피를 여러 뷰에 적용하거나 나중에 다시 재생해야 한다면 `Dali::Ui::ViewAnimationSpec`을 사용합니다. `Dali::Ui::View::NewAnimationSpec`으로 사양을 한 번 구성한 뒤, `Dali::Ui::ViewAnimationSpec::ApplyTo`로 적용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

ViewAnimationSpec CreateEnterSpec()
{
  return View::NewAnimationSpec()
    .Opacity(1.0f, Duration(0.25f))
    .PositionY(0.0f, Duration(0.35f), AlphaFunction(AlphaFunction::EASE_OUT));
}

void PlayEnter(View card, const ViewAnimationSpec& enterSpec)
{
  Animation animation = Animation::New(0.35f);

  enterSpec.ApplyTo(animation, card);
  animation.Play();
}
```

사양은 애니메이션 항목을 저장할 뿐, 자체적으로 재생되지는 않습니다. `ApplyTo`는 사용자가 제공한 `Dali::Animation`에 해당 항목들을 적용하므로, 동일한 `Dali::Animation` 재생 제어 기능을 그대로 사용할 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void ApplySharedSpec(View first, View second)
{
  ViewAnimationSpec fadeOut = View::NewAnimationSpec()
    .Opacity(0.0f, Duration(0.2f));

  Animation firstAnimation = Animation::New(0.2f);
  fadeOut.ApplyTo(firstAnimation, first);
  firstAnimation.Play();

  Animation secondAnimation = Animation::New(0.2f);
  fadeOut.ApplyTo(secondAnimation, second);
  secondAnimation.PlayAfter(0.1f);
}
```

`Dali::Ui::LabelAnimationBridge`와 `Dali::Ui::LabelAnimationSpec`은 레이블 전용 애니메이션 항목에 대해 같은 브리지/사양 모델을 따릅니다. 애니메이션을 적용할 객체가 dali-ui 레이블 컨트롤이고, 애니메이션 대상이 레이블이 소유한 시각 상태라면 `Dali::Ui::Label::Animate`와 `Dali::Ui::Label::NewAnimationSpec`을 통해 생성합니다.

## 타이밍, 지연, 이징 {#timing-delays-and-easing}

타입이 지정된 dali-ui 애니메이션 호출은 지속 시간에 `Dali::Ui::Duration`을 사용하며, 선택적으로 지연 시간을 받을 수 있습니다. `Dali::Ui::Duration`은 초 단위 시간을 저장하고 `InSeconds` 또는 `InMilliseconds`로 조회할 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void PlayDelayedFade(View view)
{
  Duration delay(0.15f);
  Duration fadeTime(0.4f);

  Animation animation = Animation::New(delay.InSeconds() + fadeTime.InSeconds());

  view.Animate(animation)
    .Opacity(0.0f, fadeTime, AlphaFunction(AlphaFunction::EASE_IN), delay);

  animation.Play();
}
```

브리지와 사양 구현은 각 항목에 필요한 애니메이션 시간으로 지연 시간과 지속 시간을 적용합니다. 어떤 항목에 필요한 시간이 제공된 `Dali::Animation`의 지속 시간보다 길면, 그 항목을 적용하기 전에 지속 시간이 확장됩니다.

시간에 따라 값이 어떻게 변하는지 조정하려면 `Dali::AlphaFunction`을 사용합니다. 기본 `Dali::AlphaFunction`은 선형입니다. `Dali::AlphaFunction::EASE_IN`, `Dali::AlphaFunction::EASE_OUT`, `Dali::AlphaFunction::EASE_IN_OUT` 같은 내장 이징 값은 `Dali::AlphaFunction`을 통해 전달할 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void Emphasize(View view)
{
  Animation animation = Animation::New(0.45f);

  view.Animate(animation)
    .ScaleX(1.1f, Duration(0.45f), AlphaFunction(AlphaFunction::EASE_IN_OUT))
    .ScaleY(1.1f, Duration(0.45f), AlphaFunction(AlphaFunction::EASE_IN_OUT));

  animation.Play();
}
```

## 재생 제어 {#playback-control}

`Dali::Animation`은 재생 상태를 소유합니다. dali-ui의 타입 지정 항목을 추가한 뒤에는 애니메이션 핸들에서 `Play`, `Pause`, `Stop`, `PlayFrom`, `PlayAfter`, `SetLoopCount`, `SetLooping`, `SetLoopingMode`, `SetSpeedFactor`, `SetPlayRange`, `SetEndAction`, `Clear`를 사용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void Pulse(View view)
{
  Animation animation = Animation::New(0.5f);

  view.Animate(animation)
    .Opacity(0.35f, Duration(0.5f));

  animation.SetLoopCount(0);
  animation.SetLoopingMode(Animation::AUTO_REVERSE);
  animation.Play();
}
```

`GetState`, `GetCurrentProgress`, `GetDuration`, `GetLoopCount`, `GetCurrentLoop`, `GetSpeedFactor`, `GetPlayRange` 같은 getter로 애니메이션을 검사할 수 있습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void PauseIfPlaying(Animation animation)
{
  if(animation.GetState() == Animation::PLAYING)
  {
    animation.Pause();
  }
}
```

애니메이션이 완료된 뒤 앱 로직을 실행해야 한다면 `Dali::Animation::FinishedSignal`을 사용합니다. 시그널 콜백은 완료된 `Dali::Animation`을 받습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class Controller : public ConnectionTracker
{
public:
  void PlayDismiss(View view)
  {
    Animation animation = Animation::New(0.2f);

    view.Animate(animation)
      .Opacity(0.0f, Duration(0.2f));

    animation.FinishedSignal().Connect(this, &Controller::OnDismissFinished);
    animation.Play();
  }

private:
  void OnDismissFinished(Animation animation)
  {
    animation.Clear();
  }
};
```

## 프로퍼티 소유권 {#property-ownership}

dali-ui 애플리케이션 코드에서는 프로퍼티를 직접 지정하는 방식보다 타입이 지정된 `Dali::Ui::ViewAnimationBridge` 및 `Dali::Ui::ViewAnimationSpec` 호출을 우선적으로 사용합니다. 앱에서 다루는 객체는 계속 `Dali::Ui::View`입니다.

일부 애니메이션 값은 상속된 transform 또는 opacity 프로퍼티입니다. 이러한 프로퍼티를 enum 소유자 기준으로 설명할 때, 타입이 지정된 뷰 애니메이션 호출은 `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION_X`, `Dali::Actor::Property::POSITION_Y`, `Dali::Actor::Property::SCALE_X`, `Dali::Actor::Property::SCALE_Y` 같은 `Dali::Actor::Property` 값에 매핑됩니다. dali-ui 브리지는 저수준 프로퍼티 연결을 숨기면서도 뷰에 애니메이션을 적용합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void ShowAsSelected(View view)
{
  Animation animation = Animation::New(0.3f);

  view.Animate(animation)
    .Opacity(1.0f, Duration(0.3f))
    .ScaleX(1.05f, Duration(0.3f))
    .ScaleY(1.05f, Duration(0.3f));

  animation.SetEndAction(Animation::BAKE);
  animation.Play();
}
```
