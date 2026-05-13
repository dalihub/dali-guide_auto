---
title: Render Effects
sidebar_label: Render Effects
category: images-visuals
---

# Render Effects

Render effects let a dali-ui `View` render through a blur or mask effect while keeping effect setup in typed dali-ui APIs.

## Table of Contents

- [Choosing a Render Effect](#choosing-a-render-effect)
- [Blurring a View](#blurring-a-view)
- [Blurring the Background Behind a View](#blurring-the-background-behind-a-view)
- [Masking with Another View](#masking-with-another-view)
- [Activating, Refreshing, and Reusing Effects](#activating-refreshing-and-reusing-effects)
- [One-Shot Rendering and Completion Signals](#one-shot-rendering-and-completion-signals)
- [Animating Blur Strength and Opacity](#animating-blur-strength-and-opacity)

## Choosing a Render Effect

`Dali::Ui::RenderEffect` is the common handle type for dali-ui render effects. Application code normally creates one of its concrete effect types and assigns it to a `Dali::Ui::View` through the view render-effect slot.

Use `Dali::Ui::GaussianBlurEffect` when the owner view and its children should be blurred. Use `Dali::Ui::BackgroundBlurEffect` when the owner view should blur content behind it. Use `Dali::Ui::MaskEffect` when one `Dali::Ui::View` should provide the mask for another view.

```cpp
using namespace Dali;
using namespace Dali::Ui;

GaussianBlurEffect CreateContentBlur()
{
  GaussianBlurEffect effect = GaussianBlurEffect::New(16u);
  effect.SetBlurDownscaleFactor(0.25f);
  effect.SetBlurOnce(false);
  return effect;
}

BackgroundBlurEffect CreatePanelBackgroundBlur()
{
  BackgroundBlurEffect effect = BackgroundBlurEffect::New(24u);
  effect.SetBlurDownscaleFactor(0.25f);
  effect.SetBlurOnce(false);
  return effect;
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

## Blurring a View

`Dali::Ui::GaussianBlurEffect` blurs the owner view together with its children. Create it with `Dali::Ui::GaussianBlurEffect::New`, then configure it with typed setters such as `SetBlurRadius`, `SetBlurDownscaleFactor`, and `SetBlurOnce`.

`SetBlurRadius` sets the Gaussian kernel radius. `SetBlurDownscaleFactor` controls the input texture downscale factor and accepts values between `0.0f` and `1.0f`. Because the implementation uses downscaling and kernel optimizations, very small radius changes may not produce visible changes at some downscale factors.

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

For static content, `SetBlurOnce(true)` avoids refreshing the blur every frame. For content that changes continuously, keep `SetBlurOnce(false)` so the blurred result updates.

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

## Blurring the Background Behind a View

`Dali::Ui::BackgroundBlurEffect` blurs the owner view's background. The view tree matters: the effect needs a clear relationship between the effect owner and the content that should count as background.

Configure background blur with the same blur controls used by `Dali::Ui::GaussianBlurEffect`: `SetBlurRadius`, `SetBlurDownscaleFactor`, and `SetBlurOnce`.

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

`Dali::Ui::BackgroundBlurEffect` also provides `SetSourceActor` and `SetStopperActor` for advanced background-boundary control. In dali-ui application code, prefer the `View` hierarchy as the main model and use these only when you already have the corresponding DALi actors from surrounding setup code.

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

## Masking with Another View

`Dali::Ui::MaskEffect` masks the owner view using another `Dali::Ui::View` as the mask source. The mask can use either alpha or luminance data.

Use `MaskEffect::ALPHA` to use the mask view's alpha channel. Use `MaskEffect::LUMINANCE` to convert the mask view's RGB value to grayscale and use that as the mask value.

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

Use `SetSourceMaskOnce(true)` when the mask source is static. Use `SetTargetMaskOnce(true)` only when the masked target is also static. If either side changes, keep the matching option set to `false`.

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

## Activating, Refreshing, and Reusing Effects

After an effect is associated with a view, the base `Dali::Ui::RenderEffect` operations control whether it is active and whether its rendered result should be refreshed.

`Activate` enables the effect. `Deactivate` disables it without destroying the handle. `Refresh` requests the effect to render again. `IsActivated` reports the current activation state.

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

Concrete effects can be stored as their own handle type when you need type-specific setters, or as `Dali::Ui::RenderEffect` when the code only needs activation control.

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

## One-Shot Rendering and Completion Signals

`Dali::Ui::GaussianBlurEffect::FinishedSignal` and `Dali::Ui::BackgroundBlurEffect::FinishedSignal` use `Signal<void()>`. They are useful with `SetBlurOnce(true)`, where the effect emits a completion signal after the target has been rendered.

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

The same signal shape is used by `Dali::Ui::BackgroundBlurEffect`.

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

## Animating Blur Strength and Opacity

Both blur effects can add their own blur animations to an existing DALi `Animation`. `AddBlurStrengthAnimation` animates the blur strength between `0.0f` and `1.0f`. `AddBlurOpacityAnimation` animates the opacity contribution of the blurred texture, also between `0.0f` and `1.0f`.

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

To reverse the visual transition, pass a larger `fromValue` and smaller `toValue`.

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

For responsive UI, keep the animation values in the documented `0.0f` to `1.0f` range and choose the blur radius and downscale factor before adding the animation.
