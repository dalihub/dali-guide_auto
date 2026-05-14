---
title: Render Effects
sidebar_label: Render Effects
category: images-visuals
---

# Render Effects

Render effects apply real-time visual effects such as blur and masking to views in your dali-ui application. Each effect inherits from `RenderEffect` and is attached to a `View` using `SetRenderEffect()`.

## Table of Contents

- [Applying Render Effects](#applying-render-effects)
- [Gaussian Blur Effect](#gaussian-blur-effect)
- [Background Blur Effect](#background-blur-effect)
- [Mask Effect](#mask-effect)
- [Animating Blur Effects](#animating-blur-effects)
- [Effect Lifecycle](#effect-lifecycle)

## Applying Render Effects

A `View` can have at most one render effect attached at a time. Use `SetRenderEffect()` to attach an effect, `GetRenderEffect()` to retrieve it, and `ClearRenderEffect()` to remove it.

```cpp
// Create and attach a blur effect
auto blurEffect = Dali::Ui::GaussianBlurEffect::New(20);
view.SetRenderEffect(blurEffect);

// Later, clear the effect
view.ClearRenderEffect();
```

When you call `SetRenderEffect()`, the effect is automatically activated. Calling `ClearRenderEffect()` deactivates the effect.

## Gaussian Blur Effect

`GaussianBlurEffect` applies a Gaussian blur to the owner view and all its children. The effect renders the view content to an offscreen buffer and applies a two-pass blur algorithm.

### Creating a Gaussian Blur

Create a blur effect with a specific radius:

```cpp
// Create with default radius (10 units)
auto defaultBlur = Dali::Ui::GaussianBlurEffect::New();

// Create with custom radius
auto blurEffect = Dali::Ui::GaussianBlurEffect::New(25);
view.SetRenderEffect(blurEffect);
```

### Configuring Blur Parameters

Control the blur intensity and performance with radius and downscale factor:

```cpp
auto blurEffect = Dali::Ui::GaussianBlurEffect::New(30);

// Adjust blur radius at runtime
blurEffect.SetBlurRadius(40);

// Improve performance by downscaling (0.0f to 1.0f)
blurEffect.SetBlurDownscaleFactor(0.5f);

view.SetRenderEffect(blurEffect);
```

The downscale factor reduces the internal render target size. A value of `0.5f` renders at half resolution, improving performance at the cost of blur quality. Due to kernel optimizations, the effective blur radius changes in fixed increments based on the downscale factor.

### One-Time Rendering

For static content, enable `SetBlurOnce()` to render the blur only once instead of every frame:

```cpp
auto blurEffect = Dali::Ui::GaussianBlurEffect::New(20);
blurEffect.SetBlurOnce(true);
view.SetRenderEffect(blurEffect);

// Connect to finished signal to know when rendering completes
blurEffect.FinishedSignal().Connect(this, &MyClass::OnBlurFinished);
```

## Background Blur Effect

`BackgroundBlurEffect` blurs the content behind a view, creating a frosted glass effect. Unlike `GaussianBlurEffect`, which blurs the view itself, this effect blurs the view's background region.

### Creating a Background Blur

```cpp
auto bgBlur = Dali::Ui::BackgroundBlurEffect::New(15);
overlayView.SetRenderEffect(bgBlur);
```

### Defining the Blur Source

By default, `BackgroundBlurEffect` captures content from the parent hierarchy. Use `SetSourceActor()` to limit the blur to a specific ancestor:

```cpp
auto bgBlur = Dali::Ui::BackgroundBlurEffect::New(20);

// Only blur content from this specific parent
bgBlur.SetSourceActor(parentContainer);

// Stop capturing at a certain point in the hierarchy
bgBlur.SetStopperActor(stopperView);

overlayView.SetRenderEffect(bgBlur);
```

The source actor must be a parent of the view for the effect to work correctly.

### Background Blur Properties

```cpp
auto bgBlur = Dali::Ui::BackgroundBlurEffect::New();

// Configure blur intensity
bgBlur.SetBlurRadius(25);
bgBlur.SetBlurDownscaleFactor(0.25f);

// Render once for static backgrounds
bgBlur.SetBlurOnce(true);

view.SetRenderEffect(bgBlur);
```

## Mask Effect

`MaskEffect` uses one view's content to mask another view. The mask can use alpha channel or luminance values to determine visibility.

### Basic Masking

Create a mask effect by providing a mask view:

```cpp
// Create the mask source
auto maskView = Dali::Ui::Label::New("MASK")
  .SetRequestedWidth(200)
  .SetRequestedHeight(100)
  .SetTextColor(Dali::Ui::UiColor(0xFFFFFF));

// Create the target to be masked
auto targetView = Dali::Ui::Label::New("Hello World")
  .SetRequestedWidth(200)
  .SetRequestedHeight(100)
  .SetTextColor(Dali::Ui::UiColor(0xFF0000));

// Add mask as child and apply effect
targetView.Add(maskView);
targetView.SetRenderEffect(Dali::Ui::MaskEffect::New(maskView));
```

### Mask Modes

`MaskEffect` supports two mask modes:

- `MaskMode::ALPHA` — Uses the alpha channel of the mask (default)
- `MaskMode::LUMINANCE` — Converts RGB to grayscale and uses luminance

```cpp
auto maskEffect = Dali::Ui::MaskEffect::New(
  maskView,
  Dali::Ui::MaskEffect::MaskMode::LUMINANCE,
  Dali::Vector2(0.0f, 0.0f),  // mask position
  Dali::Vector2(1.0f, 1.0f)   // mask scale
);
targetView.SetRenderEffect(maskEffect);
```

### Positioning and Scaling the Mask

Adjust the mask position and scale to align it with the target content:

```cpp
auto maskEffect = Dali::Ui::MaskEffect::New(
  maskView,
  Dali::Ui::MaskEffect::MaskMode::ALPHA,
  Dali::Vector2(10.0f, 5.0f),   // offset position
  Dali::Vector2(1.5f, 1.5f)     // scale up mask
);
```

### One-Time Mask Rendering

For static masks, enable one-time rendering to improve performance:

```cpp
auto maskEffect = Dali::Ui::MaskEffect::New(maskView);

// Render target only once
maskEffect.SetTargetMaskOnce(true);

// Render mask source only once
maskEffect.SetSourceMaskOnce(true);

view.SetRenderEffect(maskEffect);
```

## Animating Blur Effects

Both `GaussianBlurEffect` and `BackgroundBlurEffect` support animated blur strength and opacity transitions.

### Blur Strength Animation

Animate the blur intensity from clear to blurred (or vice versa):

```cpp
auto blurEffect = Dali::Ui::GaussianBlurEffect::New(30);
view.SetRenderEffect(blurEffect);

// Create animation
auto animation = Dali::Animation::New(1000);  // 1 second

// Animate from clear (0.0) to fully blurred (1.0)
blurEffect.AddBlurStrengthAnimation(
  animation,
  Dali::AlphaFunction::EASE_IN_OUT,
  Dali::TimePeriod(0.0f, 1000.0f),
  0.0f,  // from: clear
  1.0f   // to: fully blurred
);

animation.Play();
```

### Blur Opacity Animation

Animate the visibility of the blur overlay:

```cpp
auto blurEffect = Dali::Ui::BackgroundBlurEffect::New(20);
view.SetRenderEffect(blurEffect);

auto animation = Dali::Animation::New(500);

// Fade in the blur effect
blurEffect.AddBlurOpacityAnimation(
  animation,
  Dali::AlphaFunction::EASE_IN,
  Dali::TimePeriod(0.0f, 500.0f),
  0.0f,  // from: invisible
  1.0f   // to: visible
);

animation.Play();
```

### Reversing Blur Animations

To animate from blurred to clear, swap the from and to values:

```cpp
// Animate from blurred to clear
blurEffect.AddBlurStrengthAnimation(
  animation,
  Dali::AlphaFunction::EASE_OUT,
  Dali::TimePeriod(0.0f, 800.0f),
  1.0f,  // from: fully blurred
  0.0f   // to: clear
);
```

## Effect Lifecycle

### Manual Activation and Deactivation

While `SetRenderEffect()` and `ClearRenderEffect()` manage activation automatically, you can also control the effect manually:

```cpp
auto blurEffect = Dali::Ui::GaussianBlurEffect::New(20);
view.SetRenderEffect(blurEffect);

// Effect is now active
bool isActive = blurEffect.IsActivated();  // true

// Temporarily deactivate without removing
blurEffect.Deactivate();

// Reactivate later
blurEffect.Activate();
```

### Refreshing the Effect

Force a re-render of the effect when content changes:

```cpp
// Update the view content
view.SetBackgroundColor(Dali::Ui::UiColor(0xFF0000));

// Refresh the blur effect to capture new content
auto effect = view.GetRenderEffect();
effect.Refresh();
```

### Finished Signal

Connect to the `FinishedSignal()` to be notified when one-time rendering completes:

```cpp
class MyController : public Dali::ConnectionTracker
{
  void SetupBlur()
  {
    mBlurEffect = Dali::Ui::GaussianBlurEffect::New(25);
    mBlurEffect.SetBlurOnce(true);
    mBlurEffect.FinishedSignal().Connect(this, &MyController::OnBlurFinished);
    mView.SetRenderEffect(mBlurEffect);
  }

  void OnBlurFinished()
  {
    // Blur rendering complete, safe to show or animate
  }

private:
  Dali::Ui::GaussianBlurEffect mBlurEffect;
  Dali::Ui::View mView;
};
```
