---
title: Render Effects
sidebar_label: Render Effects
category: uncategorized
---

# Render Effects

Render effects provide visual effects that can be applied to `Dali::Ui::View` objects. The dali-ui framework offers blur effects and masking capabilities through a unified effect system.

## Table of Contents

- [Applying Render Effects to Views](#applying-render-effects-to-views)
- [Background Blur Effect](#background-blur-effect)
- [Gaussian Blur Effect](#gaussian-blur-effect)
- [Mask Effect](#mask-effect)
- [Animating Blur Effects](#animating-blur-effects)

---

## Applying Render Effects to Views

All render effects inherit from `Dali::Ui::RenderEffect` and are applied to a `View` using `SetRenderEffect()`. A View can have at most one render effect at a time.

```cpp
// Create a render effect
auto effect = Dali::Ui::BackgroundBlurEffect::New(20);

// Apply to a view
view.SetRenderEffect(effect);

// The effect is now active

// Deactivate without removing
effect.Deactivate();

// Reactivate
effect.Activate();

// Remove the effect completely
view.ClearRenderEffect();
```

Use `GetRenderEffect()` to retrieve the currently applied effect:

```cpp
auto effect = view.GetRenderEffect();
if (effect && effect.IsActivated())
{
  // Effect is active
}
```

Call `Refresh()` to force a re-render of the effect:

```cpp
effect.Refresh();
```

---

## Background Blur Effect

`Dali::Ui::BackgroundBlurEffect` blurs the background behind the owner View. This is useful for creating frosted glass or depth-of-field visual effects.

### Creating a Background Blur

```cpp
// Create with default radius (10)
auto blur = Dali::Ui::BackgroundBlurEffect::New();

// Create with specific radius
auto blur = Dali::Ui::BackgroundBlurEffect::New(25);
```

### Configuring Blur Parameters

```cpp
auto blur = Dali::Ui::BackgroundBlurEffect::New(30);

// Set blur radius
blur.SetBlurRadius(50);

// Get current radius
uint32_t radius = blur.GetBlurRadius();

// Set downscale factor (0.0f to 1.0f) for performance optimization
blur.SetBlurDownscaleFactor(0.25f);

// Get downscale factor
float factor = blur.GetBlurDownscaleFactor();
```

The effective blur radius changes in fixed increments based on the downscale factor. The increment size is `(2 / downscaleFactor)`. For example, with `downscaleFactor = 0.25f`, the effective blur radius changes in steps of 8 pixels.

### One-Time Rendering

For static content, enable one-time rendering to improve performance:

```cpp
blur.SetBlurOnce(true);

// Check current setting
bool isOnce = blur.GetBlurOnce();
```

When `blurOnce` is true, connect to `FinishedSignal()` to be notified when rendering completes:

```cpp
blur.FinishedSignal().Connect(this, &MyClass::OnBlurFinished);
```

### Specifying Source and Stopper Actors

Control which part of the scene is blurred:

```cpp
// Set a specific source actor for the blur
blur.SetSourceActor(backgroundActor);

// Set a stopper actor to limit the blur region
blur.SetStopperActor(foregroundActor);
```

---

## Gaussian Blur Effect

`Dali::Ui::GaussianBlurEffect` blurs the owner View and all its children. This creates a uniform blur over the entire view hierarchy.

### Creating a Gaussian Blur

```cpp
// Create with default radius (10)
auto blur = Dali::Ui::GaussianBlurEffect::New();

// Create with specific radius
auto blur = Dali::Ui::GaussianBlurEffect::New(40);
```

### Configuring Gaussian Blur

```cpp
auto blur = Dali::Ui::GaussianBlurEffect::New(35);

// Set blur radius
blur.SetBlurRadius(60);
uint32_t radius = blur.GetBlurRadius();

// Set downscale factor
blur.SetBlurDownscaleFactor(0.5f);
float factor = blur.GetBlurDownscaleFactor();

// Enable one-time rendering for static content
blur.SetBlurOnce(true);
bool isOnce = blur.GetBlurOnce();
```

### Finished Signal

Connect to the finished signal for one-time blur operations:

```cpp
blur.SetBlurOnce(true);
blur.FinishedSignal().Connect(this, &MyClass::OnBlurFinished);
```

---

## Mask Effect

`Dali::Ui::MaskEffect` uses one View to mask another View. The mask can use alpha channel or luminance values.

### Creating a Mask Effect

```cpp
// Create a mask view
auto maskView = Dali::Ui::Label::New("MASK")
  .SetRequestedWidth(400)
  .SetRequestedHeight(100)
  .SetTextColor(Dali::Ui::UiColor(0x3A6D75));

// Create the target view to be masked
auto targetView = Dali::Ui::Label::New("Hello")
  .SetRequestedWidth(400)
  .SetRequestedHeight(100)
  .SetTextColor(Dali::Ui::UiColor(0xff0000));

// Add mask view as child of target
targetView.Add(maskView);

// Apply mask effect
targetView.SetRenderEffect(Dali::Ui::MaskEffect::New(maskView));
```

### Mask Modes

`MaskEffect` supports two mask modes:

- `MaskEffect::MaskMode::ALPHA` — Uses the alpha channel of the mask texture (default)
- `MaskEffect::MaskMode::LUMINANCE` — Converts RGB to grayscale and uses luminance as the mask value

```cpp
// Create with luminance mode and custom position/scale
auto mask = Dali::Ui::MaskEffect::New(
  maskView,
  Dali::Ui::MaskEffect::MaskMode::LUMINANCE,
  Dali::Vector2(0.0f, 0.0f),  // position
  Dali::Vector2(1.0f, 1.0f)   // scale
);
```

### One-Time Mask Rendering

For static mask content, enable one-time rendering:

```cpp
auto mask = Dali::Ui::MaskEffect::New(maskView);

// Render target once
mask.SetTargetMaskOnce(true);

// Render mask source once
mask.SetSourceMaskOnce(true);

// Check settings
bool targetOnce = mask.GetTargetMaskOnce();
bool sourceOnce = mask.GetSourceMaskOnce();
```

---

## Animating Blur Effects

Both `BackgroundBlurEffect` and `GaussianBlurEffect` support animated transitions for blur strength and opacity.

### Animating Blur Strength

```cpp
auto blur = Dali::Ui::BackgroundBlurEffect::New(30);
view.SetRenderEffect(blur);

// Create an animation
auto animation = Dali::Animation::New(1000.0f);  // 1000ms duration

// Add blur strength animation (0.0 = no blur, 1.0 = full blur)
blur.AddBlurStrengthAnimation(
  animation,
  Dali::AlphaFunction::EASE_IN_OUT,
  Dali::TimePeriod(0.0f, 1000.0f),  // start time, duration
  0.0f,  // from: clear
  1.0f   // to: fully blurred
);

animation.Play();
```

### Animating Blur Opacity

```cpp
auto blur = Dali::Ui::GaussianBlurEffect::New(25);
view.SetRenderEffect(blur);

auto animation = Dali::Animation::New(500.0f);

// Fade blur in or out
blur.AddBlurOpacityAnimation(
  animation,
  Dali::AlphaFunction::EASE_OUT,
  Dali::TimePeriod(0.0f, 500.0f),
  0.0f,  // from: invisible
  1.0f   // to: visible
);

animation.Play();
```

### Reversed Animations

Set `fromValue` greater than `toValue` for reversed animations (blurred to clear):

```cpp
blur.AddBlurStrengthAnimation(
  animation,
  Dali::AlphaFunction::EASE_IN,
  Dali::TimePeriod(0.0f, 800.0f),
  1.0f,  // from: fully blurred
  0.0f   // to: clear
);
```

Note that the Gaussian curve itself is innately non-linear, so consider this when choosing an alpha function.
