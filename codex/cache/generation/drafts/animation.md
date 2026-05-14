---
title: Animation
sidebar_label: Animation
category: animation-motion
---

# Animation

`Animation` lets a dali-ui app move, fade, scale, and sequence `Dali::Ui::View` changes with typed, fluent APIs.

## Table of Contents

- [Animate a View Directly](#animate-a-view-directly)
- [Reuse Motion with ViewAnimationSpec](#reuse-motion-with-viewanimationspec)
- [Control Timing, Easing, and Delays](#control-timing-easing-and-delays)
- [Loop, Reverse, Pause, and Resume](#loop-reverse-pause-and-resume)
- [End Behavior and Cleanup](#end-behavior-and-cleanup)

## Animate a View Directly

For application code, start from `Dali::Ui::View` and create a `Dali::Animation` as the timing handle. `Dali::Ui::View::Animate` returns a `Dali::Ui::ViewAnimationBridge`, which applies typed animation entries to that `View`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

void FadeOut(Dali::Ui::View panel)
{
  Dali::Animation animation = Dali::Animation::New();

  panel.Animate(animation)
    .Opacity(0.0f, Dali::Ui::Duration(0.25f), Dali::AlphaFunction::EASE_OUT);

  animation.Play();
}
```

The fluent calls on `Dali::Ui::ViewAnimationBridge` are view-oriented. Use `Opacity`, `PositionX`, `PositionY`, `ScaleX`, and `ScaleY` for common app motion instead of addressing raw `Dali::Actor` properties directly.

```cpp
void SlideAndFadeIn(Dali::Ui::View panel)
{
  Dali::Animation animation = Dali::Animation::New();

  panel.Animate(animation)
    .PositionY(0.0f, Dali::Ui::Duration(0.35f), Dali::AlphaFunction::EASE_OUT)
    .Opacity(1.0f, Dali::Ui::Duration(0.20f), Dali::AlphaFunction::EASE_IN);

  animation.Play();
}
```

Use the `By` variants when the destination should be relative to the current view state.

```cpp
void Nudge(Dali::Ui::View panel)
{
  Dali::Animation animation = Dali::Animation::New();

  panel.Animate(animation)
    .PositionXBy(24.0f, Dali::Ui::Duration(0.15f), Dali::AlphaFunction::EASE_OUT)
    .ScaleXBy(0.05f, Dali::Ui::Duration(0.15f))
    .ScaleYBy(0.05f, Dali::Ui::Duration(0.15f));

  animation.Play();
}
```

## Reuse Motion with ViewAnimationSpec

Use `Dali::Ui::ViewAnimationSpec` when the same motion recipe should be applied to multiple views or replayed later. Create a spec with `Dali::Ui::View::NewAnimationSpec`, add typed entries, then call `ApplyTo`.

```cpp
Dali::Ui::ViewAnimationSpec CreateEnterSpec()
{
  return Dali::Ui::View::NewAnimationSpec()
    .Opacity(1.0f, Dali::Ui::Duration(0.25f), Dali::AlphaFunction::EASE_IN_OUT)
    .PositionY(0.0f, Dali::Ui::Duration(0.35f), Dali::AlphaFunction::EASE_OUT);
}

void PlayEnter(Dali::Ui::View card, const Dali::Ui::ViewAnimationSpec& enterSpec)
{
  Dali::Animation animation = Dali::Animation::New();

  enterSpec.ApplyTo(animation, card);

  animation.Play();
}
```

A spec stores the animation entries; the `Dali::Animation` still owns playback state such as duration, current progress, loop count, and whether it is playing or paused.

```cpp
void ApplySharedPulse(Dali::Ui::View first, Dali::Ui::View second)
{
  Dali::Ui::ViewAnimationSpec pulse = Dali::Ui::View::NewAnimationSpec()
    .ScaleX(1.08f, Dali::Ui::Duration(0.18f), Dali::AlphaFunction::EASE_OUT)
    .ScaleY(1.08f, Dali::Ui::Duration(0.18f), Dali::AlphaFunction::EASE_OUT)
    .Opacity(0.85f, Dali::Ui::Duration(0.18f));

  Dali::Animation firstAnimation = Dali::Animation::New();
  Dali::Animation secondAnimation = Dali::Animation::New();

  pulse.ApplyTo(firstAnimation, first);
  pulse.ApplyTo(secondAnimation, second);

  firstAnimation.Play();
  secondAnimation.PlayAfter(0.08f);
}
```

## Control Timing, Easing, and Delays

`Dali::Ui::Duration` is passed to each typed view animation entry. `Dali::AlphaFunction` controls interpolation. If no alpha function is supplied to a typed entry, the default is linear.

```cpp
void StageEntrance(Dali::Ui::View title, Dali::Ui::View body)
{
  Dali::Animation animation = Dali::Animation::New();

  title.Animate(animation)
    .Opacity(1.0f, Dali::Ui::Duration(0.20f), Dali::AlphaFunction::EASE_IN)
    .PositionY(0.0f, Dali::Ui::Duration(0.30f), Dali::AlphaFunction::EASE_OUT);

  body.Animate(animation)
    .Opacity(1.0f, Dali::Ui::Duration(0.20f), Dali::AlphaFunction::EASE_IN, Dali::Ui::Duration(0.10f))
    .PositionY(0.0f, Dali::Ui::Duration(0.30f), Dali::AlphaFunction::EASE_OUT, Dali::Ui::Duration(0.10f));

  animation.Play();
}
```

You can set a default easing on `Dali::Animation` with `SetDefaultAlphaFunction`. Typed entries that receive an explicit `Dali::AlphaFunction` use that value for the entry.

```cpp
void UseDefaultEasing(Dali::Ui::View panel)
{
  Dali::Animation animation = Dali::Animation::New();
  animation.SetDefaultAlphaFunction(Dali::AlphaFunction::EASE_IN_OUT);

  panel.Animate(animation)
    .Opacity(1.0f, Dali::Ui::Duration(0.25f))
    .PositionX(0.0f, Dali::Ui::Duration(0.25f));

  animation.Play();
}
```

`PlayAfter` delays the start of the whole `Dali::Animation`. Per-entry delay on `Dali::Ui::ViewAnimationBridge` offsets individual entries inside the same animation.

```cpp
void DelayedReveal(Dali::Ui::View panel)
{
  Dali::Animation animation = Dali::Animation::New();

  panel.Animate(animation)
    .Opacity(1.0f, Dali::Ui::Duration(0.20f))
    .ScaleX(1.0f, Dali::Ui::Duration(0.25f), Dali::AlphaFunction::EASE_OUT)
    .ScaleY(1.0f, Dali::Ui::Duration(0.25f), Dali::AlphaFunction::EASE_OUT);

  animation.PlayAfter(0.15f);
}
```

## Loop, Reverse, Pause, and Resume

`Dali::Animation` controls playback. Use `SetLoopCount` for a fixed repeat count, or `SetLooping(true)` for continuous playback. Use `SetLoopingMode` with `Dali::Animation::AUTO_REVERSE` when the animation should run forward and backward across each loop.

```cpp
Dali::Animation StartBreathing(Dali::Ui::View view)
{
  Dali::Animation animation = Dali::Animation::New();

  view.Animate(animation)
    .ScaleX(1.04f, Dali::Ui::Duration(0.8f), Dali::AlphaFunction::EASE_IN_OUT)
    .ScaleY(1.04f, Dali::Ui::Duration(0.8f), Dali::AlphaFunction::EASE_IN_OUT);

  animation.SetLooping(true);
  animation.SetLoopingMode(Dali::Animation::AUTO_REVERSE);
  animation.Play();

  return animation;
}
```

For a finite loop, use `SetLoopCount`. A count of `0` is equivalent to continuous looping, so use a positive value for a fixed repeat count.

```cpp
void PulseThreeTimes(Dali::Ui::View view)
{
  Dali::Animation animation = Dali::Animation::New();

  view.Animate(animation)
    .Opacity(0.6f, Dali::Ui::Duration(0.2f), Dali::AlphaFunction::EASE_IN_OUT);

  animation.SetLoopCount(3);
  animation.SetLoopingMode(Dali::Animation::AUTO_REVERSE);
  animation.Play();
}
```

Use `Pause`, `Play`, `PlayFrom`, and `SetCurrentProgress` for interactive playback control.

```cpp
void TogglePaused(Dali::Animation animation)
{
  if(animation.GetState() == Dali::Animation::PLAYING)
  {
    animation.Pause();
  }
  else
  {
    animation.Play();
  }
}

void ReplayFromMiddle(Dali::Animation animation)
{
  animation.PlayFrom(0.5f);
}
```

`SetPlayRange` limits playback to a normalized segment. `SetSpeedFactor` changes playback speed; values below `1.0f` slow down, values above `1.0f` speed up, and negative values play in reverse.

```cpp
void PlaySecondHalfFast(Dali::Animation animation)
{
  animation.SetPlayRange(Dali::Vector2(0.5f, 1.0f));
  animation.SetSpeedFactor(2.0f);
  animation.PlayFrom(0.5f);
}
```

## End Behavior and Cleanup

`Dali::Animation::SetEndAction` defines what happens to animated values when playback ends or the animation is stopped. The default is `Dali::Animation::BAKE`, which keeps the animated values. Use `Dali::Animation::DISCARD` when the animated values should be forgotten, or `Dali::Animation::BAKE_FINAL` when stopping should save the final values as if playback completed.

```cpp
void FadeForPreview(Dali::Ui::View preview)
{
  Dali::Animation animation = Dali::Animation::New();

  preview.Animate(animation)
    .Opacity(0.35f, Dali::Ui::Duration(0.2f));

  animation.SetEndAction(Dali::Animation::DISCARD);
  animation.Play();
}
```

Use `SetDisconnectAction` to control what happens if an animated property owner is disconnected while the animation is playing.

```cpp
void AnimatePossiblyTemporaryView(Dali::Ui::View view)
{
  Dali::Animation animation = Dali::Animation::New();

  view.Animate(animation)
    .PositionY(80.0f, Dali::Ui::Duration(0.3f), Dali::AlphaFunction::EASE_OUT)
    .Opacity(0.0f, Dali::Ui::Duration(0.3f));

  animation.SetDisconnectAction(Dali::Animation::BAKE_FINAL);
  animation.Play();
}
```

Connect `FinishedSignal` when application state should update after the animation completes. The signal callback receives the finished `Dali::Animation`.

```cpp
class MotionController : public Dali::ConnectionTracker
{
public:
  void Collapse(Dali::Ui::View panel)
  {
    Dali::Animation animation = Dali::Animation::New();

    panel.Animate(animation)
      .Opacity(0.0f, Dali::Ui::Duration(0.2f), Dali::AlphaFunction::EASE_IN)
      .PositionY(-32.0f, Dali::Ui::Duration(0.2f), Dali::AlphaFunction::EASE_IN);

    animation.FinishedSignal().Connect(this, &MotionController::OnCollapseFinished);
    animation.Play();
  }

private:
  void OnCollapseFinished(Dali::Animation animation)
  {
    animation.Clear();
  }
};
```

`Clear` disconnects animated objects from the `Dali::Animation`. Keep the `Dali::Animation` handle alive for as long as your app needs to query or control playback state with methods such as `GetState`, `GetCurrentProgress`, `GetLoopCount`, or `GetAnimationId`.