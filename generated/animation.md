---
title: Animation
sidebar_label: Animation
category: animation-motion
---

# Animation

Animation in dali-ui provides a type-safe, fluent API for animating `View` properties. The framework offers two complementary patterns: the Bridge pattern for immediate animation construction and the Spec pattern for reusable animation specifications.

## Table of Contents

- [Creating Animations](#creating-animations)
- [View-First Animation with Bridge Pattern](#view-first-animation-with-bridge-pattern)
- [Reusable Animation Specifications](#reusable-animation-specifications)
- [Alpha Functions](#alpha-functions)
- [Key Frame Animations](#key-frame-animations)
- [Animation Control](#animation-control)
- [Looping and Repeat](#looping-and-repeat)
- [Animation Lifecycle and Signals](#animation-lifecycle-and-signals)

## Creating Animations

Create an `Animation` using `Animation::New()`. The animation can be created with or without an initial duration.

```cpp
// Create with explicit duration
Animation anim = Animation::New(2.0f);

// Create without duration (auto-extended as animators are added)
Animation anim = Animation::New();
```

The animation duration is automatically extended if any individual animator exceeds the initial duration.

## View-First Animation with Bridge Pattern

The Bridge pattern provides a fluent, type-safe API for animating `View` properties. Call `View::Animate()` with an `Animation` instance to obtain a `ViewAnimationBridge`, then chain property animations.

```cpp
Animation anim = Animation::New();
view.Animate(anim)
  .Opacity(0.0f, 300_ms, AlphaFunction::EASE_OUT)
  .PositionY(50.0f, 500_ms);
anim.Play();
```

The `ViewAnimationBridge` supports both absolute (`AnimateTo`-style) and relative (`AnimateBy`-style) methods:

| Method | Description |
|--------|-------------|
| `Opacity(target, duration, alpha, delay)` | Animates opacity to target value |
| `OpacityBy(relative, duration, alpha, delay)` | Animates opacity by relative amount |
| `PositionX(target, duration, alpha, delay)` | Animates X position to target |
| `PositionXBy(relative, duration, alpha, delay)` | Animates X position by relative amount |
| `PositionY(target, duration, alpha, delay)` | Animates Y position to target |
| `PositionYBy(relative, duration, alpha, delay)` | Animates Y position by relative amount |
| `ScaleX(target, duration, alpha, delay)` | Animates X scale to target |
| `ScaleY(target, duration, alpha, delay)` | Animates Y scale to target |
| `BackgroundColor(target, duration, alpha, delay)` | Animates background color |
| `CornerRadius(target, duration, alpha, delay)` | Animates corner radius |
| `BorderlineWidth(target, duration, alpha, delay)` | Animates borderline width |

### Duration Literals

Use the user-defined literals for readable duration values:

```cpp
using namespace Dali::Ui;

Duration d1 = 300_ms;   // 0.3 seconds
Duration d2 = 1500_ms;  // 1.5 seconds
Duration d3 = 2_s;      // 2.0 seconds
```

### Complete Bridge Example

```cpp
void OnViewClicked(View view, InputEvent e)
{
  Animation anim = Animation::New();
  view.Animate(anim)
    .BackgroundColor(UiColor::PRIMARY, 500_ms)
    .PositionY(50.0f, 300_ms, AlphaFunction::EASE_OUT);
  anim.Play();
}
```

## Reusable Animation Specifications

The Spec pattern allows defining reusable animation configurations. Create a `ViewAnimationSpec` via `View::NewAnimationSpec()`, configure it, then apply to any `View`.

```cpp
// Define a reusable fade-in specification
ViewAnimationSpec fadeInSpec = View::NewAnimationSpec()
  .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);

// Apply to multiple views
Animation anim = Animation::New();
fadeInSpec.ApplyTo(anim, view1);
fadeInSpec.ApplyTo(anim, view2);
anim.Play();
```

### Spec-First Pattern Example

```cpp
class MyController : public ConnectionTracker
{
public:
  void Create(Application application)
  {
    // Build reusable spec once
    mFadeInSpec = View::NewAnimationSpec()
      .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);
  }

  void OnButtonClicked(View view, InputEvent e)
  {
    Animation anim = Animation::New();
    mFadeInSpec.ApplyTo(anim, view);
    anim.Play();
  }

private:
  ViewAnimationSpec mFadeInSpec;
};
```

## Alpha Functions

`AlphaFunction` controls the rate of change of animation progress over time. Built-in functions provide common easing curves.

### Built-in Alpha Functions

| Constant | Description |
|----------|-------------|
| `AlphaFunction::LINEAR` | No transformation, constant speed |
| `AlphaFunction::EASE_IN` | Speeds up, sudden stop (cubic) |
| `AlphaFunction::EASE_OUT` | Sudden start, slows to stop (cubic) |
| `AlphaFunction::EASE_IN_OUT` | Speeds up then slows down (cubic) |
| `AlphaFunction::EASE_IN_SINE` | Speeds up, sudden stop (sinusoidal) |
| `AlphaFunction::EASE_OUT_SINE` | Sudden start, slows to stop (sinusoidal) |
| `AlphaFunction::EASE_IN_OUT_SINE` | Speeds up then slows down (sinusoidal) |
| `AlphaFunction::SIN` | Single sine revolution |
| `AlphaFunction::BOUNCE` | Bounces at end |
| `AlphaFunction::EASE_OUT_BACK` | Exceeds end, returns to stop |
| `AlphaFunction::REVERSE` | Reverse linear |

### Using Alpha Functions

```cpp
// Set default alpha function for an animation
Animation anim = Animation::New(1.0f);
anim.SetDefaultAlphaFunction(AlphaFunction::EASE_IN_OUT);

// Per-animator alpha function
view.Animate(anim)
  .Opacity(0.0f, 300_ms, AlphaFunction::EASE_OUT);
```

### Bezier Curve Alpha Function

Create custom easing with a cubic Bezier curve:

```cpp
// Control points define the curve shape
AlphaFunction customEase(Vector2(0.25f, 0.1f), Vector2(0.25f, 1.0f));
```

### Spring Alpha Function

Use physics-based spring animation with predefined presets:

```cpp
// Spring presets
AlphaFunction spring(AlphaFunction::GENTLE);  // Slow, smooth
AlphaFunction spring(AlphaFunction::QUICK);   // Fast settling
AlphaFunction spring(AlphaFunction::BOUNCY);  // Highly oscillatory
AlphaFunction spring(AlphaFunction::SLOW);    // Relaxed motion
```

## Key Frame Animations

For complex animations with multiple intermediate values, use `KeyFrames` with `AnimateBetween()`.

### Creating Key Frames

```cpp
KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));
keyFrames.Add(0.25f, Property::Value(Vector4(0.0f, 0.0f, 0.5f, 0.5f)));
keyFrames.Add(0.50f, Property::Value(Vector4(0.25f, 0.25f, 0.5f, 0.5f)));
keyFrames.Add(1.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));
```

### Animating Between Key Frames

```cpp
Animation anim = Animation::New(3.0f);
anim.SetLooping(true);
anim.AnimateBetween(
  Property(view, propertyIndex),
  keyFrames,
  AlphaFunction::EASE_IN_OUT);
anim.Play();
```

### Interpolation Modes

`AnimateBetween()` supports two interpolation modes:

- `Animation::LINEAR`: Linear interpolation between key frames (default)
- `Animation::CUBIC`: Cubic interpolation for smoother curves

```cpp
anim.AnimateBetween(Property(view, propertyIndex), keyFrames,
                    AlphaFunction::EASE_IN_OUT,
                    Animation::CUBIC);
```

## Animation Control

### Playing Animations

```cpp
// Start animation
anim.Play();

// Play from specific progress (0.0 to 1.0)
anim.PlayFrom(0.5f);

// Play after delay
anim.PlayAfter(0.5f);
```

### Pausing and Stopping

```cpp
// Pause animation
anim.Pause();

// Stop animation (applies end action)
anim.Stop();

// Clear all animators
anim.Clear();
```

### Querying State

```cpp
Animation::State state = anim.GetState();
// Possible values: Animation::STOPPED, Animation::PLAYING, Animation::PAUSED
```

### Progress and Speed

```cpp
// Get current progress (0.0 to 1.0)
float progress = anim.GetCurrentProgress();

// Set progress manually
anim.SetCurrentProgress(0.5f);

// Speed factor (1.0 = normal, <1.0 = slower, >1.0 = faster, negative = reverse)
anim.SetSpeedFactor(2.0f);
float speed = anim.GetSpeedFactor();
```

### Play Range

Limit animation to a sub-range:

```cpp
// Play only from 25% to 75% of the animation
anim.SetPlayRange(Vector2(0.25f, 0.75f));
Vector2 range = anim.GetPlayRange();
```

## Looping and Repeat

### Basic Looping

```cpp
// Loop forever
anim.SetLooping(true);

// Loop specific number of times
anim.SetLoopCount(3);

// Check if looping
bool isLooping = anim.IsLooping();

// Get current loop count
int32_t currentLoop = anim.GetCurrentLoop();
```

### Looping Modes

```cpp
// Restart from beginning each loop (default)
anim.SetLoopingMode(Animation::RESTART);

// Reverse direction at each loop end
anim.SetLoopingMode(Animation::AUTO_REVERSE);
Animation::LoopingMode mode = anim.GetLoopingMode();
```

## Animation Lifecycle and Signals

### End Actions

Control what happens when an animation ends or is stopped:

| End Action | Behavior |
|------------|----------|
| `Animation::BAKE` | Save animated property values at end |
| `Animation::DISCARD` | Forget animated values, revert to original |
| `Animation::BAKE_FINAL` | Save values as if animation completed (on stop) |

```cpp
anim.SetEndAction(Animation::BAKE);
anim.SetDisconnectAction(Animation::BAKE_FINAL);
```

### Finished Signal

Connect to receive notification when animation completes:

```cpp
void OnAnimationFinished(Animation& anim)
{
  // Animation completed
}

// Connect signal
anim.FinishedSignal().Connect(this, &MyClass::OnAnimationFinished);
```

### Fire and Forget

Animations continue playing even if the handle is discarded. This enables "fire and forget" patterns:

```cpp
void FadeOut(View view)
{
  Animation anim = Animation::New();
  view.Animate(anim).Opacity(0.0f, 300_ms, AlphaFunction::EASE_OUT);
  anim.Play();
  // anim goes out of scope, but animation continues
}
```

### Blend Point

For `AnimateBetween()`, set a blend point to smoothly transition from the current value to the first key frame:

```cpp
anim.SetBlendPoint(0.2f);  // Blend during first 20% of animation
float blend = anim.GetBlendPoint();
```
