---
title: Lottie Animation View
sidebar_label: Lottie Animation View
category: uncategorized
---

# Lottie Animation View

`LottieAnimationView` is a dali-ui View for rendering JSON-based Lottie vector animations with playback control, loop configuration, and frame-range manipulation.

## Table of Contents

- [Creating a LottieAnimationView](#creating-a-lottieanimationview)
- [Playback Control](#playback-control)
- [Loop Configuration](#loop-configuration)
- [Frame Range and Markers](#frame-range-and-markers)
- [Visual Appearance](#visual-appearance)
- [Performance Tuning](#performance-tuning)
- [Resource Loading](#resource-loading)
- [Signals](#signals)

## Creating a LottieAnimationView

Create a `LottieAnimationView` using `LottieAnimationView::New()`, optionally passing the URL of a Lottie JSON file:

```cpp
using namespace Dali::Ui;

// Create with a URL
LottieAnimationView view = LottieAnimationView::New("animations/loading.json");

// Create empty and set URL later
LottieAnimationView view = LottieAnimationView::New();
view.SetResourceUrl("animations/loading.json");
```

Set the view size and add it to your view hierarchy:

```cpp
LottieAnimationView::New("animations/character.json")
  .SetRequestedWidth(300.0f)
  .SetRequestedHeight(300.0f)
  .As(lottieView);

window.Add(lottieView);
```

Use `DownCast()` to convert a base `View` handle back to `LottieAnimationView`:

```cpp
View baseView = /* ... */;
LottieAnimationView lottie = LottieAnimationView::DownCast(baseView);
if (lottie)
{
  lottie.Play();
}
```

## Playback Control

Control animation playback with `Play()`, `Pause()`, and `Stop()`:

```cpp
// Start or resume playback
lottieView.Play();

// Pause at current frame
lottieView.Pause();

// Stop and apply stop behavior
lottieView.Stop();
```

Query the current playback state using `GetPlayState()`:

```cpp
using namespace Dali::Ui::AnimatedImage;

if (lottieView.GetPlayState() == PlayState::PLAYING)
{
  lottieView.Pause();
}
```

Jump directly to a specific frame with `JumpToFrame()`:

```cpp
// Jump to frame 10
lottieView.JumpToFrame(10);
```

Query the current frame and total frame count:

```cpp
int current = lottieView.GetCurrentFrame();
int total = lottieView.GetTotalFrame();

DALI_LOG_RELEASE_INFO("Frame %d of %d\n", current, total);
```

## Loop Configuration

Set the number of animation loops using `SetLoopCount()`. A value of `-1` enables infinite looping, `0` prevents playback, and positive values specify exact loop counts:

```cpp
// Infinite looping
lottieView.SetLoopCount(-1);

// Play exactly 3 times
lottieView.SetLoopCount(3);

// Play once (no looping)
lottieView.SetLoopCount(1);
```

Control how the animation loops using `SetLoopingMode()`:

```cpp
using namespace Dali::Ui::LottieAnimation;

// Restart from beginning after each loop (default)
lottieView.SetLoopingMode(LoopingMode::RESTART);

// Reverse direction at each loop boundary
lottieView.SetLoopingMode(LoopingMode::AUTO_REVERSE);
```

Configure where the animation stops using `SetStopBehavior()`:

```cpp
using namespace Dali::Ui::AnimatedImage;

// Stop at current frame (default)
lottieView.SetStopBehavior(StopBehavior::CURRENT_FRAME);

// Stop and jump to first frame
lottieView.SetStopBehavior(StopBehavior::FIRST_FRAME);

// Stop and jump to last frame
lottieView.SetStopBehavior(StopBehavior::LAST_FRAME);
```

Adjust playback speed with `SetFrameSpeedFactor()`. Values below `1.0` slow down the animation; values above `1.0` speed it up:

```cpp
// Half speed
lottieView.SetFrameSpeedFactor(0.5f);

// Normal speed (default)
lottieView.SetFrameSpeedFactor(1.0f);

// Double speed
lottieView.SetFrameSpeedFactor(2.0f);
```

## Frame Range and Markers

Limit playback to a specific frame range using `SetMinMaxFrame()`:

```cpp
int total = lottieView.GetTotalFrame();

// Play only the first half
lottieView.SetMinMaxFrame(0, total / 2);

// Play only the second half
lottieView.SetMinMaxFrame(total / 2, total - 1);

// Reset to full range
lottieView.SetMinMaxFrame(0, total - 1);
```

Use markers embedded in the Lottie file with `SetMinMaxFrameByMarker()`:

```cpp
// Play the range defined by a single marker
lottieView.SetMinMaxFrameByMarker("intro");

// Play from one marker to another
lottieView.SetMinMaxFrameByMarker("start", "end");
```

Query marker and layer information from the Lottie file:

```cpp
// Get marker information
Dali::Property::Map markers = lottieView.GetMarkerInfo();

// Get layer information
Dali::Property::Map layers = lottieView.GetContentInfo();
```

## Visual Appearance

Apply a color tint to the entire animation using `SetImageColor()`. The color acts as a per-pixel multiplier:

```cpp
// No tint (original appearance)
lottieView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 1.0f));

// Red tint
lottieView.SetImageColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// 50% transparent
lottieView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 0.5f));
```

Control render quality with `SetRenderScale()`. Higher values rasterize at larger dimensions for sharper output on high-density displays:

```cpp
// Standard quality (default)
lottieView.SetRenderScale(1.0f);

// Higher quality for high-DPI displays
lottieView.SetRenderScale(2.0f);

// Lower quality for memory savings
lottieView.SetRenderScale(0.5f);
```

Set desired rasterization dimensions as hints for the renderer:

```cpp
lottieView.SetDesiredWidth(1920);
lottieView.SetDesiredHeight(1080);
```

## Performance Tuning

Enable frame caching to reduce CPU usage during looping at the cost of higher memory consumption:

```cpp
// Cache all decoded frames
lottieView.SetEnableFrameCache(true);
```

Control whether the animation redraws when scaled:

```cpp
// Disable redraw when scaling down (saves performance)
lottieView.SetRedrawOnScaleDown(false);

// Disable redraw when scaling up
lottieView.SetRedrawOnScaleUp(false);
```

For low-FPS animations, enable notify-after-rasterization to reduce unnecessary render thread wakeups:

```cpp
lottieView.SetNotifyAfterRasterization(true);
```

## Resource Loading

Set a placeholder image to display while the Lottie file loads:

```cpp
lottieView.SetPlaceholderUrl("images/placeholder.png");
lottieView.SetResourceUrl("animations/heavy-animation.json");
```

Control the resource release policy:

```cpp
using namespace Dali::Ui::Image;

// Release when detached from view hierarchy (default)
lottieView.SetReleasePolicy(ReleasePolicy::DETACHED);

// Keep resource until the view is destroyed
lottieView.SetReleasePolicy(ReleasePolicy::DESTROYED);

// Never release automatically
lottieView.SetReleasePolicy(ReleasePolicy::NEVER);
```

Enable synchronous loading to block until the JSON is parsed:

```cpp
lottieView.SetSynchronousLoading(true);
```

Configure pre-multiplied alpha for the rendered frames:

```cpp
lottieView.SetPreMultipliedAlpha(true);
```

Check the loading status:

```cpp
Ui::Visual::ResourceStatus status = lottieView.GetLoadingStatus();
```

## Signals

Connect to `ResourceReadySignal()` to know when the Lottie file has finished loading:

```cpp
lottieView.ResourceReadySignal().Connect(this, &MyClass::OnResourceReady);

// ...

void OnResourceReady(View view)
{
  LottieAnimationView lottie = LottieAnimationView::DownCast(view);
  int totalFrames = lottie.GetTotalFrame();
  DALI_LOG_RELEASE_INFO("Animation ready with %d frames\n", totalFrames);
}
```

Connect to `AnimationFinishedSignal()` to be notified when the animation completes all loops:

```cpp
lottieView.AnimationFinishedSignal().Connect(this, &MyClass::OnAnimationFinished);

// ...

void OnAnimationFinished(View view)
{
  DALI_LOG_RELEASE_INFO("Animation finished!\n");
}
```

Note: `AnimationFinishedSignal()` is emitted when the animation completes all requested loops. For infinite looping animations (`SetLoopCount(-1)`), this signal will not fire.
