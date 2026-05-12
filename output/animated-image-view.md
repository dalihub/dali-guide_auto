---
title: Animated Image View
sidebar_label: Animated Image View
category: uncategorized
---

# Animated Image View

`AnimatedImageView` is a `Dali::Ui::View` for displaying animated image resources such as GIF and WebP files. It provides playback control (play, pause, stop), loop count configuration, frame-level manipulation, and visual customization including alpha masking and color tinting.

## Table of Contents

- [Creating an AnimatedImageView](#creating-an-animatedimageview)
- [Playback Control](#playback-control)
- [Frame and Speed Control](#frame-and-speed-control)
- [Image Sources and Caching](#image-sources-and-caching)
- [Visual Appearance](#visual-appearance)
- [Alpha Masking](#alpha-masking)
- [Loading Configuration](#loading-configuration)
- [Signals](#signals)

## Creating an AnimatedImageView

Create an `AnimatedImageView` using the static `New()` method. You can pass an optional URL to load an animated image immediately.

```cpp
using namespace Dali::Ui;

// Create with an animated GIF
AnimatedImageView animatedView = AnimatedImageView::New("animation.gif");

// Create empty and set URL later
AnimatedImageView animatedView = AnimatedImageView::New();
animatedView.SetResourceUrl("animation.webp");
```

Set the view size and add it to your view hierarchy:

```cpp
animatedView.SetRequestedWidth(300.0f)
             .SetRequestedHeight(300.0f);

window.Add(animatedView);
```

## Playback Control

Control animation playback using `Play()`, `Pause()`, and `Stop()` methods. All playback methods return a reference to the view for fluent chaining.

```cpp
// Start or resume playback
animatedView.Play();

// Pause at the current frame
animatedView.Pause();

// Stop and reset based on StopBehavior
animatedView.Stop();
```

Check the current playback state using `GetPlayState()`:

```cpp
using namespace Dali::Ui::AnimatedImage;

if (animatedView.GetPlayState() == PlayState::PLAYING)
{
    // Animation is currently playing
}
```

The `PlayState` enumeration has three values:

- `PlayState::STOPPED` — Animation has stopped
- `PlayState::PLAYING` — Animation is playing
- `PlayState::PAUSED` — Animation is paused

### Loop Count

Configure how many times the animation repeats using `SetLoopCount()`:

```cpp
// Loop infinitely (default)
animatedView.SetLoopCount(-1);

// Play exactly 3 times
animatedView.SetLoopCount(3);

// Play once
animatedView.SetLoopCount(1);
```

Retrieve the current loop count with `GetLoopCount()`.

## Frame and Speed Control

### Stop Behavior

Control which frame displays when the animation stops using `SetStopBehavior()`:

```cpp
using namespace Dali::Ui::AnimatedImage;

// Show current frame when stopped
animatedView.SetStopBehavior(StopBehavior::CURRENT_FRAME);

// Show first frame when stopped
animatedView.SetStopBehavior(StopBehavior::FIRST_FRAME);

// Show last frame when stopped
animatedView.SetStopBehavior(StopBehavior::LAST_FRAME);
```

### Frame Navigation

Jump to a specific frame using `JumpToFrame()`:

```cpp
// Jump to frame 5
animatedView.JumpToFrame(5);
```

Query the current frame and total frame count:

```cpp
int currentFrame = animatedView.GetCurrentFrame();
int totalFrames = animatedView.GetTotalFrame();
```

### Playback Speed

Adjust animation speed using `SetFrameSpeedFactor()`. Values below 1.0 slow down the animation; values above 1.0 speed it up:

```cpp
// Half speed
animatedView.SetFrameSpeedFactor(0.5f);

// Normal speed (default)
animatedView.SetFrameSpeedFactor(1.0f);

// Double speed
animatedView.SetFrameSpeedFactor(2.0f);
```

### Frame Delay

Override the embedded frame timing using `SetFrameDelay()`. This sets a fixed delay in milliseconds between frames:

```cpp
// 500ms between frames
animatedView.SetFrameDelay(500);

// 1000ms between frames
animatedView.SetFrameDelay(1000);
```

## Image Sources and Caching

### Single Animated File

Load a single animated image file (GIF, WebP) using `SetResourceUrl()`:

```cpp
animatedView.SetResourceUrl("animation.gif");
```

### Frame Sequence (URL Array)

For frame-by-frame animation from individual image files, use `SetResourceUrls()` with a vector of URLs:

```cpp
Dali::Vector<Dali::String> urls;
urls.PushBack(Dali::String("frame-001.png"));
urls.PushBack(Dali::String("frame-002.png"));
urls.PushBack(Dali::String("frame-003.png"));
// ... add more frames

animatedView.SetResourceUrls(urls);
animatedView.Play();
```

### Batch and Cache Size

For URL array animations, configure pre-loading and caching behavior:

```cpp
// Pre-load 4 frames at a time
animatedView.SetBatchSize(4);

// Keep 10 frames in cache
animatedView.SetCacheSize(10);
```

## Visual Appearance

### Fitting Mode

Control how the image fits within the view bounds using `SetFittingMode()`:

```cpp
using namespace Dali::Ui::Image;

// Scale to fit, preserve aspect ratio
animatedView.SetFittingMode(FittingMode::FIT_KEEP_ASPECT_RATIO);

// Stretch to fill (default)
animatedView.SetFittingMode(FittingMode::FILL);

// Scale to cover, crop overflow
animatedView.SetFittingMode(FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// Center at original size
animatedView.SetFittingMode(FittingMode::CENTER);
```

### Image Color

Apply a color tint to the animation using `SetImageColor()`:

```cpp
// Apply red tint
animatedView.SetImageColor(UiColor(0xFF0000FF));

// Apply semi-transparent blue tint
animatedView.SetImageColor(UiColor(0x0000FF88));
```

### Sampling Mode

Configure the filtering algorithm used when scaling the image:

```cpp
using namespace Dali::Ui::Image;

animatedView.SetSamplingMode(SamplingMode::LINEAR);
animatedView.SetSamplingMode(SamplingMode::NEAREST);
animatedView.SetSamplingMode(SamplingMode::LANCZOS);
```

### Desired Size

Provide size hints to the image loader for memory optimization:

```cpp
animatedView.SetDesiredWidth(256);
animatedView.SetDesiredHeight(256);
```

### Pixel Area

Display a sub-region of the image using normalized coordinates (0.0 to 1.0):

```cpp
// Display the top-left quadrant
animatedView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));
```

## Alpha Masking

Apply an alpha mask to shape the visible region of the animation.

### Basic Masking

Set a mask image URL using `SetAlphaMaskUrl()`:

```cpp
animatedView.SetAlphaMaskUrl("mask.png");
```

### Crop to Mask

Control whether the view size adjusts to the mask bounds:

```cpp
// Crop image to mask bounding box
animatedView.SetCropToMask(true);

// Keep full image size, apply mask alpha only
animatedView.SetCropToMask(false);
```

### Masking Mode

Choose when masking is applied:

```cpp
using namespace Dali::Ui::Image;

// Apply mask during rendering (default)
animatedView.SetMaskingMode(MaskingType::MASKING_ON_RENDERING);

// Apply mask during loading (more efficient for static masks)
animatedView.SetMaskingMode(MaskingType::MASKING_ON_LOADING);
```

## Loading Configuration

### Load Policy

Control when the image resource is loaded:

```cpp
using namespace Dali::Ui::Image;

// Load immediately when created
animatedView.SetLoadPolicy(LoadPolicy::IMMEDIATE);

// Load when attached to the scene (default)
animatedView.SetLoadPolicy(LoadPolicy::ATTACHED);
```

### Release Policy

Control when the image texture is released from cache:

```cpp
using namespace Dali::Ui::Image;

// Release when detached from scene
animatedView.SetReleasePolicy(ReleasePolicy::DETACHED);

// Release when view is destroyed
animatedView.SetReleasePolicy(ReleasePolicy::DESTROYED);

// Never release from cache
animatedView.SetReleasePolicy(ReleasePolicy::NEVER);
```

### Synchronous Loading

Load the image on the main thread synchronously:

```cpp
animatedView.SetSynchronousLoading(true);
```

### Placeholder URL

Display a placeholder image while the main image loads:

```cpp
animatedView.SetPlaceholderUrl("placeholder.png");
```

### Load with View Size

Use the view size as a loading hint:

```cpp
animatedView.SetImageLoadWithViewSize(true);
```

## Signals

### Animation Finished Signal

Connect to `AnimationFinishedSignal()` to be notified when the animation completes all loops:

```cpp
animatedView.AnimationFinishedSignal().Connect(&controller, &MyController::OnAnimationFinished);
```

The callback signature is `void(View)`:

```cpp
void OnAnimationFinished(Dali::Ui::View view)
{
    // Animation has finished all loops
}
```

### Resource Ready Signal

Connect to `ResourceReadySignal()` to be notified when the image has finished loading:

```cpp
animatedView.ResourceReadySignal().Connect(&controller, &MyController::OnResourceReady);
```

The callback signature is `void(View)`:

```cpp
void OnResourceReady(Dali::Ui::View view)
{
    // Image is ready to display
    int totalFrames = animatedView.GetTotalFrame();
}
```

Check the loading status programmatically:

```cpp
using namespace Dali::Ui::Visual;

ResourceStatus status = animatedView.GetLoadingStatus();