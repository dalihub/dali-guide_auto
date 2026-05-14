---
title: Animation
sidebar_label: Animation
category: animation-motion
---

# Animation

Animation in dali-ui provides a type-safe, fluent API for animating `View` properties. The animation system supports property transitions, key-frame animations, and reusable animation specifications.

## Table of Contents

- [Creating and Playing Animations](#creating-and-playing-animations)
- [View-First Animation with Bridge Pattern](#view-first-animation-with-bridge-pattern)
- [Reusable Animation Specifications](#reusable-animation-specifications)
- [Alpha Functions](#alpha-functions)
- [Key-Frame Animations](#key-frame-animations)
- [Animation Control](#animation-control)
- [Looping and Playback Range](#looping-and-playback-range)
- [Animation Signals](#animation-signals)

## Creating and Playing Animations

Create an `Animation` instance using `Animation::New()`, configure it, and call `Play()` to start the animation.

```cpp
// Create a 2-second animation
Animation animation = Animation::New(2.0f);

// Configure and play
animation.SetDefaultAlphaFunction(AlphaFunction::EASE_IN_OUT);
animation.Play();
```


## View-First Animation with Bridge Pattern

The preferred approach in dali-ui is to use `View::Animate()` which returns a `ViewAnimationBridge`. This provides a type-safe, fluent chaining API for animating View properties.

```cpp
// Create an animation and apply it to a View
Animation anim = Animation::New();
view.Animate(anim)
  .Opacity(0.0f, 500_ms, AlphaFunction::EASE_OUT)
  .PositionY(100.0f, 300_ms);

anim.Play();
```

The `ViewAnimationBridge` supports animating various View properties:

```cpp
view.Animate(anim)
  .BackgroundColor(UiColor::PRIMARY, 500_ms)
  .Opacity(0.5f, 300_ms, AlphaFunction::EASE_IN_OUT)
  .PositionX(50.0f, 200_ms)
  .PositionY(100.0f, 400_ms)
  .ScaleX(1.5f, 300_ms)
  .ScaleY(1.5f, 300_ms)
  .CornerRadius(Vector4(10.0f, 10.0f, 10.0f, 10.0f), 500_ms);
```

Use the `*By` variants to animate by relative amounts:

```cpp
view.Animate(anim)
  .OpacityBy(-0.3f, 300_ms)      // Decrease opacity by 0.3
  .PositionXBy(50.0f, 200_ms);    // Move right by 50 pixels
```

## Reusable Animation Specifications

For animations that need to be reused across multiple Views, create a `ViewAnimationSpec` using `View::NewAnimationSpec()`.

```cpp
// Create a reusable fade-in specification
ViewAnimationSpec fadeInSpec = View::NewAnimationSpec()
  .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);

// Apply to any View with any Animation
Animation anim = Animation::New();
fadeInSpec.ApplyTo(anim, view);
anim.Play();
```

This pattern is useful for defining animation presets that can be applied consistently throughout your application.

## Alpha Functions

Alpha functions define the rate of change of an animation parameter over time. Use `AlphaFunction` to specify easing behavior.

### Built-in Alpha Functions

```cpp
AlphaFunction::LINEAR           // No transformation
AlphaFunction::EASE_IN          // Speeds up and stops suddenly
AlphaFunction::EASE_OUT         // Sudden start, slows to gradual stop
AlphaFunction::EASE_IN_OUT      // Speeds up and slows down
AlphaFunction::EASE_IN_SQUARE   // Square easing
AlphaFunction::EASE_OUT_SQUARE  // Square easing
AlphaFunction::EASE_IN_SINE     // Sinusoidal easing
AlphaFunction::EASE_OUT_SINE    // Sinusoidal easing
AlphaFunction::EASE_IN_OUT_SINE // Sinusoidal easing
AlphaFunction::BOUNCE           // Bouncing effect
AlphaFunction::EASE_OUT_BACK     // Exceeds end position then returns
```

### Setting the Default Alpha Function

Set a default alpha function for all animations in an `Animation` instance:

```cpp
Animation animation = Animation::New(1.0f);
animation.SetDefaultAlphaFunction(AlphaFunction::EASE_IN_OUT);
```

### Custom Bezier Curves

Create custom easing curves with bezier control points:

```cpp
// Control points for a custom curve
AlphaFunction customAlpha(Vector2(0.25f, 0.1f), Vector2(0.25f, 1.0f));
```

### Spring Animations

Use spring-based physics animations for natural motion:

```cpp
// Predefined spring types
AlphaFunction spring(AlphaFunction::GENTLE);  // Slow, smooth motion
AlphaFunction spring(AlphaFunction::QUICK);   // Fast settling
AlphaFunction spring(AlphaFunction::BOUNCY); // Highly elastic
AlphaFunction spring(AlphaFunction::SLOW);    // Relaxed motion
```

## Key-Frame Animations

Use `KeyFrames` to define complex animations with multiple values at specific progress points.

```cpp
KeyFrames keyFrames = KeyFrames::New();
keyFrames.Add(0.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));
keyFrames.Add(0.25f, Property::Value(Vector4(0.0f, 0.0f, 0.5f, 0.5f)));
keyFrames.Add(0.50f, Property::Value(Vector4(0.25f, 0.25f, 0.5f, 0.5f)));
keyFrames.Add(0.75f, Property::Value(Vector4(0.5f, 0.5f, 0.5f, 0.5f)));
keyFrames.Add(1.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));

Animation animation = Animation::New(3.0f);
animation.AnimateBetween(Property(view, View::Property::CORNER_RADIUS), keyFrames, AlphaFunction::EASE_IN_OUT);
animation.Play();
```

For animating properties inherited from `Actor`, use the `Actor::Property` indices:

```cpp
Animation animation = Animation::New(1.0f);
animation.AnimateTo(Property(view, Actor::Property::OPACITY), 0.0f, AlphaFunction::EASE_OUT);
animation.AnimateTo(Property(view, Actor::Property::SCALE), Vector3(1.5f, 1.5f, 1.0f));
animation.Play();
```

## Animation Control

### Play, Pause, and Stop

Control animation playback with `Play()`, `Pause()`, and `Stop()`:

```cpp
Animation animation = Animation::New(2.0f);

animation.Play();   // Start or resume the animation
animation.Pause();  // Pause the animation
animation.Stop();   // Stop and apply end action
```

### Play from Specific Progress

Start an animation from a specific progress point:

```cpp
animation.PlayFrom(0.5f);  // Start from 50% progress
```

### Play After Delay

Delay the start of an animation:

```cpp
animation.PlayAfter(0.5f);  // Start after 500ms delay
```

### Query Animation State

Check the current state of an animation:

```cpp
if(animation.GetState() == Animation::PLAYING)
{
  // Animation is currently playing
}
```

The `State` enum values are:
- `Animation::STOPPED`
- `Animation::PLAYING`
- `Animation::PAUSED`

### Current Progress

Get or set the current progress:

```cpp
float progress = animation.GetCurrentProgress();
animation.SetCurrentProgress(0.25f);
```

### Speed Factor

Adjust the playback speed:

```cpp
animation.SetSpeedFactor(2.0f);  // Double speed
animation.SetSpeedFactor(0.5f);  // Half speed
animation.SetSpeedFactor(-1.0f); // Play in reverse
```

## Looping and Playback Range

### Looping

Enable continuous looping:

```cpp
animation.SetLooping(true);
```

Or set a specific loop count:

```cpp
animation.SetLoopCount(3);  // Play 3 times
animation.SetLoopCount(0);  // Loop forever (same as SetLooping(true))
```

Query the current loop:

```cpp
int32_t currentLoop = animation.GetCurrentLoop();
```

### Looping Mode

Control how looping behaves:

```cpp
animation.SetLoopingMode(Animation::RESTART);      // Restart from beginning
animation.SetLoopingMode(Animation::AUTO_REVERSE);  // Reverse direction each loop
```

### Play Range

Limit animation to a specific progress range:

```cpp
animation.SetPlayRange(Vector2(0.2f, 0.8f));  // Play from 20% to 80%
```

## Animation Signals

Connect to the `FinishedSignal()` to be notified when an animation completes:

```cpp
animation.FinishedSignal().Connect(this, &MyClass::OnAnimationFinished);

void OnAnimationFinished(Animation& animation)
{
  // Animation has finished
}
```

## End Actions

Control what happens when an animation ends:

```cpp
animation.SetEndAction(Animation::BAKE);       // Save final property values
animation.SetEndAction(Animation::DISCARD);    // Discard property changes
animation.SetEndAction(Animation::BAKE_FINAL); // Save as if completed
```

Set the disconnect action for when animated objects are removed from the scene:

```cpp
animation.SetDisconnectAction(Animation::BAKE);
```

## Duration Literals

Use the user-defined literals for convenient duration specification:

```cpp
using namespace Dali::Ui;

Duration d1 = 300_ms;   // 300 milliseconds = 0.3 seconds
Duration d2 = 1.5_s;    // 1.5 seconds
Duration d3 = 2000_ms;  // 2000 milliseconds = 2 seconds
```

These literals can be used directly in animation methods:

```cpp
view.Animate(anim)
  .Opacity(0.0f, 300_ms)
  .PositionY(100.0f, 500_ms, AlphaFunction::EASE_OUT, 100_ms);  // With 100ms delay