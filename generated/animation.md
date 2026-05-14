---
title: Animation
sidebar_label: Animation
category: animation-motion
---

# Animation

The `Animation` API provides property-based animation for `Dali::Ui::View` objects. You can animate transform properties, colors, and custom animatable properties using a fluent API with support for alpha functions, key frames, and looping behaviors.

## Table of Contents

- [Creating an Animation](#creating-an-animation)
- [Animating Properties](#animating-properties)
- [Alpha Functions](#alpha-functions)
- [Key Frame Animation](#key-frame-animation)
- [Playback Control](#playback-control)
- [Looping and Speed](#looping-and-speed)
- [Animation Signals](#animation-signals)
- [View-First Animation Pattern](#view-first-animation-pattern)

## Creating an Animation

Create an `Animation` instance using `Animation::New()` with a duration in seconds:

```cpp
// Create a 500ms animation
Animation anim = Animation::New(0.5f);
```

You can also create an animation without a fixed duration:

```cpp
// Create an animation; duration extends as animations are added
Animation anim = Animation::New();
```

The duration can be modified after creation using `SetDuration()`:

```cpp
anim.SetDuration(1.0f); // 1 second
```

## Animating Properties

### AnimateTo

Use `AnimateTo()` to animate a property to a target value:

```cpp
Animation anim = Animation::New(0.3f);

// Animate opacity to 0.0
anim.AnimateTo(Property(view, Actor::Property::OPACITY), 0.0f);

// Animate position
anim.AnimateTo(Property(view, Actor::Property::POSITION), Vector3(100.0f, 50.0f, 0.0f));

// Animate scale
anim.AnimateTo(Property(view, Actor::Property::SCALE), Vector3(1.5f, 1.5f, 1.0f));

anim.Play();
```

### AnimateBy

Use `AnimateBy()` to animate a property by a relative offset:

```cpp
Animation anim = Animation::New(0.3f);

// Move view by 50 pixels in Y direction
anim.AnimateBy(Property(view, Actor::Property::POSITION), Vector3(0.0f, 50.0f, 0.0f));

anim.Play();
```

### Time Period

Specify delay and duration using `TimePeriod`:

```cpp
// Animate over 300ms starting after 100ms delay
anim.AnimateTo(
    Property(view, Actor::Property::OPACITY),
    0.0f,
    TimePeriod(0.1f, 0.3f)); // TimePeriod(delaySeconds, durationSeconds)
```

## Alpha Functions

Alpha functions define the interpolation curve. Set a default alpha function for the animation or specify per-animation:

```cpp
// Set default alpha function for the animation
anim.SetDefaultAlphaFunction(AlphaFunction::EASE_IN_OUT);

// Or specify alpha function per AnimateTo call
anim.AnimateTo(
    Property(view, Actor::Property::POSITION_Y),
    100.0f,
    AlphaFunction::EASE_OUT,
    TimePeriod(0.3f));
```

### Built-in Alpha Functions

Common alpha functions include:

- `AlphaFunction::LINEAR` — Constant speed
- `AlphaFunction::EASE_IN` — Slow start
- `AlphaFunction::EASE_OUT` — Slow end
- `AlphaFunction::EASE_IN_OUT` — Slow start and end
- `AlphaFunction::BOUNCE` — Bouncing effect
- `AlphaFunction::SIN` — Sinusoidal easing

### Custom Alpha Functions

Create custom alpha functions with bezier control points:

```cpp
// Custom bezier curve with two control points
AlphaFunction customAlpha(Vector2(0.2f, 0.8f), Vector2(0.3f, 1.0f));
anim.SetDefaultAlphaFunction(customAlpha);
```

## Key Frame Animation

Use `KeyFrames` to define multiple key points for complex animations:

```cpp
KeyFrames keyFrames = KeyFrames::New();

// Add key frames at progress values (0.0 to 1.0)
keyFrames.Add(0.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));
keyFrames.Add(0.25f, Property::Value(Vector4(0.0f, 0.0f, 0.5f, 0.5f)));
keyFrames.Add(0.50f, Property::Value(Vector4(0.25f, 0.25f, 0.5f, 0.5f)));
keyFrames.Add(0.75f, Property::Value(Vector4(0.5f, 0.5f, 0.5f, 0.5f)));
keyFrames.Add(1.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));

Animation anim = Animation::New(3.0f);
anim.SetLooping(true);
anim.AnimateBetween(Property(view, Actor::Property::COLOR), keyFrames, AlphaFunction::EASE_IN_OUT);
anim.Play();
```

## Playback Control

Control animation playback with `Play()`, `Pause()`, and `Stop()`:

```cpp
Animation anim = Animation::New(0.5f);
anim.AnimateTo(Property(view, Actor::Property::OPACITY), 0.0f);

// Start playback
anim.Play();

// Pause playback
anim.Pause();

// Stop and reset to start
anim.Stop();
```

### Delayed Playback

Start playback after a delay using `PlayAfter()`:

```cpp
// Start animation after 0.5 second delay
anim.PlayAfter(0.5f);
```

### Playback from Progress

Start from a specific progress point using `PlayFrom()`:

```cpp
// Start from 50% progress
anim.PlayFrom(0.5f);
```

### Current Progress

Query or set the current progress:

```cpp
float progress = anim.GetCurrentProgress();
anim.SetCurrentProgress(0.25f);
```

### Animation State

Check the current state using `GetState()`:

```cpp
if (anim.GetState() == Animation::PLAYING)
{
    // Animation is currently playing
}
```

## Looping and Speed

### Loop Count

Set the number of loops using `SetLoopCount()`:

```cpp
// Loop 3 times
anim.SetLoopCount(3);

// Loop indefinitely
anim.SetLooping(true);
```

Query the current loop:

```cpp
int32_t currentLoop = anim.GetCurrentLoop();
```

### Looping Mode

Control how looping behaves using `SetLoopingMode()`:

```cpp
// Restart from beginning each loop
anim.SetLoopingMode(Animation::RESTART);

// Alternate direction each loop
anim.SetLoopingMode(Animation::AUTO_REVERSE);
```

### Speed Factor

Adjust playback speed using `SetSpeedFactor()`:

```cpp
// Play at 2x speed
anim.SetSpeedFactor(2.0f);

// Play at half speed
anim.SetSpeedFactor(0.5f);
```

### Play Range

Limit playback to a subrange using `SetPlayRange()`:

```cpp
// Play only from 25% to 75% of the animation
anim.SetPlayRange(Vector2(0.25f, 0.75f));
```

## Animation Signals

Connect to `FinishedSignal()` to be notified when an animation completes:

```cpp
anim.FinishedSignal().Connect(this, &MyClass::OnAnimationFinished);

void OnAnimationFinished(Animation anim)
{
    // Animation completed
}
```

### End Action

Control what happens when the animation finishes using `SetEndAction()`:

```cpp
// Save the animated property values when animation ends (default)
anim.SetEndAction(Animation::BAKE);

// Discard the animated property values when animation ends
anim.SetEndAction(Animation::DISCARD);

// If stopped, save values as if animation ran to completion
anim.SetEndAction(Animation::BAKE_FINAL);
```

### Disconnect Action

Control behavior when the animation is disconnected:

```cpp
anim.SetDisconnectAction(Animation::BAKE);
```

## View-First Animation Pattern

The dali-ui framework provides a fluent, View-centric animation API using `ViewAnimationBridge` and `ViewAnimationSpec`.

### Bridge Pattern

Use `View::Animate()` to get a bridge for chaining animations:

```cpp
Animation anim = Animation::New();
view.Animate(anim)
    .Opacity(0.0f, 500_ms)
    .PositionY(100.0f, 300_ms, AlphaFunction::EASE_OUT);
anim.Play();
```

### Spec Pattern

Create reusable animation specs with `View::NewAnimationSpec()`:

```cpp
// Build a reusable fade-in spec
ViewAnimationSpec fadeInSpec = View::NewAnimationSpec()
    .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);

// Apply to any view
Animation anim = Animation::New();
fadeInSpec.ApplyTo(anim, view);
anim.Play();
```

The spec pattern enables animation reuse across multiple views and promotes separation of animation definition from application.

### Duration Literal

Use the `_ms` and `_s` literals for readable duration values:

```cpp
using namespace Dali::Ui;

view.Animate(anim)
    .Opacity(1.0f, 500_ms)      // 500 milliseconds
    .PositionX(50.0f, 300_ms);  // 300 milliseconds
```
