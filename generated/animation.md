---
title: Animation
sidebar_label: Animation
category: animation-motion
---

# Animation

`dali-ui` animation is written against `Dali::Ui::View`, with `Dali::Animation` controlling playback and `Dali::Ui::ViewAnimationBridge` or `Dali::Ui::ViewAnimationSpec` describing what changes.

## Table of Contents

- [Animate a `View` Directly](#animate-a-view-directly)
- [Reuse Motion with `ViewAnimationSpec`](#reuse-motion-with-viewanimationspec)
- [Timing, Delay, and Easing](#timing-delay-and-easing)
- [Playback Control](#playback-control)
- [Completion Handling](#completion-handling)

## Animate a `View` Directly

For application code, start from a `Dali::Ui::View` and call `Dali::Ui::View::Animate`. This creates a `Dali::Ui::ViewAnimationBridge` bound to that view and to a `Dali::Animation`.

The bridge exposes typed animation methods such as `Opacity`, `PositionX`, `PositionY`, `ScaleX`, and `ScaleY`. These are the preferred app-facing form for common view motion. The inherited property constants behind opacity, position, and scale are owned by `Dali::Actor::Property`, for example `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION`, and `Dali::Actor::Property::SCALE`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void FadeOut(View view)
{
  Animation animation = Animation::New(0.5f);

  view.Animate(animation)
    .Opacity(0.0f, 500_ms);

  animation.Play();
}
```

Multiple typed changes can be chained on the same `Dali::Ui::ViewAnimationBridge`. Set the `Dali::Animation` duration to cover the longest animated segment.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void MoveAndFade(View view)
{
  Animation animation = Animation::New(0.7f);

  view.Animate(animation)
    .PositionY(80.0f, 600_ms, AlphaFunction(AlphaFunction::EASE_OUT), 100_ms)
    .Opacity(0.2f, 400_ms, AlphaFunction(), 0_ms);

  animation.Play();
}
```

Use relative methods such as `PositionXBy`, `PositionYBy`, `ScaleXBy`, `ScaleYBy`, and `OpacityBy` when the motion should be based on the current value rather than an absolute destination.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void Nudge(View view)
{
  Animation animation = Animation::New(0.25f);

  view.Animate(animation)
    .PositionXBy(24.0f, 250_ms, AlphaFunction(AlphaFunction::EASE_OUT))
    .OpacityBy(-0.15f, 250_ms);

  animation.Play();
}
```

## Reuse Motion with `ViewAnimationSpec`

Use `Dali::Ui::ViewAnimationSpec` when the same motion should be applied to more than one `Dali::Ui::View`, or when the animation description should be built once and played later. Create it with `Dali::Ui::View::NewAnimationSpec`, add typed animation entries, then call `ApplyTo`.

`ApplyTo` writes the spec entries into a `Dali::Animation`. If an entry needs more time than the current animation duration, the implementation extends the animation duration to cover that entry's delay plus duration.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

ViewAnimationSpec CreateEnterSpec()
{
  return View::NewAnimationSpec()
    .Opacity(1.0f, 300_ms)
    .PositionY(0.0f, 450_ms, AlphaFunction(AlphaFunction::EASE_OUT));
}

void PlayEnter(View view)
{
  ViewAnimationSpec spec = CreateEnterSpec();
  Animation animation = Animation::New(0.1f);

  spec.ApplyTo(animation, view);
  animation.Play();
}
```

A spec can also express relative motion. This is useful for reusable feedback such as a press bounce or a small attention shift.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void PlayPulse(View view)
{
  ViewAnimationSpec pulse = View::NewAnimationSpec()
    .ScaleXBy(0.08f, 120_ms, AlphaFunction(AlphaFunction::EASE_OUT))
    .ScaleYBy(0.08f, 120_ms, AlphaFunction(AlphaFunction::EASE_OUT))
    .OpacityBy(-0.1f, 120_ms);

  Animation animation = Animation::New(0.12f);
  pulse.ApplyTo(animation, view);
  animation.Play();
}
```

## Timing, Delay, and Easing

`Dali::Ui::Duration` is the dali-ui time value used by the typed animation API. Construct it with `Dali::Ui::Duration`, or use the `_ms` and `_s` literals from `Dali::Ui`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void InspectDurations()
{
  Duration quick = 180_ms;
  Duration slow = 1.25_s;

  float quickSeconds = quick.InSeconds();
  float slowMilliseconds = slow.InMilliseconds();

  (void)quickSeconds;
  (void)slowMilliseconds;
}
```

Typed view animation methods accept an optional `Dali::AlphaFunction` and an optional delay. `Dali::AlphaFunction()` uses the default alpha function for the entry. Built-in functions such as `Dali::AlphaFunction::EASE_IN`, `Dali::AlphaFunction::EASE_OUT`, and `Dali::AlphaFunction::EASE_IN_OUT` can be passed through `Dali::AlphaFunction`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void Stagger(View first, View second)
{
  Animation animation = Animation::New(0.8f);

  first.Animate(animation)
    .Opacity(1.0f, 300_ms, AlphaFunction(AlphaFunction::EASE_IN_OUT), 0_ms);

  second.Animate(animation)
    .Opacity(1.0f, 300_ms, AlphaFunction(AlphaFunction::EASE_IN_OUT), 250_ms)
    .PositionY(0.0f, 550_ms, AlphaFunction(AlphaFunction::EASE_OUT), 250_ms);

  animation.Play();
}
```

Use `Dali::Animation::SetDefaultAlphaFunction` when most entries in an animation should share the same easing. Per-entry alpha functions still override the default for that entry.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void FadeWithDefaultEase(View view)
{
  Animation animation = Animation::New(0.4f);
  animation.SetDefaultAlphaFunction(AlphaFunction(AlphaFunction::EASE_IN_OUT));

  view.Animate(animation)
    .Opacity(1.0f, 400_ms);

  animation.Play();
}
```

## Playback Control

`Dali::Animation` owns playback state. After building view animation entries, use `Play`, `Pause`, `PlayFrom`, or `PlayAfter` to control when motion runs.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void ConfigurePlayback(View view)
{
  Animation animation = Animation::New(1.0f);

  view.Animate(animation)
    .PositionX(240.0f, 1_s, AlphaFunction(AlphaFunction::EASE_OUT));

  animation.SetSpeedFactor(1.5f);
  animation.PlayFrom(0.25f);
}
```

Looping is configured on `Dali::Animation`. Use either `SetLooping` or `SetLoopCount` for the same animation, and use `SetLoopingMode` to choose restart or auto-reverse behavior.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void PlayBreathingOpacity(View view)
{
  Animation animation = Animation::New(0.8f);

  view.Animate(animation)
    .Opacity(0.45f, 800_ms, AlphaFunction(AlphaFunction::EASE_IN_OUT));

  animation.SetLoopCount(0);
  animation.SetLoopingMode(Animation::AUTO_REVERSE);
  animation.Play();
}
```

Use `SetPlayRange` when only part of the animation timeline should run, and `SetCurrentProgress` when the current progress must be positioned before playback.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void PlayMiddleSegment(View view)
{
  Animation animation = Animation::New(1.0f);

  view.Animate(animation)
    .PositionY(120.0f, 1_s);

  animation.SetPlayRange(Vector2(0.25f, 0.75f));
  animation.SetCurrentProgress(0.25f);
  animation.Play();
}
```

## Completion Handling

Connect to `Dali::Animation::FinishedSignal` when the app needs to react after all entries in the animation finish. The signal callback receives the finished `Dali::Animation`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

class MotionController : public ConnectionTracker
{
public:
  void PlayAndTrack(View view)
  {
    Animation animation = Animation::New(0.3f);

    view.Animate(animation)
      .Opacity(0.0f, 300_ms);

    animation.FinishedSignal().Connect(this, &MotionController::OnFinished);
    animation.Play();
  }

private:
  void OnFinished(Animation animation)
  {
    Animation::State state = animation.GetState();
    uint32_t animationId = animation.GetAnimationId();

    (void)state;
    (void)animationId;
  }
};
```

`Dali::Animation::SetEndAction` controls what happens to animated values when the animation ends or is stopped. `Dali::Animation::BAKE` keeps the animated property values at their current result, `Dali::Animation::DISCARD` forgets them, and `Dali::Animation::BAKE_FINAL` saves the final values when stopped as if the animation had completed.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void FadeAndKeepFinalValue(View view)
{
  Animation animation = Animation::New(0.5f);

  view.Animate(animation)
    .Opacity(0.0f, 500_ms);

  animation.SetEndAction(Animation::BAKE_FINAL);
  animation.SetDisconnectAction(Animation::BAKE_FINAL);
  animation.Play();
}
```
