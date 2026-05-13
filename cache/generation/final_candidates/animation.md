---
title: Animation
sidebar_label: Animation
category: animation-motion
---

# Animation

Animation in dali-ui provides a type-safe, fluent API for animating `View` properties. The framework offers two complementary patterns: a View-first Bridge pattern for immediate animations and a Spec-first pattern for reusable animation definitions.

## Table of Contents

- [Creating Animations](#creating-animations)
- [View-First Bridge Pattern](#view-first-bridge-pattern)
- [Spec-First Reusable Animations](#spec-first-reusable-animations)
- [Core Animation API](#core-animation-api)
- [Alpha Functions](#alpha-functions)
- [Key Frame Animations](#key-frame-animations)
- [Animation Control](#animation-control)
- [Animation Signals](#animation-signals)

## Creating Animations

Create an `Animation` instance using `Animation::New()`. The animation duration can be specified at creation time or set later.

```cpp
// Create with duration
Animation animation = Animation::New(2.0f); // 2 seconds

// Create without duration (set later)
Animation animation = Animation::New();
animation.SetDuration(1.5f);
```

## View-First Bridge Pattern

The Bridge pattern provides a stack-allocated, zero-heap fluent API for animating `View` properties. Call `View::Animate()` with an `Animation` instance, then chain property animations.

### Basic Usage

```cpp
auto anim = Animation::New();
view.Animate(anim)
  .Opacity(0.0f, 300_ms, AlphaFunction::EASE_OUT)
  .PositionY(50.0f, 300_ms);
anim.Play();
```

### Available Properties

The `ViewAnimationBridge` supports the following animatable properties:

| Property | Method | Relative Method |
|----------|--------|-----------------|
| Opacity | `Opacity(float target, Duration duration, ...)` | `OpacityBy(float relative, Duration duration, ...)` |
| Position X | `PositionX(float target, Duration duration, ...)` | `PositionXBy(float relative, Duration duration, ...)` |
| Position Y | `PositionY(float target, Duration duration, ...)` | `PositionYBy(float relative, Duration duration, ...)` |
| Scale X | `ScaleX(float target, Duration duration, ...)` | `ScaleXBy(float relative, Duration duration, ...)` |
| Scale Y | `ScaleY(float target, Duration duration, ...)` | `ScaleYBy(float relative, Duration duration, ...)` |
| Background Color | `BackgroundColor(const UiColor& target, Duration duration, ...)` | `BackgroundColorBy(const UiColor& relative, Duration duration, ...)` |
| Corner Radius | `CornerRadius(const Vector4& target, Duration duration, ...)` | `CornerRadiusBy(const Vector4& relative, Duration duration, ...)` |
| Borderline Width | `BorderlineWidth(float target, Duration duration, ...)` | `BorderlineWidthBy(float relative, Duration duration, ...)` |
| Borderline Color | `BorderlineColor(const UiColor& target, Duration duration, ...)` | `BorderlineColorBy(const UiColor& relative, Duration duration, ...)` |
| Borderline Offset | `BorderlineOffset(float target, Duration duration, ...)` | `BorderlineOffsetBy(float relative, Duration duration, ...)` |

### Chaining Multiple Properties

```cpp
auto anim = Animation::New();
view.Animate(anim)
  .BackgroundColor(UiColor::PRIMARY, 500_ms)
  .PositionY(50.0f, 300_ms, AlphaFunction::EASE_OUT)
  .Opacity(0.8f, 400_ms, AlphaFunction::EASE_IN_OUT, 100_ms); // 100ms delay
anim.Play();
```

### Duration Extension

The Bridge automatically extends the animation duration if any chained animation exceeds the current duration:

```cpp
auto anim = Animation::New(); // Initial duration is 0
view.Animate(anim)
  .Opacity(0.0f, 500_ms); // Duration automatically set to 0.5s
anim.Play();
```

## Spec-First Reusable Animations

`ViewAnimationSpec` allows defining reusable animation specifications that can be applied to multiple Views or stored for later use.

### Creating a Spec

```cpp
// Create a reusable fade-in spec
auto fadeInSpec = View::NewAnimationSpec()
  .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);
```

### Applying a Spec

```cpp
Animation anim = Animation::New();
fadeInSpec.ApplyTo(anim, view);
anim.Play();
```

### Storing Specs for Reuse

```cpp
class MyController : public ConnectionTracker
{
public:
  void OnInit(Application application)
  {
    // Build reusable specs during initialization
    mFadeInSpec = View::NewAnimationSpec()
      .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);

    mSlideInSpec = View::NewAnimationSpec()
      .PositionX(0.0f, 300_ms, AlphaFunction::EASE_OUT)
      .Opacity(1.0f, 300_ms);
  }

  void AnimateView(View view)
  {
    auto anim = Animation::New();
    mSlideInSpec.ApplyTo(anim, view);
    anim.Play();
  }

private:
  ViewAnimationSpec mFadeInSpec;
  ViewAnimationSpec mSlideInSpec;
};
```

## Core Animation API

### Animating to a Target Value

Use `AnimateTo()` to animate a property to a specific target value:

```cpp
Animation anim = Animation::New(1.0f);
anim.AnimateTo(Property(view, Actor::Property::OPACITY), 0.0f, AlphaFunction::EASE_OUT);
anim.Play();
```

### Animating by a Relative Value

Use `AnimateBy()` to animate a property by a relative amount:

```cpp
Animation anim = Animation::New(0.5f);
anim.AnimateBy(Property(view, Actor::Property::POSITION), Vector3(100.0f, 0.0f, 0.0f));
anim.Play();
```

### Using TimePeriod

`TimePeriod` allows precise control over when an animation starts and how long it runs:

```cpp
Animation anim = Animation::New(2.0f);

// Animate opacity from 0s to 1s
anim.AnimateTo(
  Property(view, Actor::Property::OPACITY),
  0.0f,
  TimePeriod(0.0f, 1.0f)
);

// Animate position from 0.5s to 1.5s
anim.AnimateTo(
  Property(view, Actor::Property::POSITION),
  Vector3(100.0f, 50.0f, 0.0f),
  AlphaFunction::EASE_OUT,
  TimePeriod(0.5f, 1.0f)
);

anim.Play();
```

## Alpha Functions

Alpha functions define the rate of change of an animation parameter over time. Use `AlphaFunction` to control easing behavior.

### Built-in Alpha Functions

```cpp
AlphaFunction::LINEAR           // No transformation
AlphaFunction::EASE_IN           // Speeds up, sudden stop (cubic)
AlphaFunction::EASE_OUT          // Sudden start, slows to stop (cubic)
AlphaFunction::EASE_IN_OUT       // Speeds up and slows down (cubic)
AlphaFunction::EASE_IN_SQUARE    // Speeds up, sudden stop (square)
AlphaFunction::EASE_OUT_SQUARE  // Sudden start, slows to stop (square)
AlphaFunction::EASE_IN_SINE      // Speeds up, sudden stop (sinusoidal)
AlphaFunction::EASE_OUT_SINE     // Sudden start, slows to stop (sinusoidal)
AlphaFunction::EASE_IN_OUT_SINE  // Speeds up and slows down (sinusoidal)
AlphaFunction::BOUNCE            // Bounces back to start
AlphaFunction::SIN               // Single revolution
AlphaFunction::EASE_OUT_BACK     // Exceeds end, returns to stop
AlphaFunction::REVERSE           // Reverse linear
```

### Using Alpha Functions

```cpp
// With Bridge pattern
view.Animate(anim)
  .Opacity(0.0f, 300_ms, AlphaFunction::EASE_IN_OUT);

// With core API
anim.AnimateTo(
  Property(view, Actor::Property::OPACITY),
  0.0f,
  AlphaFunction::EASE_OUT
);
```

### Bezier Curve Alpha Functions

Create custom easing curves with Bezier control points:

```cpp
// Custom bezier curve (control points as Vector2)
AlphaFunction customBezier(
  Vector2(0.25f, 0.1f),  // First control point
  Vector2(0.25f, 1.0f)   // Second control point
);

anim.AnimateTo(Property(view, Actor::Property::OPACITY), 0.0f, customBezier);
```

### Spring-based Alpha Functions

Use physics-based spring animations for natural motion:

```cpp
// Predefined spring types
AlphaFunction spring(AlphaFunction::GENTLE);  // Slow, smooth, less oscillation
AlphaFunction spring(AlphaFunction::QUICK);   // Fast settling, minimal overshoot
AlphaFunction spring(AlphaFunction::BOUNCY);  // Highly elastic, oscillatory
AlphaFunction spring(AlphaFunction::SLOW);    // Smooth, relaxed, longer settling

// Custom spring parameters
SpringData springData(stiffness, damping, mass);
AlphaFunction customSpring(springData);
```

## Key Frame Animations

`KeyFrames` allows defining multiple time-value pairs for complex animation paths.

### Creating Key Frames

```cpp
KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.0f, Property::Value(1.0f));  // Start at full opacity
keyFrames.Add(0.5f, Property::Value(0.0f));  // Fade to transparent
keyFrames.Add(1.0f, Property::Value(1.0f));  // Back to full opacity
```

### Animating with Key Frames

```cpp
Animation anim = Animation::New(3.0f);
anim.SetLooping(true);
anim.AnimateBetween(
  Property(view, Actor::Property::OPACITY),
  keyFrames,
  AlphaFunction::EASE_IN_OUT
);
anim.Play();
```

### Interpolation Methods

Key frame animations support two interpolation methods:

```cpp
Animation::LINEAR  // Linear interpolation between key frames (default)
Animation::CUBIC   // Cubic interpolation for smoother curves

// Using cubic interpolation
anim.AnimateBetween(
  Property(view, Actor::Property::POSITION),
  keyFrames,
  AlphaFunction::EASE_IN_OUT,
  Animation::CUBIC
);
```

## Animation Control

### Playing Animations

```cpp
// Start playback
animation.Play();

// Play from a specific progress (0.0 to 1.0)
animation.PlayFrom(0.5f);

// Play after a delay
animation.PlayAfter(1.0f); // 1 second delay
```

### Pausing and Stopping

```cpp
// Pause the animation
animation.Pause();

// Stop the animation (applies end action)
animation.Stop();

// Clear all animators
animation.Clear();
```

### Querying State

```cpp
Animation::State state = animation.GetState();
// Possible values: Animation::STOPPED, Animation::PLAYING, Animation::PAUSED

float progress = animation.GetCurrentProgress();
int32_t loopCount = animation.GetCurrentLoop();
```

### Looping

```cpp
// Loop forever
animation.SetLooping(true);

// Loop specific number of times
animation.SetLoopCount(3);

// Check if looping
bool isLooping = animation.IsLooping();

// Set looping mode
animation.SetLoopingMode(Animation::RESTART);     // Restart from beginning
animation.SetLoopingMode(Animation::AUTO_REVERSE); // Reverse direction at end
```

### Speed Control

```cpp
// Set speed factor (multiplier)
animation.SetSpeedFactor(2.0f);  // 2x speed
animation.SetSpeedFactor(0.5f);  // Half speed
animation.SetSpeedFactor(-1.0f); // Reverse

float speedFactor = animation.GetSpeedFactor();
```

### Play Range

Limit animation to a specific progress range:

```cpp
// Play only from 25% to 75% of the animation
animation.SetPlayRange(Vector2(0.25f, 0.75f));

Vector2 range = animation.GetPlayRange();
```

### End Actions

Control what happens when an animation ends:

```cpp
Animation::BAKE       // Save animated property values at end
Animation::DISCARD    // Forget animated property values at end
Animation::BAKE_FINAL // Save as if animation completed (even if stopped)

animation.SetEndAction(Animation::BAKE);
animation.SetDisconnectAction(Animation::BAKE_FINAL);
```

## Animation Signals

Connect to `FinishedSignal()` to be notified when an animation completes:

```cpp
void OnAnimationFinished(Animation& animation)
{
  // Animation completed
}

animation.FinishedSignal().Connect(this, &MyClass::OnAnimationFinished);
```

### Signal with Lambda

```cpp
animation.FinishedSignal().Connect([](Animation& anim) {
  // Handle animation completion
});
```

### Disconnecting Signals

When connecting to a member function, disconnect before the object is destroyed:

```cpp
class MyController : public ConnectionTracker
{
public:
  void StartAnimation()
  {
    mAnimation = Animation::New(1.0f);
    mAnimation.FinishedSignal().Connect(this, &MyController::OnAnimationFinished);
    mAnimation.Play();
  }

  ~MyController()
  {
    // Signal automatically disconnected by ConnectionTracker
  }

  void OnAnimationFinished(Animation& animation)
  {
    // Handle completion
  }

private:
  Animation mAnimation;
};
