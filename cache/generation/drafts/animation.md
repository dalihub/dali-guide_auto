---
title: Animation
sidebar_label: Animation
category: animation-motion
---

# Animation

Animation in dali-ui provides a type-safe, fluent API for animating `View` properties. The framework offers two complementary patterns: the Bridge pattern for inline animations and the Spec pattern for reusable animation definitions.

## Table of Contents

- [Creating and Playing Animations](#creating-and-playing-animations)
- [View-First Bridge Pattern](#view-first-bridge-pattern)
- [Spec-First Reusable Animations](#spec-first-reusable-animations)
- [Alpha Functions](#alpha-functions)
- [Key Frame Animations](#key-frame-animations)
- [Animation Control](#animation-control)
- [Looping and Speed](#looping-and-speed)
- [Animation Signals](#animation-signals)

## Creating and Playing Animations

Create an `Animation` using `Animation::New()` with an optional duration parameter. Call `Play()` to start the animation.

```cpp
// Create animation with default duration
auto anim = Animation::New();

// Create animation with specific duration (3 seconds)
auto anim = Animation::New(3.0f);

// Start the animation
anim.Play();
```

The animation duration is automatically extended if any individual animator exceeds it. Use `SetDuration()` to modify the duration after creation.

```cpp
anim.SetDuration(2.0f);
float duration = anim.GetDuration();
```

## View-First Bridge Pattern

The Bridge pattern provides a fluent, type-safe API for animating `View` properties. Call `Animate()` on a `View` with an `Animation` instance to get a `ViewAnimationBridge`, then chain property animations.

```cpp
auto anim = Animation::New();
view.Animate(anim)
  .Opacity(0.0f, 300_ms, AlphaFunction::EASE_OUT)
  .PositionY(50.0f, 200_ms);

anim.Play();
```

The `ViewAnimationBridge` is stack-allocated with zero heap overhead. Each method accepts the target value, a `Duration`, an optional `AlphaFunction`, and an optional delay.

### Available Bridge Methods

| Method | Description |
|--------|-------------|
| `Opacity(target, duration, alpha, delay)` | Animates opacity to target value |
| `OpacityBy(relative, duration, alpha, delay)` | Animates opacity by relative amount |
| `PositionX(target, duration, alpha, delay)` | Animates X position |
| `PositionY(target, duration, alpha, delay)` | Animates Y position |
| `ScaleX(target, duration, alpha, delay)` | Animates X scale |
| `ScaleY(target, duration, alpha, delay)` | Animates Y scale |
| `BackgroundColor(target, duration, alpha, delay)` | Animates background color |
| `CornerRadius(target, duration, alpha, delay)` | Animates corner radius |
| `BorderlineWidth(target, duration, alpha, delay)` | Animates borderline width |

### Chaining Multiple Properties

Multiple properties can be animated in parallel within the same `Animation`:

```cpp
auto anim = Animation::New();
view.Animate(anim)
  .Opacity(0.0f, 500_ms)
  .ScaleX(1.2f, 500_ms, AlphaFunction::EASE_OUT)
  .ScaleY(1.2f, 500_ms, AlphaFunction::EASE_OUT)
  .PositionY(-20.0f, 250_ms);

anim.Play();
```

## Spec-First Reusable Animations

The Spec pattern creates reusable animation definitions that can be applied to multiple Views. Use `View::NewAnimationSpec()` to create a `ViewAnimationSpec`, then call `ApplyTo()` with an `Animation` and `View`.

```cpp
// Create a reusable fade-in spec
auto fadeInSpec = View::NewAnimationSpec()
  .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);

// Apply to multiple views
auto anim1 = Animation::New();
fadeInSpec.ApplyTo(anim1, view1);
anim1.Play();

auto anim2 = Animation::New();
fadeInSpec.ApplyTo(anim2, view2);
anim2.Play();
```

This pattern is useful for defining animations once and reusing them across different Views or throughout an application.

## Alpha Functions

Alpha functions define the rate of change of animation progress over time. Use built-in functions or create custom curves.

### Built-in Alpha Functions

```cpp
AlphaFunction::LINEAR           // No transformation
AlphaFunction::EASE_IN          // Speeds up, sudden stop
AlphaFunction::EASE_OUT         // Sudden start, slows down
AlphaFunction::EASE_IN_OUT      // Speeds up, then slows down
AlphaFunction::EASE_OUT_BACK    // Exceeds end, returns
AlphaFunction::BOUNCE           // Bounces back to start
AlphaFunction::SIN             // Single sine revolution
```

Set a default alpha function for an animation:

```cpp
anim.SetDefaultAlphaFunction(AlphaFunction::EASE_IN_OUT);
AlphaFunction alpha = anim.GetDefaultAlphaFunction();
```

### Spring Animations

Spring animations provide physics-based motion with natural settling behavior:

```cpp
// Predefined spring types
AlphaFunction spring(AlphaFunction::GENTLE);   // Slow, smooth
AlphaFunction spring(AlphaFunction::QUICK);     // Fast settling
AlphaFunction spring(AlphaFunction::BOUNCY);   // Elastic, oscillatory
AlphaFunction spring(AlphaFunction::SLOW);     // Relaxed, longer settling

// Apply to animation
view.Animate(anim).Opacity(1.0f, 500_ms, AlphaFunction::BOUNCY);
```

### Custom Bezier Curves

Create custom easing curves with bezier control points:

```cpp
// Control points define the curve shape
AlphaFunction custom(Vector2(0.25f, 0.1f), Vector2(0.25f, 1.0f));
```

## Key Frame Animations

Use `KeyFrames` with `AnimateBetween()` for complex multi-step animations. Add key frames with progress values between 0.0 and 1.0.

```cpp
KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));
keyFrames.Add(0.25f, Property::Value(Vector4(0.0f, 0.0f, 0.5f, 0.5f)));
keyFrames.Add(0.50f, Property::Value(Vector4(0.25f, 0.25f, 0.5f, 0.5f)));
keyFrames.Add(0.75f, Property::Value(Vector4(0.5f, 0.5f, 0.5f, 0.5f)));
keyFrames.Add(1.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));

Animation anim = Animation::New(3.0f);
anim.AnimateBetween(Property(view, View::Property::CORNER_RADIUS), keyFrames, AlphaFunction::EASE_IN_OUT);
anim.Play();
```

### Interpolation Modes

`AnimateBetween()` supports linear or cubic interpolation:

```cpp
// Linear interpolation (default)
anim.AnimateBetween(target, keyFrames, Animation::LINEAR);

// Cubic interpolation for smoother curves
anim.AnimateBetween(target, keyFrames, Animation::CUBIC);
```

## Animation Control

Control animation playback with `Play()`, `Pause()`, and `Stop()`.

```cpp
// Start or resume
anim.Play();

// Play from specific progress (0.0 to 1.0)
anim.PlayFrom(0.5f);

// Play after delay
anim.PlayAfter(1.0f);  // 1 second delay

// Pause
anim.Pause();

// Stop (applies end action)
anim.Stop();

// Clear all animators
anim.Clear();
```

Query the current state:

```cpp
Animation::State state = anim.GetState();
// Returns: Animation::STOPPED, Animation::PLAYING, or Animation::PAUSED
```

### Progress Control

Set or get the current animation progress:

```cpp
anim.SetCurrentProgress(0.5f);
float progress = anim.GetCurrentProgress();
```

### Play Range

Limit animation to a specific progress range:

```cpp
anim.SetPlayRange(Vector2(0.25f, 0.75f));  // Play middle half only
Vector2 range = anim.GetPlayRange();
```

## Looping and Speed

### Looping

Enable continuous looping or set a specific loop count:

```cpp
// Loop forever
anim.SetLooping(true);

// Loop specific number of times
anim.SetLoopCount(3);

int32_t loopCount = anim.GetLoopCount();
int32_t currentLoop = anim.GetCurrentLoop();
bool isLooping = anim.IsLooping();
```

### Looping Mode

Control how looping behaves at boundaries:

```cpp
// Restart from beginning (default)
anim.SetLoopingMode(Animation::RESTART);

// Reverse direction at end
anim.SetLoopingMode(Animation::AUTO_REVERSE);

Animation::LoopingMode mode = anim.GetLoopingMode();
```

### Speed Factor

Adjust animation speed with a multiplier:

```cpp
anim.SetSpeedFactor(2.0f);  // 2x speed
anim.SetSpeedFactor(0.5f);  // Half speed
anim.SetSpeedFactor(-1.0f); // Reverse

float factor = anim.GetSpeedFactor();
```

## Animation Signals

Connect to `FinishedSignal()` to be notified when an animation completes.

```cpp
anim.FinishedSignal().Connect(this, &MyClass::OnAnimationFinished);

// Handler signature
void OnAnimationFinished(Animation& animation)
{
  // Animation completed
}
```

### End Actions

Control what happens when an animation ends or is stopped:

```cpp
// Save final property values (default)
anim.SetEndAction(Animation::BAKE);

// Discard animated values, revert to original
anim.SetEndAction(Animation::DISCARD);

// Save values as if animation ran to completion
anim.SetEndAction(Animation::BAKE_FINAL);

Animation::EndAction action = anim.GetEndAction();
```

### Disconnect Action

Handle the case where an animated View is removed from the scene:

```cpp
anim.SetDisconnectAction(Animation::BAKE_FINAL);  // Default
Animation::EndAction action = anim.GetDisconnectAction();
```

### Blend Point

For `AnimateBetween()`, set a blend point to smoothly transition from the current value to the first key frame:

```cpp
anim.SetBlendPoint(0.1f);  // Blend during first 10% of animation
float blendPoint = anim.GetBlendPoint();
```
