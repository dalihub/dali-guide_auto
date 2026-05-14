---
title: 렌더 효과
sidebar_label: 렌더 효과
category: images-visuals
---

# 렌더 효과 {#render-effects}

렌더 효과를 사용하면 효과 설정은 형식이 지정된 dali-ui API로 유지하면서, dali-ui `View`를 블러 또는 마스크 효과를 거쳐 렌더링할 수 있습니다.

## 목차 {#table-of-contents}

- [렌더 효과 선택](#choosing-a-render-effect)
- [뷰 블러 처리](#blurring-a-view)
- [뷰 뒤의 배경 블러 처리](#blurring-the-background-behind-a-view)
- [다른 뷰로 마스킹](#masking-with-another-view)
- [효과 활성화, 새로 고침, 재사용](#activating-refreshing-and-reusing-effects)
- [일회성 렌더링과 완료 시그널](#one-shot-rendering-and-completion-signals)
- [블러 강도와 불투명도 애니메이션](#animating-blur-strength-and-opacity)

## 렌더 효과 선택 {#choosing-a-render-effect}

`Dali::Ui::RenderEffect`는 dali-ui 렌더 효과를 위한 공통 인터페이스 핸들입니다. 애플리케이션 코드는 일반적으로 구체적인 효과 타입 중 하나를 생성한 뒤, `Dali::Ui::View::SetRenderEffect`를 사용해 `Dali::Ui::View`에 설정합니다.

소유자 뷰와 그 자식까지 함께 블러 처리해야 할 때는 `Dali::Ui::GaussianBlurEffect`를 사용합니다. 소유자 뷰 뒤의 콘텐츠를 블러 처리해야 할 때는 `Dali::Ui::BackgroundBlurEffect`를 사용합니다. 하나의 `Dali::Ui::View`가 다른 뷰의 마스크 역할을 해야 할 때는 `Dali::Ui::MaskEffect`를 사용합니다.

```cpp
using namespace Dali;
using namespace Dali::Ui;

GaussianBlurEffect CreateContentBlur()
{
  GaussianBlurEffect blur = GaussianBlurEffect::New(16u);
  blur.SetBlurDownscaleFactor(0.25f);
  blur.SetBlurOnce(false);
  return blur;
}

BackgroundBlurEffect CreatePanelBackgroundBlur()
{
  BackgroundBlurEffect blur = BackgroundBlurEffect::New(24u);
  blur.SetBlurDownscaleFactor(0.25f);
  blur.SetBlurOnce(false);
  return blur;
}

MaskEffect CreateAlphaMask(View maskView)
{
  MaskEffect effect = MaskEffect::New(
    maskView,
    MaskEffect::ALPHA,
    Vector2(0.0f, 0.0f),
    Vector2(1.0f, 1.0f));

  effect.SetSourceMaskOnce(true);
  effect.SetTargetMaskOnce(false);
  return effect;
}
```

## 뷰 블러 처리 {#blurring-a-view}

`Dali::Ui::GaussianBlurEffect`는 소유자 뷰와 그 자식을 함께 블러 처리합니다. `Dali::Ui::GaussianBlurEffect::New`로 효과를 생성한 다음, `SetBlurRadius`, `SetBlurDownscaleFactor`, `SetBlurOnce` 같은 형식 지정 setter로 설정합니다.

`SetBlurRadius`는 Gaussian 커널 반경을 설정합니다. `SetBlurDownscaleFactor`는 입력 텍스처의 다운스케일 계수를 제어하며 `0.0f`부터 `1.0f`까지의 값을 받습니다. 구현 내부에서 다운스케일링과 커널 최적화를 사용하므로, 적용되는 블러는 `2 / downscaleFactor`의 고정 증분 단위로 바뀝니다. 예를 들어 다운스케일 계수가 `0.25f`이면 유효 반경은 8픽셀 단위로 변경됩니다.

```cpp
using namespace Dali;
using namespace Dali::Ui;

GaussianBlurEffect ConfigureViewBlur(uint32_t radius)
{
  GaussianBlurEffect blur = GaussianBlurEffect::New(radius);

  blur.SetBlurDownscaleFactor(0.25f);
  blur.SetBlurRadius(24u);
  blur.SetBlurOnce(false);

  const uint32_t configuredRadius = blur.GetBlurRadius();
  const float downscale = blur.GetBlurDownscaleFactor();
  const bool rendersOnce = blur.GetBlurOnce();

  (void)configuredRadius;
  (void)downscale;
  (void)rendersOnce;

  return blur;
}
```

정적인 콘텐츠에는 `SetBlurOnce(true)`를 사용해 블러를 한 번만 렌더링합니다. 계속 변경되는 콘텐츠에는 블러 결과가 매 프레임 갱신되도록 `SetBlurOnce(false)`를 유지합니다.

```cpp
using namespace Dali::Ui;

GaussianBlurEffect CreateStaticThumbnailBlur()
{
  GaussianBlurEffect blur = GaussianBlurEffect::New(12u);
  blur.SetBlurOnce(true);
  return blur;
}

GaussianBlurEffect CreateLiveContentBlur()
{
  GaussianBlurEffect blur = GaussianBlurEffect::New(12u);
  blur.SetBlurOnce(false);
  return blur;
}
```

## 뷰 뒤의 배경 블러 처리 {#blurring-the-background-behind-a-view}

`Dali::Ui::BackgroundBlurEffect`는 소유자 뷰의 배경을 블러 처리합니다. 이때 뷰 트리 구조가 중요합니다. 효과 소유자와 배경으로 간주되어야 하는 콘텐츠 사이의 관계가 명확해야 합니다.

배경 블러는 `Dali::Ui::GaussianBlurEffect`에서 사용하는 것과 같은 블러 제어 항목인 `SetBlurRadius`, `SetBlurDownscaleFactor`, `SetBlurOnce`로 설정합니다.

```cpp
using namespace Dali;
using namespace Dali::Ui;

BackgroundBlurEffect CreateDialogBackdropBlur()
{
  BackgroundBlurEffect blur = BackgroundBlurEffect::New(30u);

  blur.SetBlurDownscaleFactor(0.25f);
  blur.SetBlurOnce(false);

  return blur;
}
```

`Dali::Ui::BackgroundBlurEffect`는 고급 배경 경계 제어를 위해 `SetSourceActor`와 `SetStopperActor`도 제공합니다. `SetSourceActor`는 소스 actor가 소스 뷰의 부모일 때만 효과가 있으며, 이 효과는 두 actor 중 어느 쪽에 대한 참조도 보관하지 않습니다. 따라서 주변 뷰 또는 actor 계층을 통해 해당 actor가 계속 살아 있도록 유지해야 합니다.

```cpp
using namespace Dali;
using namespace Dali::Ui;

void ConfigureBackgroundBlurBounds(
  BackgroundBlurEffect& blur,
  Actor sourceActor,
  Actor stopperActor)
{
  blur.SetSourceActor(sourceActor);
  blur.SetStopperActor(stopperActor);
}
```

## 다른 뷰로 마스킹 {#masking-with-another-view}

`Dali::Ui::MaskEffect`는 다른 `Dali::Ui::View`를 마스크 소스로 사용해 소유자 뷰를 마스킹합니다. 마스크는 알파 데이터나 휘도 데이터를 사용할 수 있습니다.

마스크 뷰의 알파 채널을 사용하려면 `MaskEffect::ALPHA`를 사용합니다. 마스크 뷰의 RGB 값을 그레이스케일로 변환해 마스크 값으로 사용하려면 `MaskEffect::LUMINANCE`를 사용합니다.

```cpp
using namespace Dali;
using namespace Dali::Ui;

MaskEffect CreateLuminanceMask(View maskView)
{
  MaskEffect mask = MaskEffect::New(
    maskView,
    MaskEffect::LUMINANCE,
    Vector2(0.0f, 0.0f),
    Vector2(1.0f, 1.0f));

  mask.SetSourceMaskOnce(true);
  mask.SetTargetMaskOnce(false);

  return mask;
}
```

마스크 소스가 정적일 때는 `SetSourceMaskOnce(true)`를 사용합니다. 마스킹되는 대상도 정적일 때만 `SetTargetMaskOnce(true)`를 사용합니다. 어느 한쪽이라도 변경된다면 해당 옵션을 `false`로 유지합니다.

```cpp
using namespace Dali::Ui;

void ConfigureMaskRefreshPolicy(MaskEffect& mask, bool sourceChanges, bool targetChanges)
{
  mask.SetSourceMaskOnce(!sourceChanges);
  mask.SetTargetMaskOnce(!targetChanges);

  const bool sourceRenderedOnce = mask.GetSourceMaskOnce();
  const bool targetRenderedOnce = mask.GetTargetMaskOnce();

  (void)sourceRenderedOnce;
  (void)targetRenderedOnce;
}
```

## 효과 활성화, 새로 고침, 재사용 {#activating-refreshing-and-reusing-effects}

효과가 뷰에 연결된 뒤에는 기본 `Dali::Ui::RenderEffect` 작업으로 효과의 활성 상태와 렌더링 결과의 새로 고침 여부를 제어합니다. 각 렌더 효과에는 단일 소유자 `View`가 있으며, 하나의 뷰는 최대 하나의 렌더 효과만 소유합니다.

`Activate`는 효과를 활성화합니다. `Deactivate`는 핸들을 제거하지 않고 효과를 비활성화합니다. `Refresh`는 효과를 다시 렌더링하도록 요청합니다. `IsActivated`는 현재 활성화 상태를 보고합니다.

```cpp
using namespace Dali::Ui;

void TemporarilyDisableEffect(RenderEffect effect)
{
  if(effect.IsActivated())
  {
    effect.Deactivate();
  }

  effect.Refresh();
  effect.Activate();
}
```

타입별 setter가 필요하면 구체적인 효과를 자체 핸들 타입으로 저장할 수 있고, 코드에서 활성화 제어만 필요하면 `Dali::Ui::RenderEffect`로 저장할 수 있습니다.

```cpp
using namespace Dali::Ui;

RenderEffect CreateReusableRenderEffect()
{
  GaussianBlurEffect blur = GaussianBlurEffect::New(18u);
  blur.SetBlurOnce(true);

  RenderEffect effect = blur;
  effect.Activate();

  return effect;
}
```

## 일회성 렌더링과 완료 시그널 {#one-shot-rendering-and-completion-signals}

`Dali::Ui::GaussianBlurEffect::FinishedSignal`과 `Dali::Ui::BackgroundBlurEffect::FinishedSignal`은 `Signal<void()>`을 사용합니다. `SetBlurOnce(true)`가 설정되고 효과가 활성화되면, 대상 actor가 렌더링된 뒤 이 시그널이 알림을 보냅니다.

```cpp
using namespace Dali::Ui;

void OnBlurRendered()
{
}

void ConfigureOneShotGaussianBlur(GaussianBlurEffect& blur)
{
  blur.SetBlurOnce(true);
  blur.FinishedSignal().Connect(&OnBlurRendered);
}
```

`Dali::Ui::BackgroundBlurEffect`도 동일한 형태의 시그널을 사용합니다.

```cpp
using namespace Dali::Ui;

void OnBackgroundBlurRendered()
{
}

void ConfigureOneShotBackgroundBlur(BackgroundBlurEffect& blur)
{
  blur.SetBlurOnce(true);
  blur.FinishedSignal().Connect(&OnBackgroundBlurRendered);
}
```

## 블러 강도와 불투명도 애니메이션 {#animating-blur-strength-and-opacity}

두 블러 효과 모두 기존 DALi `Animation`에 자체 블러 애니메이션을 추가할 수 있습니다. `AddBlurStrengthAnimation`은 블러 강도를 `0.0f`와 `1.0f` 사이에서 애니메이션합니다. `AddBlurOpacityAnimation`은 블러 불투명도를 `0.0f`와 `1.0f` 사이에서 애니메이션합니다.

```cpp
using namespace Dali;
using namespace Dali::Ui;

void AddGaussianBlurInAnimation(
  GaussianBlurEffect& blur,
  Animation& animation,
  AlphaFunction alpha,
  TimePeriod period)
{
  blur.AddBlurStrengthAnimation(animation, alpha, period, 0.0f, 1.0f);
  blur.AddBlurOpacityAnimation(animation, alpha, period, 0.0f, 1.0f);
}
```

시각적 전환을 반대로 적용하려면 더 큰 `fromValue`와 더 작은 `toValue`를 전달합니다.

```cpp
using namespace Dali;
using namespace Dali::Ui;

void AddBackgroundBlurOutAnimation(
  BackgroundBlurEffect& blur,
  Animation& animation,
  AlphaFunction alpha,
  TimePeriod period)
{
  blur.AddBlurStrengthAnimation(animation, alpha, period, 1.0f, 0.0f);
  blur.AddBlurOpacityAnimation(animation, alpha, period, 1.0f, 0.0f);
}
```

반응형 UI에서는 애니메이션 값을 문서화된 `0.0f`에서 `1.0f` 범위 안에 유지하고, 애니메이션을 추가하기 전에 블러 반경과 다운스케일 계수를 선택합니다.
