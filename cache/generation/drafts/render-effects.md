---
title: Render Effects
sidebar_label: Render Effects
category: images-visuals
---

# Render Effects

Render effects let a `Dali::Ui::View` render with blur or mask processing while keeping the app-facing object model in dali-ui.

## Table of Contents

- [Rendering a View Through an Effect](#rendering-a-view-through-an-effect)
- [Blurring a View with GaussianBlurEffect](#blurring-a-view-with-gaussianblureffect)
- [Blurring Content Behind a View](#blurring-content-behind-a-view)
- [Masking One View with Another View](#masking-one-view-with-another-view)
- [Updating, Activating, and Reusing Effects](#updating-activating-and-reusing-effects)
- [Animating Blur Strength and Opacity](#animating-blur-strength-and-opacity)
- [Handling One-shot Render Completion](#handling-one-shot-render-completion)

## Rendering a View Through an Effect

`Dali::Ui::RenderEffect` is the shared base handle for render effects. Concrete effects such as `Dali::Ui::GaussianBlurEffect`, `Dali::Ui::BackgroundBlurEffect`, and `Dali::Ui::MaskEffect` are created first, then assigned to the owning `Dali::Ui::View`.

Each effect has a single owner view. Use a separate effect handle for each view that needs an independent render effect.

```cpp
void ApplyRenderEffect(Dali::Ui::View panel)
{
  Dali::Ui::GaussianBlurEffect blur = Dali::Ui::GaussianBlurEffect::New(12u);

  blur.SetBlurDownscaleFactor(0.5f);
  blur.SetBlurOnce(false);

  panel.SetRenderEffect(blur);
}
```

Because each concrete effect inherits from `Dali::Ui::RenderEffect`, the common control methods are available after the effect is attached.

```cpp
void TemporarilyDisableEffect(Dali::Ui::GaussianBlurEffect blur)
{
  if(blur.IsActivated())
  {
    blur.Deactivate();
  }

  blur.Activate();
  blur.Refresh();
}
```

## Blurring a View with GaussianBlurEffect

Use `Dali::Ui::GaussianBlurEffect` when the target view and its children should be blurred together. Create it with `Dali::Ui::GaussianBlurEffect::New(uint32_t blurRadius)`, then tune the blur radius, downscale factor, and update policy with typed setters.

```cpp
Dali::Ui::GaussianBlurEffect CreatePanelBlur()
{
  Dali::Ui::GaussianBlurEffect blur = Dali::Ui::GaussianBlurEffect::New(16u);

  blur.SetBlurRadius(20u);
  blur.SetBlurDownscaleFactor(0.25f);
  blur.SetBlurOnce(false);

  return blur;
}
```

`Dali::Ui::GaussianBlurEffect::GetBlurRadius`, `Dali::Ui::GaussianBlurEffect::GetBlurDownscaleFactor`, and `Dali::Ui::GaussianBlurEffect::GetBlurOnce` are useful when updating existing UI state.

```cpp
void StrengthenBlur(Dali::Ui::GaussianBlurEffect blur)
{
  const uint32_t currentRadius = blur.GetBlurRadius();
  blur.SetBlurRadius(currentRadius + 4u);

  const float downscaleFactor = blur.GetBlurDownscaleFactor();
  if(downscaleFactor > 0.5f)
  {
    blur.SetBlurDownscaleFactor(0.5f);
  }
}
```

The blur radius is a Gaussian kernel radius. The public header notes that the effective visual change is quantized by the downscale factor, so very small radius changes may not visibly change the result.

## Blurring Content Behind a View

Use `Dali::Ui::BackgroundBlurEffect` when the view should blur its background instead of blurring the view's own rendered content. This is useful for translucent panels, sheets, and overlays in a dali-ui view tree.

```cpp
Dali::Ui::BackgroundBlurEffect CreateOverlayBackgroundBlur()
{
  Dali::Ui::BackgroundBlurEffect blur = Dali::Ui::BackgroundBlurEffect::New(24u);

  blur.SetBlurDownscaleFactor(0.25f);
  blur.SetBlurOnce(false);

  return blur;
}

void ApplyOverlayEffect(Dali::Ui::View overlay)
{
  Dali::Ui::BackgroundBlurEffect blur = CreateOverlayBackgroundBlur();
  overlay.SetRenderEffect(blur);
}
```

`Dali::Ui::BackgroundBlurEffect` also provides `Dali::Ui::BackgroundBlurEffect::SetBlurRadius`, `Dali::Ui::BackgroundBlurEffect::GetBlurRadius`, `Dali::Ui::BackgroundBlurEffect::SetBlurDownscaleFactor`, and `Dali::Ui::BackgroundBlurEffect::GetBlurDownscaleFactor` with the same value roles as `Dali::Ui::GaussianBlurEffect`.

```cpp
void ConfigureBackgroundBlur(Dali::Ui::BackgroundBlurEffect blur)
{
  blur.SetBlurRadius(18u);
  blur.SetBlurDownscaleFactor(0.5f);
  blur.SetBlurOnce(true);

  const bool rendersOnce = blur.GetBlurOnce();
  if(rendersOnce)
  {
    blur.Refresh();
  }
}
```

For advanced hierarchy control, `Dali::Ui::BackgroundBlurEffect::SetSourceActor` and `Dali::Ui::BackgroundBlurEffect::SetStopperActor` can constrain where the background source is taken from. These APIs accept `Dali::Actor` handles because they operate on the underlying scene hierarchy; keep normal app composition in `Dali::Ui::View` and use these only when you already have the relevant hierarchy handles.

## Masking One View with Another View

Use `Dali::Ui::MaskEffect` to mask an owner `Dali::Ui::View` with another `Dali::Ui::View`. The mask can use alpha data or luminance data through `Dali::Ui::MaskEffect::MaskMode`.

```cpp
Dali::Ui::MaskEffect CreateAlphaMask(Dali::Ui::View maskView)
{
  Dali::Ui::MaskEffect mask = Dali::Ui::MaskEffect::New(
    maskView,
    Dali::Ui::MaskEffect::ALPHA,
    Dali::Vector2(0.0f, 0.0f),
    Dali::Vector2(1.0f, 1.0f));

  mask.SetSourceMaskOnce(true);
  mask.SetTargetMaskOnce(false);

  return mask;
}
```

Use `Dali::Ui::MaskEffect::LUMINANCE` when the mask value should come from the mask view's RGB luminance instead of its alpha channel.

```cpp
void ApplyLuminanceMask(Dali::Ui::View targetView, Dali::Ui::View maskView)
{
  Dali::Ui::MaskEffect mask = Dali::Ui::MaskEffect::New(
    maskView,
    Dali::Ui::MaskEffect::LUMINANCE,
    Dali::Vector2(0.0f, 0.0f),
    Dali::Vector2(1.0f, 1.0f));

  mask.SetSourceMaskOnce(false);
  mask.SetTargetMaskOnce(false);

  targetView.SetRenderEffect(mask);
}
```

`Dali::Ui::MaskEffect::GetSourceMaskOnce` and `Dali::Ui::MaskEffect::GetTargetMaskOnce` report whether the source or target side is rendered once or updated every frame.

```cpp
bool NeedsLiveMaskRendering(Dali::Ui::MaskEffect mask)
{
  return !mask.GetSourceMaskOnce() || !mask.GetTargetMaskOnce();
}
```

## Updating, Activating, and Reusing Effects

`Dali::Ui::RenderEffect::Activate`, `Dali::Ui::RenderEffect::Deactivate`, `Dali::Ui::RenderEffect::Refresh`, and `Dali::Ui::RenderEffect::IsActivated` are shared by all render effects. Use them when the effect configuration remains attached but its rendering should be controlled explicitly.

```cpp
void RefreshIfActive(Dali::Ui::RenderEffect effect)
{
  if(effect.IsActivated())
  {
    effect.Refresh();
  }
}
```

A concrete effect can be passed through the base `Dali::Ui::RenderEffect` handle when the calling code only needs common lifecycle control.

```cpp
void PauseAndResume(Dali::Ui::GaussianBlurEffect blur)
{
  Dali::Ui::RenderEffect effect = blur;

  effect.Deactivate();
  effect.Activate();
}
```

## Animating Blur Strength and Opacity

`Dali::Ui::GaussianBlurEffect` and `Dali::Ui::BackgroundBlurEffect` can add blur-specific animation tracks to an existing `Dali::Animation`. Use `Dali::Ui::GaussianBlurEffect::AddBlurStrengthAnimation` to animate from clear to blurred, and `Dali::Ui::GaussianBlurEffect::AddBlurOpacityAnimation` to animate how much of the blurred result is blended.

```cpp
void AddGaussianBlurInAnimation(
  Dali::Ui::GaussianBlurEffect blur,
  Dali::Animation& animation)
{
  blur.AddBlurStrengthAnimation(
    animation,
    Dali::AlphaFunction(Dali::AlphaFunction::EASE_IN_OUT),
    Dali::TimePeriod(0.0f, 0.25f),
    0.0f,
    1.0f);

  blur.AddBlurOpacityAnimation(
    animation,
    Dali::AlphaFunction(Dali::AlphaFunction::EASE_IN_OUT),
    Dali::TimePeriod(0.0f, 0.25f),
    0.0f,
    1.0f);
}
```

The background blur effect exposes the same animation methods as owned APIs: `Dali::Ui::BackgroundBlurEffect::AddBlurStrengthAnimation` and `Dali::Ui::BackgroundBlurEffect::AddBlurOpacityAnimation`.

```cpp
void AddBackgroundBlurOutAnimation(
  Dali::Ui::BackgroundBlurEffect blur,
  Dali::Animation& animation)
{
  blur.AddBlurStrengthAnimation(
    animation,
    Dali::AlphaFunction(Dali::AlphaFunction::EASE_IN_OUT),
    Dali::TimePeriod(0.0f, 0.2f),
    1.0f,
    0.0f);

  blur.AddBlurOpacityAnimation(
    animation,
    Dali::AlphaFunction(Dali::AlphaFunction::EASE_IN_OUT),
    Dali::TimePeriod(0.0f, 0.2f),
    1.0f,
    0.0f);
}
```

The `fromValue` and `toValue` arguments are normalized values in the `0.0f` to `1.0f` range. Passing a smaller `toValue` than `fromValue` creates a reverse blur transition.

## Handling One-shot Render Completion

When a blur effect is configured to render once with `Dali::Ui::GaussianBlurEffect::SetBlurOnce(true)` or `Dali::Ui::BackgroundBlurEffect::SetBlurOnce(true)`, the effect provides a finished signal after the target has been rendered.

`Dali::Ui::GaussianBlurEffect::FinishedSignal` and `Dali::Ui::BackgroundBlurEffect::FinishedSignal` both use a `void()` callback shape.

```cpp
void OnBlurFinished()
{
  // Continue with UI work that depends on the one-shot blur render.
}

void ConfigureOneShotGaussianBlur(Dali::Ui::View targetView)
{
  Dali::Ui::GaussianBlurEffect blur = Dali::Ui::GaussianBlurEffect::New(12u);

  blur.SetBlurOnce(true);
  blur.FinishedSignal().Connect(&OnBlurFinished);

  targetView.SetRenderEffect(blur);
}
```

The same pattern applies to `Dali::Ui::BackgroundBlurEffect::FinishedSignal`.

```cpp
void OnBackgroundBlurFinished()
{
  // The one-shot background blur render has completed.
}

void ConfigureOneShotBackgroundBlur(Dali::Ui::View overlay)
{
  Dali::Ui::BackgroundBlurEffect blur = Dali::Ui::BackgroundBlurEffect::New(20u);

  blur.SetBlurOnce(true);
  blur.FinishedSignal().Connect(&OnBackgroundBlurFinished);

  overlay.SetRenderEffect(blur);
}
```
