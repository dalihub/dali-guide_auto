---
title: Lottie Animation View
sidebar_label: Lottie Animation View
category: images-visuals
---

# Lottie Animation View

`LottieAnimationView` renders vector-based animations from JSON files exported by Adobe After Effects with the Bodymovin plugin. It provides high-quality, scalable animations with playback controls, looping modes, and frame-level manipulation.

## Table of Contents

- [Creating a LottieAnimationView](#creating-a-lottieanimationview)
- [Playback Control](#playback-control)
- [Looping Configuration](#looping-configuration)
- [Frame Range and Markers](#frame-range-and-markers)
- [Speed and Timing](#speed-and-timing)
- [Visual Styling](#visual-styling)
- [Resource Management](#resource-management)
- [Signals](#signals)

## Creating a LottieAnimationView

Create a `LottieAnimationView` by calling `LottieAnimationView::New()`. You can pass a URL to the Lottie JSON file at construction time or set it later with `SetResourceUrl()`.

```cpp
using namespace Dali::Ui;

// Create with a URL
LottieAnimationView lottieView = LottieAnimationView::New("animations/loading.json");

// Create empty and set URL later
LottieAnimationView lottieView = LottieAnimationView::New();
lottieView.SetResourceUrl("animations/loading.json");
```

Add the view to your view hierarchy and configure its size:

```cpp
lottieView.SetRequestedWidth(200.0f)
          .SetRequestedHeight(200.0f);
window.Add(lottieView);
```

## Playback Control

Control animation playback with `Play()`, `Pause()`, and `Stop()`. All playback methods return a reference to the view for method chaining.

```cpp
// Start or resume playback
lottieView.Play();

// Pause at the current frame
lottieView.Pause();

// Stop and apply stop behavior
lottieView.Stop();
```

Query the current play state with `GetPlayState()`:

```cpp
if (lottieView.GetPlayState() == Ui::AnimatedImage::PlayState::PLAYING)
{
    // Animation is currently playing
}
```

Jump directly to a specific frame using `JumpToFrame()`:

```cpp
// Jump to frame 30
lottieView.JumpToFrame(30);

// Query current and total frames
int currentFrame = lottieView.GetCurrentFrame();
int totalFrames = lottieView.GetTotalFrame();
```

### Stop Behavior

Configure where the animation stops using `SetStopBehavior()`. The `StopBehavior` enum provides three options:

- `StopBehavior::CURRENT_FRAME`: Stop at the current frame (default)
- `StopBehavior::FIRST_FRAME`: Stop and jump to the first frame
- `StopBehavior::LAST_FRAME`: Stop and jump to the last frame

```cpp
lottieView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::FIRST_FRAME);
lottieView.Stop(); // Will stop at the first frame
```

## Looping Configuration

### Loop Count

Set the number of times the animation repeats with `SetLoopCount()`. Use `-1` for infinite looping. A value of `0` means the animation will not play.

```cpp
// Loop forever
lottieView.SetLoopCount(-1);

// Play exactly 3 times
lottieView.SetLoopCount(3);

// Play once (no looping)
lottieView.SetLoopCount(1);

int loopCount = lottieView.GetLoopCount();
```

### Looping Mode

Control how the animation loops with `SetLoopingMode()`. The `LoopingMode` enum provides two modes:

- `LoopingMode::RESTART`: Animation restarts from the beginning on each loop (default)
- `LoopingMode::AUTO_REVERSE`: Animation plays forward, then backward on alternate loops

```cpp
// Ping-pong animation
lottieView.SetLoopingMode(Ui::LottieAnimation::LoopingMode::AUTO_REVERSE);

// Standard restart loop
lottieView.SetLoopingMode(Ui::LottieAnimation::LoopingMode::RESTART);
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

### Markers

If the Lottie file contains markers, use `SetMinMaxFrameByMarker()` to define the playback range by marker names:

```cpp
// Play the range of the "start" marker
lottieView.SetMinMaxFrameByMarker("start");

// Play from "intro" marker start to "loop" marker end
lottieView.SetMinMaxFrameByMarker("intro", "loop");
```

Retrieve marker information with `GetMarkerInfo()`:

```cpp
Dali::Property::Map markerInfo = lottieView.GetMarkerInfo();
```

### Content Information

Get layer and content information from the Lottie file using `GetContentInfo()`:

```cpp
Dali::Property::Map contentInfo = lottieView.GetContentInfo();
```

## Speed and Timing

Adjust playback speed with `SetFrameSpeedFactor()`. A factor of `1.0f` is normal speed, `0.5f` is half speed, and `2.0f` is double speed.

```cpp
// Half speed (slow motion)
lottieView.SetFrameSpeedFactor(0.5f);

// Normal speed
lottieView.SetFrameSpeedFactor(1.0f);

// Double speed
lottieView.SetFrameSpeedFactor(2.0f);

float speed = lottieView.GetFrameSpeedFactor();
```

## Visual Styling

### Image Color Tint

Apply a color tint to the animation using `SetImageColor()`. The color acts as a per-pixel multiplier. Use white (`UiColor(1.0f, 1.0f, 1.0f, 1.0f)`) for no tint.

```cpp
// Apply red tint
lottieView.SetImageColor(UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// Apply 50% gray tint (desaturate)
lottieView.SetImageColor(UiColor(0.5f, 0.5f, 0.5f, 1.0f));

// Apply 50% transparency
lottieView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 0.5f));

// Reset to original colors
lottieView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 1.0f));

UiColor currentColor = lottieView.GetImageColor();
```

### Render Scale

Control the rendering resolution with `SetRenderScale()`. Lower values reduce memory usage and improve performance at the cost of visual quality.

```cpp
// Lower resolution for better performance
lottieView.SetRenderScale(0.5f);

// Full resolution
lottieView.SetRenderScale(1.0f);

// Higher resolution for sharper output
lottieView.SetRenderScale(2.0f);

float scale = lottieView.GetRenderScale();
```

### Desired Size

Set the desired dimensions for the animation buffer:

```cpp
lottieView.SetDesiredWidth(1920);
lottieView.SetDesiredHeight(1080);

int width = lottieView.GetDesiredWidth();
int height = lottieView.GetDesiredHeight();
```

### Pixel Area

Display a sub-region of the animation using `SetPixelArea()`. The area is specified as normalized coordinates `(x, y, width, height)` where each component is in the range `[0, 1]`.

```cpp
// Show the top-left quadrant
lottieView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));

Dali::Vector4 pixelArea = lottieView.GetPixelArea();
```

### Pre-multiplied Alpha

Enable pre-multiplied alpha blending if your animation requires it:

```cpp
lottieView.SetPreMultipliedAlpha(true);

bool isPreMultiplied = lottieView.IsPreMultipliedAlpha();
```

## Resource Management

### Loading Policy

Control synchronous vs asynchronous loading:

```cpp
// Load synchronously (blocks until ready)
lottieView.SetSynchronousLoading(true);

// Load asynchronously (default)
lottieView.SetSynchronousLoading(false);

bool isSync = lottieView.IsSynchronousLoading();
```

Check the loading status:

```cpp
Ui::Visual::ResourceStatus status = lottieView.GetLoadingStatus();
```

### Release Policy

Control when the animation resource is released from memory using `SetReleasePolicy()`:

- `ReleasePolicy::DETACHED`: Release when the view is detached from the scene (default)
- `ReleasePolicy::DESTROYED`: Release when the view is destroyed
- `ReleasePolicy::NEVER`: Keep the resource in memory indefinitely

```cpp
lottieView.SetReleasePolicy(Ui::Image::ReleasePolicy::NEVER);

Ui::Image::ReleasePolicy policy = lottieView.GetReleasePolicy();
```

### Placeholder Image

Display a placeholder image while the Lottie file loads:

```cpp
lottieView.SetPlaceholderUrl("images/placeholder.png");
lottieView.SetResourceUrl("animations/heavy-animation.json");

Dali::String placeholderUrl = lottieView.GetPlaceholderUrl();
```

### Frame Caching

Enable frame caching to improve playback performance for complex animations:

```cpp
lottieView.SetEnableFrameCache(true);

bool isCacheEnabled = lottieView.IsFrameCacheEnabled();
```

### Redraw on Scale

Control whether the animation redraws when scaled:

```cpp
// Redraw when scaling up (sharper)
lottieView.SetRedrawOnScaleUp(true);

// Redraw when scaling down (sharper)
lottieView.SetRedrawOnScaleDown(true);

bool redrawUp = lottieView.IsRedrawOnScaleUp();
bool redrawDown = lottieView.IsRedrawOnScaleDown();
```

### Notify After Rasterization

Receive notification after each frame is rasterized:

```cpp
lottieView.SetNotifyAfterRasterization(true);

bool isNotifyEnabled = lottieView.IsNotifyAfterRasterizationEnabled();
```

## Signals

### Animation Finished Signal

Connect to `AnimationFinishedSignal()` to be notified when the animation completes (for finite animations):

```cpp
class MyController : public Dali::ConnectionTracker
{
public:
    void SetupAnimation(LottieAnimationView& view)
    {
        view.SetLoopCount(1); // Play once
        view.AnimationFinishedSignal().Connect(this, &MyController::OnAnimationFinished);
        view.Play();
    }

    void OnAnimationFinished(Dali::View view)
    {
        // Animation completed
    }
};
```

### Resource Ready Signal

Connect to `ResourceReadySignal()` to be notified when the Lottie resource is loaded and ready:

```cpp
class MyController : public Dali::ConnectionTracker
{
public:
    void SetupAnimation(LottieAnimationView& view)
    {
        view.ResourceReadySignal().Connect(this, &MyController::OnResourceReady);
        view.SetResourceUrl("animations/loading.json");
    }

    void OnResourceReady(Dali::View view)
    {
        LottieAnimationView lottieView = LottieAnimationView::DownCast(view);
        if (lottieView)
        {
            int totalFrames = lottieView.GetTotalFrame();
            // Resource is ready, can start playback
            lottieView.Play();
        }
    }
};
```

## Properties

`LottieAnimationView` exposes the following properties through `LottieAnimationView::Property`:

| Property | Type | Description |
|----------|------|-------------|
| `IMAGE` | String | URL of the Lottie JSON file |
| `LOOP_COUNT` | Integer | Number of loops (-1 for infinite, 0 to not play) |
| `LOOPING_MODE` | Integer | Looping mode (RESTART or AUTO_REVERSE) |
| `STOP_BEHAVIOR` | Integer | Stop behavior (CURRENT_FRAME, FIRST_FRAME, LAST_FRAME) |
| `FRAME_SPEED_FACTOR` | Float | Playback speed multiplier |
| `IMAGE_COLOR` | Vector4 | Color tint (RGBA) |
| `DESIRED_WIDTH` | Integer | Desired buffer width |
| `DESIRED_HEIGHT` | Integer | Desired buffer height |
| `RELEASE_POLICY` | Integer | Resource release policy |
| `SYNCHRONOUS_LOADING` | Boolean | Enable synchronous loading |
| `PRE_MULTIPLIED_ALPHA` | Boolean | Enable pre-multiplied alpha |
| `REDRAW_IN_SCALING_UP` | Boolean | Redraw when scaling up |
| `REDRAW_IN_SCALING_DOWN` | Boolean | Redraw when scaling down |
| `ENABLE_FRAME_CACHE` | Boolean | Enable frame caching |
| `NOTIFY_AFTER_RASTERIZATION` | Boolean | Notify after each frame rasterization |
| `RENDER_SCALE` | Float | Rendering resolution scale |
| `PLACEHOLDER_IMAGE` | String | Placeholder image URL |
| `PIXEL_AREA` | Vector4 | Sub-region to display |

Set and get properties using the property indices:

```cpp
// Set property
lottieView.SetProperty(LottieAnimationView::Property::LOOP_COUNT, 3);
lottieView.SetProperty(LottieAnimationView::Property::FRAME_SPEED_FACTOR, 1.5f);
lottieView.SetProperty(LottieAnimationView::Property::IMAGE_COLOR, Vector4(1.0f, 0.5f, 0.5f, 1.0f));

// Get property
int loopCount = lottieView.GetProperty(LottieAnimationView::Property::LOOP_COUNT).Get<int>();
float speed = lottieView.GetProperty(LottieAnimationView::Property::FRAME_SPEED_FACTOR).Get<float>();
```

## Type Casting

Use `DownCast()` to safely cast a base `View` to `LottieAnimationView`:

```cpp
Dali::View baseView = GetViewFromSomewhere();
LottieAnimationView lottieView = LottieAnimationView::DownCast(baseView);

if (lottieView)
{
    // Successfully cast, can use LottieAnimationView methods
    lottieView.Play();
}
```
