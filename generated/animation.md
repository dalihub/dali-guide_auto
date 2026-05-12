---
title: Animation
sidebar_label: Animation
category: animation-motion
---

# Animation

`dali-ui` animation lets application code animate `Dali::Ui::View` state with typed, fluent APIs while `Dali::Animation` controls playback.

## Table of Contents

- [Animate a View](#animate-a-view)
- [Timing and Easing](#timing-and-easing)
- [Reusable Animation Specs](#reusable-animation-specs)
- [Playback Control](#playback-control)
- [Choosing the Right Animation API](#choosing-the-right-animation-api)

## Animate a View

Use `Dali::Ui::View::Animate` as the app-facing entry point. It returns a `Dali::Ui::ViewAnimationBridge`, which records typed animation operations against a `Dali::Animation`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

void FadeAndMove(View view)
{
  Animation animation = Animation::New(0.5f);

  view.Animate(animation)
    .Opacity(0.0f, 250_ms, AlphaFunction::EASE_OUT)
    .PositionY(50.0f, 300_ms, AlphaFunction::EASE_IN_OUT);

  animation.Play();
}
```

The bridge keeps application code centered on `Dali::Ui::View`. Inherited transform and opacity property enums are still owned by `Dali::Actor::Property`, for example `Dali::Actor::Property::OPACITY`, `Dali::Actor::Property::POSITION`, and `Dali::Actor::Property::SCALE`, but typical `dali-ui` code should prefer typed bridge methods such as `Opacity`, `PositionX`, `PositionY`, `ScaleX`, and `ScaleY`.

Relative animation methods use the same bridge shape. Use them when the target value should be expressed as a delta from the current value.

```cpp
void NudgeAndPulse(View view)
{
  Animation animation = Animation::New(0.4f);

  view.Animate(animation)
    .PositionXBy(24.0f, 180_ms, AlphaFunction::EASE_OUT)
    .ScaleXBy(0.1f, 200_ms, AlphaFunction::EASE_OUT_BACK)
    .ScaleYBy(0.1f, 200_ms, AlphaFunction::EASE_OUT_BACK);

  animation.Play();
}
```

## Timing and Easing

`Dali::Ui::Duration` is the `dali-ui` duration type used by `Dali::Ui::ViewAnimationBridge` and `Dali::Ui::ViewAnimationSpec`. Use `_ms` and `_s` literals for readable application code.

```cpp
void StaggeredReveal(View title, View panel)
{
  Animation animation = Animation::New(0.8f);

  title.Animate(animation)
    .Opacity(1.0f, 250_ms, AlphaFunction::EASE_OUT);

  panel.Animate(animation)
    .Opacity(1.0f, 300_ms, AlphaFunction::EASE_IN_OUT, 150_ms)
    .PositionY(0.0f, 300_ms, AlphaFunction::EASE_OUT, 150_ms);

  animation.Play();
}
```

Use `Dali::AlphaFunction` to shape motion. Built-in values such as `Dali::AlphaFunction::EASE_OUT`, `Dali::AlphaFunction::EASE_IN_OUT`, `Dali::AlphaFunction::EASE_OUT_BACK`, and `Dali::AlphaFunction::SIN` are passed where an `AlphaFunction` is expected.

```cpp
void ApplyDefaultEasing(View view)
{
  Animation animation = Animation::New(0.6f);
  animation.SetDefaultAlphaFunction(AlphaFunction::EASE_IN_OUT);

  view.Animate(animation)
    .Opacity(1.0f, 300_ms)
    .PositionX(80.0f, 600_ms);

  animation.Play();
}
```

`Dali::Animation::SetDefaultAlphaFunction` applies when an individual animation entry does not provide its own alpha function.

## Reusable Animation Specs

Use `Dali::Ui::ViewAnimationSpec` when the same motion should be applied to multiple views or replayed from different event handlers. A spec stores typed entries first, then `ApplyTo` attaches them to a concrete `Dali::Animation` and `Dali::Ui::View`.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

ViewAnimationSpec CreateFadeInSpec()
{
  return ViewAnimationSpec::New()
    .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT)
    .PositionY(0.0f, 500_ms, AlphaFunction::EASE_OUT);
}

void PlayFadeIn(View view, const ViewAnimationSpec& spec)
{
  Animation animation = Animation::New(0.5f);
  spec.ApplyTo(animation, view);
  animation.Play();
}
```

`Dali::Ui::ViewAnimationBridge` is best for one-off animation at the call site. `Dali::Ui::ViewAnimationSpec` is best for named motion patterns such as reveal, dismiss, focus, or pressed-state feedback.

For text-specific animation surfaces, `Dali::Ui::LabelAnimationBridge` and `Dali::Ui::LabelAnimationSpec` extend the same idea for label animation while keeping the `dali-ui` typed-animation style.

## Playback Control

`Dali::Animation` owns timeline playback. After one or more `Dali::Ui::View::Animate` calls have added entries, use `Play`, `Pause`, `PlayAfter`, `PlayFrom`, and `Clear` to control the timeline.

```cpp
void ConfigureAndPlay(View view)
{
  Animation animation = Animation::New(1.0f);

  view.Animate(animation)
    .Opacity(1.0f, 400_ms)
    .PositionX(120.0f, 600_ms, AlphaFunction::EASE_OUT, 200_ms);

  animation.SetEndAction(Animation::BAKE);
  animation.SetDisconnectAction(Animation::BAKE_FINAL);
  animation.PlayAfter(0.1f);
}
```

Looping is configured on `Dali::Animation`, not on the individual `Dali::Ui::ViewAnimationBridge` entries. Use either `SetLooping` or `SetLoopCount` for a timeline, because each one resets the other looping mode.

```cpp
void PlayAttentionLoop(View view)
{
  Animation animation = Animation::New(0.7f);

  view.Animate(animation)
    .ScaleX(1.08f, 350_ms, AlphaFunction::EASE_OUT)
    .ScaleY(1.08f, 350_ms, AlphaFunction::EASE_OUT);

  animation.SetLoopCount(3);
  animation.SetLoopingMode(Animation::AUTO_REVERSE);
  animation.Play();
}
```

You can inspect playback state with `Dali::Animation::GetState`, progress with `Dali::Animation::GetCurrentProgress`, and the configured duration with `Dali::Animation::GetDuration`.

```cpp
void PauseIfPlaying(Animation animation)
{
  if(animation.GetState() == Animation::PLAYING)
  {
    animation.Pause();
  }
}
```

## Choosing the Right Animation API

For `dali-ui` applications, start with `Dali::Ui::View::Animate` and `Dali::Ui::ViewAnimationSpec`. They keep examples and production code aligned with the `Dali::Ui::View` object model.

Use lower-level `Dali::Animation` features for timeline-wide behavior: `SetDuration`, `SetSpeedFactor`, `SetEndAction`, `SetDisconnectAction`, `SetLoopCount`, `SetLoopingMode`, `PlayFrom`, and `FinishedSignal`.

```cpp
void ScrubAndReplay(View view, float progress)
{
  Animation animation = Animation::New(1.0f);

  view.Animate(animation)
    .Opacity(1.0f, 300_ms)
    .PositionX(200.0f, 1000_ms, AlphaFunction::EASE_IN_OUT);

  animation.SetSpeedFactor(1.25f);
  animation.SetCurrentProgress(progress);
  animation.PlayFrom(progress);
}
```

Use `Dali::KeyFrames`, `Dali::Path`, `Dali::Constraint`, `Dali::LinearConstrainer`, and `Dali::PathConstrainer` when you are building advanced motion systems that need key-frame curves, path following, or property constraints. For ordinary app UI transitions, the typed `dali-ui` bridge and spec APIs are the preferred surface.
