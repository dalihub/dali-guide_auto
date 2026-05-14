---
title: Lottie Animation View
sidebar_label: Lottie Animation View
category: images-visuals
---

# Lottie Animation View

`LottieAnimationView` renders vector-based animations from JSON files exported by Adobe After Effects with the Bodymovin plugin. It provides playback control, looping modes, frame manipulation, and resource management for high-quality scalable animations.

## Table of Contents

- [Creating a Lottie Animation View](#creating-a-lottie-animation-view)
- [Playback Control](#playback-control)
- [Looping Configuration](#looping-configuration)
- [Frame Range and Markers](#frame-range-and-markers)
- [Animation Speed and Scaling](#animation-speed-and-scaling)
- [Resource Loading and Placeholder](#resource-loading-and-placeholder)
- [Signals and State Monitoring](#signals-and-state-monitoring)
- [Image Tinting](#image-tinting)
- [Memory and Performance](#memory-and-performance)

## Creating a Lottie Animation View

Create a `LottieAnimationView` using the static `New()` method. You can pass a URL to the JSON file at construction time or set it later.

```cpp
using namespace Dali::Ui;

// Create with a URL
LottieAnimationView lottieView = LottieAnimationView::New("animations/loading.json");

// Create empty and set URL later
LottieAnimationView lottieView = LottieAnimationView::New();
lottieView.SetResourceUrl("animations/loading.json");
```

Add the view to your view hierarchy:

```cpp
lottieView.SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(WRAP_CONTENT)
  .SetLoopCount(-1)  // Infinite loop
  .Play();

window.Add(lottieView);
```

Use `DownCast()` to safely convert a base `View` handle back to `LottieAnimationView`:

```cpp
View baseView = lottieView;
LottieAnimationView downcast = LottieAnimationView::DownCast(baseView);
if (downcast)
{
  downcast.Play();
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

Jump directly to a specific frame using `JumpToFrame()`:

```cpp
// Jump to frame 10
lottieView.JumpToFrame(10);
```

Query the current playback state with `GetPlayState()`:

```cpp
if (lottieView.GetPlayState() == Ui::AnimatedImage::PlayState::PLAYING)
{
  // Animation is playing
}
```

The play state values are:
- `PlayState::STOPPED` — Animation is stopped
- `PlayState::PLAYING` — Animation is actively playing
- `PlayState::PAUSED` — Animation is paused

Query current and total frame counts:

```cpp
int currentFrame = lottieView.GetCurrentFrame();
int totalFrames = lottieView.GetTotalFrame();
```

## Looping Configuration

### Loop Count

Set the number of times the animation repeats using `SetLoopCount()`:

```cpp
// Infinite loop
lottieView.SetLoopCount(-1);

// Play exactly 3 times
lottieView.SetLoopCount(3);

// Play once (no loop)
lottieView.SetLoopCount(1);
```

A value of `0` means the animation will not play. Retrieve the current loop count with `GetLoopCount()`. The default is `-1` (infinite).

### Looping Mode

Control how the animation loops using `SetLoopingMode()`:

```cpp
// Restart from beginning each loop (default)
lottieView.SetLoopingMode(Ui::LottieAnimation::LoopingMode::RESTART);

// Alternate direction each loop
lottieView.SetLoopingMode(Ui::LottieAnimation::LoopingMode::AUTO_REVERSE);
```

With `RESTART` mode, the animation plays from start to end, then jumps back to the start. With `AUTO_REVERSE`, the animation plays forward, then backward, then forward again.

### Stop Behavior

Define where the animation stops when `Stop()` is called using `SetStopBehavior()`:

```cpp
// Stop at current frame (default)
lottieView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::CURRENT_FRAME);

// Stop and jump to first frame
lottieView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::FIRST_FRAME);

// Stop and jump to last frame
lottieView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::LAST_FRAME);
```

## Frame Range and Markers

### Frame Range

Limit playback to a specific frame range using `SetMinMaxFrame()`:

```cpp
int totalFrames = lottieView.GetTotalFrame();

// Play only the first half
lottieView.SetMinMaxFrame(0, totalFrames / 2);

// Play only the second half
lottieView.SetMinMaxFrame(totalFrames / 2, totalFrames - 1);

// Reset to full range
lottieView.SetMinMaxFrame(0, totalFrames - 1);
```

### Marker-Based Range

If the Lottie file contains markers, use `SetMinMaxFrameByMarker()` to define the range by marker names:

```cpp
// Play from "start" marker to end
lottieView.SetMinMaxFrameByMarker("start");

// Play from "intro" marker to "loop" marker
lottieView.SetMinMaxFrameByMarker("intro", "loop");
```

Retrieve marker information with `GetMarkerInfo()`:

```cpp
Dali::Property::Map markerInfo = lottieView.GetMarkerInfo();
```

The returned map contains marker names as keys and two-element integer arrays `[startFrame, endFrame]` as values.

Retrieve layer content information with `GetContentInfo()`:

```cpp
Dali::Property::Map contentInfo = lottieView.GetContentInfo();
```

The returned map contains layer names as keys and two-element integer arrays `[startFrame, endFrame]` as values.

## Animation Speed and Scaling

### Frame Speed Factor

Control playback speed using `SetFrameSpeedFactor()`. Values between 0 and 1 slow down the animation; values above 1 speed it up:

```cpp
// Half speed (slow motion)
lottieView.SetFrameSpeedFactor(0.5f);

// Normal speed (default)
lottieView.SetFrameSpeedFactor(1.0f);

// Double speed
lottieView.SetFrameSpeedFactor(2.0f);
```

The speed factor is clamped to the range [0.01, 100.0] by the underlying animation renderer.

### Render Scale

Adjust the rendering resolution scale using `SetRenderScale()`. Lower values reduce memory usage and improve performance at the cost of visual quality:

```cpp
// Lower quality, better performance
lottieView.SetRenderScale(0.5f);

// Normal quality (default)
lottieView.SetRenderScale(1.0f);

// Higher quality, more memory
lottieView.SetRenderScale(2.0f);
```

A value of 2.0 rasterizes at twice the visual dimensions, producing sharper output on high-density displays. Negative values flip the image.

Control whether the animation redraws when scaled:

```cpp
// Redraw when scaling down (default: true)
lottieView.SetRedrawOnScaleDown(true);

// Redraw when scaling up (default: true)
lottieView.SetRedrawOnScaleUp(true);
```

## Resource Loading and Placeholder

### Resource URL

Set or change the animation file using `SetResourceUrl()`:

```cpp
lottieView.SetResourceUrl("animations/new_animation.json");
```

Retrieve the current URL with `GetResourceUrl()`:

```cpp
Dali::String url = lottieView.GetResourceUrl();
```

### Placeholder Image

Display a placeholder image while the Lottie file loads using `SetPlaceholderUrl()`:

```cpp
lottieView.SetPlaceholderUrl("images/placeholder.png");
lottieView.SetResourceUrl("animations/large_animation.json");
```

The placeholder displays until the Lottie resource is ready.

### Synchronous Loading

Force synchronous loading to block until the resource is ready:

```cpp
lottieView.SetSynchronousLoading(true);
lottieView.SetResourceUrl("animations/animation.json");
// Resource is ready immediately after this call
```

Check the loading status:

```cpp
Ui::Visual::ResourceStatus status = lottieView.GetLoadingStatus();
```

### Desired Size

Set the desired dimensions for the animation as a hint for the renderer:

```cpp
lottieView.SetDesiredWidth(1920);
lottieView.SetDesiredHeight(1080);
```

A value of 0 means use the natural size. Retrieve desired dimensions:

```cpp
int width = lottieView.GetDesiredWidth();
int height = lottieView.GetDesiredHeight();
```

## Signals and State Monitoring

### Animation Finished Signal

Connect to `AnimationFinishedSignal()` to be notified when the animation completes all loops:

```cpp
class MyController : public ConnectionTracker
{
  void SetupAnimation()
  {
    mLottieView.SetLoopCount(1);
    mLottieView.AnimationFinishedSignal().Connect(this, &MyController::OnAnimationFinished);
    mLottieView.Play();
  }

  void OnAnimationFinished(View view)
  {
    // Animation completed
  }

  LottieAnimationView mLottieView;
};
```

### Resource Ready Signal

Connect to `ResourceReadySignal()` to be notified when the Lottie file is loaded and ready:

```cpp
class MyController : public ConnectionTracker
{
  void SetupAnimation()
  {
    mLottieView.ResourceReadySignal().Connect(this, &MyController::OnResourceReady);
    mLottieView.SetResourceUrl("animations/animation.json");
  }

  void OnResourceReady(View view)
  {
    int totalFrames = mLottieView.GetTotalFrame();
    // Resource is ready, can now query frame info
  }

  LottieAnimationView mLottieView;
};
```

## Image Tinting

Apply a color tint to the animation using `SetImageColor()`. The color acts as a per-pixel multiplier:

```cpp
// No tint (original colors)
lottieView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 1.0f));

// Red tint
lottieView.SetImageColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// 50% transparent
lottieView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 0.5f));
```

Retrieve the current tint color:

```cpp
UiColor color = lottieView.GetImageColor();
```

For per-layer color control, use `SetDynamicProperty()` instead.

## Memory and Performance

### Release Policy

Control when the animation resources are released using `SetReleasePolicy()`:

```cpp
// Release when detached from view tree (default)
lottieView.SetReleasePolicy(Ui::Image::ReleasePolicy::DETACHED);

// Release when the view is destroyed
lottieView.SetReleasePolicy(Ui::Image::ReleasePolicy::DESTROYED);

// Never release automatically
lottieView.SetReleasePolicy(Ui::Image::ReleasePolicy::NEVER);
```

### Frame Cache

Enable frame caching to improve playback performance for complex animations. When enabled, all decoded frames are cached in memory to reduce CPU cost during looping, at the expense of higher memory usage:

```cpp
lottieView.SetEnableFrameCache(true);
```

Check if frame cache is enabled:

```cpp
bool cacheEnabled = lottieView.IsFrameCacheEnabled();
```

### Pre-Multiplied Alpha

Control pre-multiplied alpha handling:

```cpp
lottieView.SetPreMultipliedAlpha(true);
```

Check the current setting:

```cpp
bool preMult = lottieView.IsPreMultipliedAlpha();
```

### Rasterization Notification

Request notification after rasterization completes. This is useful for low-fps Lottie files to avoid unnecessary render thread wakeups:

```cpp
lottieView.SetNotifyAfterRasterization(true);
```

Check if notification is enabled:

```cpp
bool notify = lottieView.IsNotifyAfterRasterizationEnabled();
```

### Depth Index

Set the depth index for rendering order within a parent layout:

```cpp
lottieView.SetDepthIndex(10);
```

### Pixel Area

Define a sub-region of the animation to display using `SetPixelArea()`. The area is specified as normalized coordinates (x, y, width, height) where each component is in the range [0, 1]:

```cpp
// Show only the top-left quadrant
lottieView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));
```

Retrieve the current pixel area:

```cpp
Dali::Vector4 pixelArea = lottieView.GetPixelArea();
```

## Dynamic Properties

Set dynamic properties at runtime using `SetDynamicProperty()`. This allows per-frame callbacks to modify Lottie properties such as fill color, stroke width, or transform values:

```cpp
Ui::LottieAnimation::DynamicPropertyInfo info;
info.id = 1;
info.keyPath = "Layer 1.Shape 1.Fill 1";
info.property = Ui::LottieAnimation::VectorProperty::FILL_COLOR;
info.callback = MyCallbackFunction;
lottieView.SetDynamicProperty(info);
```

The `keyPath` specifies the target layer or element. Use `"**"` to target all elements.

**Important:** The callback is invoked on a worker thread. Do not call DALi APIs from within the callback.

## Property Access

All configuration options are also accessible through the property system using `LottieAnimationView::Property` indices:

```cpp
// Set properties using property indices
lottieView.SetProperty(LottieAnimationView::Property::LOOP_COUNT, 3);
lottieView.SetProperty(LottieAnimationView::Property::FRAME_SPEED_FACTOR, 1.5f);
lottieView.SetProperty(LottieAnimationView::Property::IMAGE_COLOR, Vector4(1.0f, 0.5f, 0.5f, 1.0f));
lottieView.SetProperty(LottieAnimationView::Property::RENDER_SCALE, 0.75f);

// Get properties
int loopCount = lottieView.GetProperty(LottieAnimationView::Property::LOOP_COUNT).Get<int>();
float speed = lottieView.GetProperty(LottieAnimationView::Property::FRAME_SPEED_FACTOR).Get<float>();
```

Available property indices:
- `Property::IMAGE` — Resource URL
- `Property::LOOP_COUNT` — Number of loops
- `Property::LOOPING_MODE` — Looping mode
- `Property::STOP_BEHAVIOR` — Stop behavior
- `Property::FRAME_SPEED_FACTOR` — Playback speed
- `Property::IMAGE_COLOR` — Tint color
- `Property::DESIRED_WIDTH` — Desired width
- `Property::DESIRED_HEIGHT` — Desired height
- `Property::RELEASE_POLICY` — Resource release policy
- `Property::SYNCHRONOUS_LOADING` — Synchronous loading flag
- `Property::PRE_MULTIPLIED_ALPHA` — Pre-multiplied alpha flag
- `Property::REDRAW_IN_SCALING_DOWN` — Redraw on scale down
- `Property::REDRAW_IN_SCALING_UP` — Redraw on scale up
- `Property::ENABLE_FRAME_CACHE` — Frame cache enabled
- `Property::NOTIFY_AFTER_RASTERIZATION` — Rasterization notification
- `Property::RENDER_SCALE` — Render scale factor
- `Property::PIXEL_AREA` — Pixel area
- `Property::PLACEHOLDER_IMAGE` — Placeholder URL