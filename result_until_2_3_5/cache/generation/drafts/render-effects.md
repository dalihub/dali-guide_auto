---
title: Render Effects
sidebar_label: Render Effects
category: images-visuals
---

# Render Effects

Render effects let a `Dali::Ui::View` render with blur or mask processing while keeping the app-facing object model in dali-ui.

## Table of Contents

- [Choosing a Render Effect](#choosing-a-render-effect)
- [Blurring a View and Its Children](#blurring-a-view-and-its-children)
- [Blurring the Background Behind a View](#blurring-the-background-behind-a-view)
- [Masking a View](#masking-a-view)
- [Refreshing, Activating, and Deactivating Effects](#refreshing-activating-and-deactivating-effects)
- [Animating Blur Strength and Opacity](#animating-blur-strength-and-opacity)
- [Handling One-Shot Render Completion](#handling-one-shot-render-completion)

## Choosing a Render Effect

All render effects derive from `Dali::Ui::RenderEffect`. An effect is assigned to one owner `Dali::Ui::View`, and that owner view has at most one render effect at a time.

Use `Dali::Ui::GaussianBlurEffect` when the view itself, including its children, should be blurred. Use `Dali::Ui::BackgroundBlurEffect` when the view should blur content behind it. Use `Dali::Ui::MaskEffect` when one `Dali::Ui::View` should provide the mask for another view.

```cpp
void ReplaceWithGaussianBlur(Dali::Ui::View contentView)
{
  Dali::Ui::GaussianBlurEffect blur = Dali::Ui::GaussianBlurEffect::New(12u);
  contentView.SetRenderEffect(blur);
}

void ReplaceWithBackgroundBlur(Dali::Ui::View overlayView)
{
  Dali::Ui::BackgroundBlurEffect blur = Dali::Ui::BackgroundBlurEffect::New(16u);
  overlayView.SetRenderEffect(blur);
}

void ReplaceWithMask(Dali::Ui::View targetView, Dali::Ui::View maskView)
{
  Dali::Ui::MaskEffect mask = Dali::Ui::MaskEffect::New(
    maskView,
    Dali::Ui::MaskEffect::ALPHA,
    Dali::Vector2(0.0f, 0.0f),
    Dali::Vector2(1.0f, 1.0f));

  targetView.SetRenderEffect(mask);
}
```

## Blurring a View and Its Children

`Dali::Ui::GaussianBlurEffect` blurs the owner `Dali::Ui::View` and its child views. Create it with `Dali::Ui::GaussianBlurEffect::New`, then tune it with typed setters such as `Dali::Ui::GaussianBlurEffect::SetBlurRadius`, `Dali::Ui::GaussianBlurEffect::SetBlurDownscaleFactor`, and `Dali::Ui::GaussianBlurEffect::SetBlurOnce`.

`Dali::Ui::GaussianBlurEffect::SetBlurRadius` sets the Gaussian kernel radius. `Dali::Ui::GaussianBlurEffect::SetBlurDownscaleFactor` sets the input texture downscale factor in the range from `0.0f` to `1.0f`. Because the implementation combines downscaling and kernel optimizations, small radius changes may not always change the visible result.

```cpp
Dali::Ui::GaussianBlurEffect ConfigureContentBlur(Dali::Ui::View contentView)
{
  Dali::Ui::GaussianBlurEffect blur = Dali::Ui::GaussianBlurEffect::New(18u);

  blur.SetBlurDownscaleFactor(0.5f);
  blur.SetBlurOnce(false);

  contentView.SetRenderEffect(blur);

  return blur;
}
```

Use `Dali::Ui::GaussianBlurEffect::GetBlurRadius`, `Dali::Ui::GaussianBlurEffect::GetBlurDownscaleFactor`, and `Dali::Ui::GaussianBlurEffect::GetBlurOnce` when you need to preserve or inspect the current effect configuration.

```cpp
void IncreaseContentBlur(Dali::Ui::GaussianBlurEffect blur)
{
  const uint32_t currentRadius = blur.GetBlurRadius();
  blur.SetBlurRadius(currentRadius + 4u);
}
```

## Blurring the Background Behind a View

`Dali::Ui::BackgroundBlurEffect` blurs the owner view's background rather than blurring the owner view's own visual content. This makes it useful for translucent panels, dialogs, sheets, and overlays where the content behind the panel should be softened.

The view tree matters for `Dali::Ui::BackgroundBlurEffect`: the effect determines its background from the surrounding hierarchy. Attach the effect to the overlay or panel view that should display the blurred background.

```cpp
Dali::Ui::BackgroundBlurEffect ApplyPanelBackgroundBlur(Dali::Ui::View panelView)
{
  Dali::Ui::BackgroundBlurEffect blur = Dali::Ui::BackgroundBlurEffect::New(20u);

  blur.SetBlurDownscaleFactor(0.25f);
  blur.SetBlurOnce(false);

  panelView.SetRenderEffect(blur);

  return blur;
}
```

For advanced hierarchy control, `Dali::Ui::BackgroundBlurEffect::SetSourceActor` can limit the source used by the background blur, and `Dali::Ui::BackgroundBlurEffect::SetStopperActor` can set a stopper in the traversal. The effect does not keep a reference to the source or stopper actor, so keep the corresponding views alive in your view tree.

```cpp
void ApplyBoundedBackgroundBlur(
  Dali::Ui::View panelView,
  Dali::Ui::View backgroundRoot,
  Dali::Ui::View blurStopper)
{
  Dali::Ui::BackgroundBlurEffect blur = Dali::Ui::BackgroundBlurEffect::New(14u);

  blur.SetSourceActor(backgroundRoot);
  blur.SetStopperActor(blurStopper);
  blur.SetBlurOnce(false);

  panelView.SetRenderEffect(blur);
}
```

## Masking a View

`Dali::Ui::MaskEffect` masks the owner `Dali::Ui::View` using another `Dali::Ui::View` as the mask source. Create it with `Dali::Ui::MaskEffect::New`, passing the mask view, `Dali::Ui::MaskEffect::MaskMode`, mask position, and mask scale.

Use `Dali::Ui::MaskEffect::ALPHA` when the mask should come from the alpha channel. Use `Dali::Ui::MaskEffect::LUMINANCE` when RGB values should be converted to grayscale and used as the mask value.

```cpp
void ApplyAlphaMask(Dali::Ui::View targetView, Dali::Ui::View maskView)
{
  Dali::Ui::MaskEffect mask = Dali::Ui::MaskEffect::New(
    maskView,
    Dali::Ui::MaskEffect::ALPHA,
    Dali::Vector2(0.0f, 0.0f),
    Dali::Vector2(1.0f, 1.0f));

  mask.SetTargetMaskOnce(true);
  mask.SetSourceMaskOnce(true);

  targetView.SetRenderEffect(mask);
}
```

Use `Dali::Ui::MaskEffect::SetTargetMaskOnce` and `Dali::Ui::MaskEffect::SetSourceMaskOnce` when the masked target or mask source is static. If either side changes every frame, set the matching value to `false`.

```cpp
void UseAnimatedMaskSource(Dali::Ui::View targetView, Dali::Ui::View animatedMaskView)
{
  Dali::Ui::MaskEffect mask = Dali::Ui::MaskEffect::New(
    animatedMaskView,
    Dali::Ui::MaskEffect::LUMINANCE,
    Dali::Vector2(0.0f, 0.0f),
    Dali::Vector2(1.0f, 1.0f));

  mask.SetTargetMaskOnce(true);
  mask.SetSourceMaskOnce(false);

  targetView.SetRenderEffect(mask);
}
```

## Refreshing, Activating, and Deactivating Effects

`Dali::Ui::RenderEffect` provides common control methods for effects. `Dali::Ui::RenderEffect::Activate` activates an effect on its owner view, `Dali::Ui::RenderEffect::Deactivate` deactivates it, `Dali::Ui::RenderEffect::Refresh` refreshes the effect rendering, and `Dali::Ui::RenderEffect::IsActivated` reports whether the effect is active.

```cpp
void TemporarilyDisableBlur(Dali::Ui::GaussianBlurEffect blur)
{
  if(blur.IsActivated())
  {
    blur.Deactivate();
  }
}

void RefreshBlur(Dali::Ui::BackgroundBlurEffect blur)
{
  blur.Refresh();

  if(!blur.IsActivated())
  {
    blur.Activate();
  }
}
```

When the effect is no longer needed on a view, clear it from the owner view.

```cpp
void RemoveRenderEffect(Dali::Ui::View view)
{
  view.ClearRenderEffect();
}
```

## Animating Blur Strength and Opacity

`Dali::Ui::GaussianBlurEffect::AddBlurStrengthAnimation` and `Dali::Ui::BackgroundBlurEffect::AddBlurStrengthAnimation` animate from clear to blurred, or from blurred to clear when the target value is smaller than the start value. The strength values must be in the `0.0f` to `1.0f` range.

```cpp
Dali::Animation AnimateGaussianBlurIn(Dali::Ui::GaussianBlurEffect blur)
{
  Dali::Animation animation = Dali::Animation::New(0.25f);

  blur.AddBlurStrengthAnimation(
    animation,
    Dali::AlphaFunction::EASE_IN_OUT,
    Dali::TimePeriod(0.25f),
    0.0f,
    1.0f);

  animation.Play();

  return animation;
}
```

`Dali::Ui::GaussianBlurEffect::AddBlurOpacityAnimation` and `Dali::Ui::BackgroundBlurEffect::AddBlurOpacityAnimation` animate the opacity of the blurred result. This is useful when you want the blur contribution to fade without changing the configured blur radius.

```cpp
Dali::Animation FadeBackgroundBlurOut(Dali::Ui::BackgroundBlurEffect blur)
{
  Dali::Animation animation = Dali::Animation::New(0.2f);

  blur.AddBlurOpacityAnimation(
    animation,
    Dali::AlphaFunction::EASE_IN_OUT,
    Dali::TimePeriod(0.2f),
    1.0f,
    0.0f);

  animation.Play();

  return animation;
}
```

## Handling One-Shot Render Completion

When `Dali::Ui::GaussianBlurEffect::SetBlurOnce` or `Dali::Ui::BackgroundBlurEffect::SetBlurOnce` is set to `true`, the effect renders once instead of refreshing every frame. In that mode, connect to `Dali::Ui::GaussianBlurEffect::FinishedSignal` or `Dali::Ui::BackgroundBlurEffect::FinishedSignal` to be notified when rendering has finished.

```cpp
class BlurController : public Dali::ConnectionTracker
{
public:
  void ApplyOneShotContentBlur(Dali::Ui::View contentView)
  {
    mGaussianBlur = Dali::Ui::GaussianBlurEffect::New(16u);
    mGaussianBlur.SetBlurOnce(true);
    mGaussianBlur.FinishedSignal().Connect(this, &BlurController::OnGaussianBlurFinished);

    contentView.SetRenderEffect(mGaussianBlur);
  }

  void ApplyOneShotBackgroundBlur(Dali::Ui::View panelView)
  {
    mBackgroundBlur = Dali::Ui::BackgroundBlurEffect::New(16u);
    mBackgroundBlur.SetBlurOnce(true);
    mBackgroundBlur.FinishedSignal().Connect(this, &BlurController::OnBackgroundBlurFinished);

    panelView.SetRenderEffect(mBackgroundBlur);
  }

private:
  void OnGaussianBlurFinished()
  {
    mGaussianBlur.Deactivate();
  }

  void OnBackgroundBlurFinished()
  {
    mBackgroundBlur.Deactivate();
  }

  Dali::Ui::GaussianBlurEffect   mGaussianBlur;
  Dali::Ui::BackgroundBlurEffect mBackgroundBlur;
};
```
