-----BEGIN_REVISED_MARKDOWN-----
---
title: Animated Image View
sidebar_label: Animated Image View
category: views-components
---

# Animated Image View

`AnimatedImageView` is a View component for displaying animated image resources such as GIF and animated WebP files. It provides playback control, loop configuration, and frame-level manipulation.

## Table of Contents

- [Creating an AnimatedImageView](#creating-an-animatedimageview)
- [Playback Control](#playback-control)
- [Loop and Speed Configuration](#loop-and-speed-configuration)
- [Frame Sequences from URL Arrays](#frame-sequences-from-url-arrays)
- [Visual Appearance](#visual-appearance)
- [Alpha Masking](#alpha-masking)
- [Loading Policies](#loading-policies)
- [Signals](#signals)

## Creating an AnimatedImageView

Create an `AnimatedImageView` using the `New()` static method. You can pass an optional URL to load an animated image immediately.

```cpp
// Create with no initial image
AnimatedImageView animatedView = AnimatedImageView::New();

// Create with a GIF file
AnimatedImageView animatedView = AnimatedImageView::New("animations/spinner.gif");

// Set the image URL after creation
animatedView.SetResourceUrl("animations/loading.webp");
```

Set the view size and add it to your layout:

```cpp
AnimatedImageView animatedView = AnimatedImageView::New("animations/logo.gif")
  .SetRequestedWidth(200.0f)
  .SetRequestedHeight(200.0f);
parentLayout.Add(animatedView);
```

## Playback Control

Control animation playback using `Play()`, `Pause()`, and `Stop()` methods. All playback methods support fluent chaining.

```cpp
// Start or resume playback
animatedView.Play();

// Pause at the current frame
animatedView.Pause();

// Stop playback; the displayed frame depends on StopBehavior
animatedView.Stop();
```

### Jumping to a Specific Frame

Use `JumpToFrame()` to jump directly to a specific frame index:

```cpp
// Jump to frame 5
animatedView.JumpToFrame(5);
```

### Querying Playback State

Check the current playback state using `GetPlayState()`:

```cpp
Ui::AnimatedImage::PlayState state = animatedView.GetPlayState();
if (state == Ui::AnimatedImage::PlayState::PLAYING)
{
  // Animation is currently playing
}
```

The `PlayState` enum has three values:

- `Ui::AnimatedImage::PlayState::STOPPED` — Animation has stopped
- `Ui::AnimatedImage::PlayState::PLAYING` — Animation is playing
- `Ui::AnimatedImage::PlayState::PAUSED` — Animation is paused

### Stop Behavior

Configure which frame displays when the animation stops using `SetStopBehavior()`:

```cpp
// Show current frame when stopped
animatedView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::CURRENT_FRAME);

// Show first frame when stopped
animatedView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::FIRST_FRAME);

// Show last frame when stopped
animatedView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::LAST_FRAME);
```

### Frame Information

Query the current frame number and total frame count:

```cpp
int currentFrame = animatedView.GetCurrentFrame();
int totalFrames = animatedView.GetTotalFrame();
```

## Loop and Speed Configuration

### Loop Count

Set how many times the animation repeats using `SetLoopCount()`:

```cpp
// Loop infinitely
animatedView.SetLoopCount(-1);

// Play exactly 3 times
animatedView.SetLoopCount(3);

// Play once (no looping)
animatedView.SetLoopCount(1);
```

A value of `-1` means infinite looping. A value of `0` means the animation will not play.

### Playback Speed

Control animation speed using `SetFrameSpeedFactor()`:

```cpp
// Half speed
animatedView.SetFrameSpeedFactor(0.5f);

// Normal speed
animatedView.SetFrameSpeedFactor(1.0f);

// Double speed
animatedView.SetFrameSpeedFactor(2.0f);
```

Values between 0 and 1 slow down the animation; values above 1 speed it up.

### Custom Frame Delay

Override the frame delay embedded in the image file using `SetFrameDelay()`:

```cpp
// Set 500ms between frames
animatedView.SetFrameDelay(500);
```

## Frame Sequences from URL Arrays

Instead of a single animated file, you can provide an array of individual image URLs that play as a frame sequence:

```cpp
Dali::Vector<Dali::String> urls;
urls.PushBack(Dali::String("frames/frame-001.png"));
urls.PushBack(Dali::String("frames/frame-002.png"));
urls.PushBack(Dali::String("frames/frame-003.png"));
urls.PushBack(Dali::String("frames/frame-004.png"));

animatedView.SetResourceUrls(urls);
animatedView.Play();
```

### Batch and Cache Configuration

For URL arrays, configure batch loading and caching:

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
// Scale to fit, preserving aspect ratio
animatedView.SetFittingMode(Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

// Stretch to fill the view (default)
animatedView.SetFittingMode(Ui::Image::FittingMode::FILL);

// Scale to cover, cropping overflow
animatedView.SetFittingMode(Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// Center at original size
animatedView.SetFittingMode(Ui::Image::FittingMode::CENTER);
```

### Image Color

Apply a color tint to the image:

```cpp
animatedView.SetImageColor(UiColor(0xFF5500));
```

### Sampling Mode

Configure the filtering used when scaling:

```cpp
animatedView.SetSamplingMode(Ui::Image::SamplingMode::LINEAR);
```

### Desired Size Hints

Provide size hints to the image loader:

```cpp
animatedView.SetDesiredWidth(256);
animatedView.SetDesiredHeight(256);
```

### Pixel Area

Display a sub-region of the image using normalized coordinates:

```cpp
// Show the top-left quarter of the image
animatedView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));
```

## Alpha Masking

Apply an alpha mask to shape the visible region of the animation:

```cpp
animatedView.SetAlphaMaskUrl("masks/circle-mask.png");

// Crop the image to the mask bounds
animatedView.SetCropToMask(true);
```

### Masking Mode

Choose when masking is applied:

```cpp
// Apply mask during rendering
animatedView.SetMaskingMode(Ui::Image::MaskingType::MASKING_ON_RENDERING);

// Apply mask during loading
animatedView.SetMaskingMode(Ui::Image::MaskingType::MASKING_ON_LOADING);
```

## Loading Policies

### Load Policy

Control when the image starts loading:

```cpp
// Load immediately when the view is created
animatedView.SetLoadPolicy(Ui::Image::LoadPolicy::IMMEDIATE);

// Load when the view is attached to the scene
animatedView.SetLoadPolicy(Ui::Image::LoadPolicy::ATTACHED);
```

### Release Policy

Control when the image texture is released from cache:

```cpp
// Release when detached from scene
animatedView.SetReleasePolicy(Ui::Image::ReleasePolicy::DETACHED);

// Release when the view is destroyed
animatedView.SetReleasePolicy(Ui::Image::ReleasePolicy::DESTROYED);

// Never release from cache
animatedView.SetReleasePolicy(Ui::Image::ReleasePolicy::NEVER);
```

### Synchronous Loading

Load the image synchronously on the main thread:

```cpp
animatedView.SetSynchronousLoading(true);
```

### Placeholder Image

Display a placeholder while the main image loads:

```cpp
animatedView.SetPlaceholderUrl("placeholders/loading-placeholder.png");
```

## Signals

### Resource Ready Signal

The `ResourceReadySignal` is emitted when the image has finished loading:

```cpp
animatedView.ResourceReadySignal().Connect(this, &MyClass::OnResourceReady);

void OnResourceReady(View view)
{
  AnimatedImageView animatedView = AnimatedImageView::DownCast(view);
  int totalFrames = animatedView.GetTotalFrame();
  // Image is ready to display
}
```

### Animation Finished Signal

The `AnimationFinishedSignal` is emitted when the animation completes all loops:

```cpp
animatedView.AnimationFinishedSignal().Connect(this, &MyClass::OnAnimationFinished);

void OnAnimationFinished(View view)
{
  // Animation has finished all loops
}
```

### Checking Loading Status

Query the current loading status:

```cpp
Ui::Visual::ResourceStatus status = animatedView.GetLoadingStatus();
```
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# Animated Image View Prose Review

## Summary

Reviewed the Animated Image View developer guide draft against public headers (`animated-image-view.h`, `animated-image-enumerations.h`, `image-enumerations.h`) and sample code (`animated-image-view-example.cpp`, `image-alpha-mask-example.cpp`, `image-loading-policy-example.cpp`).

## Changes Made

### 1. Stop() Method Description (Playback Control section)

**Original:** "Stop and reset to the first frame"

**Revised:** "Stop playback; the displayed frame depends on StopBehavior"

**Source Evidence:** The header states `Stop()` "Stops playback of the animation and resets to the first frame," but `SetStopBehavior()` allows configuring whether the current, first, or last frame is shown when stopped. The original prose was misleading because the actual displayed frame depends on the configured `StopBehavior`.

### 2. Loop Count Default Removed (Loop and Speed Configuration section)

**Original:** "Loop infinitely (default)"

**Revised:** "Loop infinitely"

**Source Evidence:** The header documentation does not specify a default loop count value. Removed the "(default)" claim to avoid inaccuracy.

### 3. Playback Speed Default Removed (Loop and Speed Configuration section)

**Original:** "Normal speed (default)"

**Revised:** "Normal speed"

**Source Evidence:** The header states `SetFrameSpeedFactor()` has a "default: 1.0" parameter, but this refers to the default parameter value, not necessarily the initial state. Removed "(default)" for consistency.

### 4. Masking Mode Description Simplified (Alpha Masking section)

**Original:** "Apply mask during rendering (default)" and "Apply mask during loading (more efficient for static masks)"

**Revised:** "Apply mask during rendering" and "Apply mask during loading"

**Source Evidence:** The `MaskingType` enum in `image-enumerations.h` shows `MASKING_ON_RENDERING` as the first value, but the header does not explicitly state a default. The "(more efficient for static masks)" comment was not found in the public headers and was removed.

### 5. Load Policy Default Removed (Loading Policies section)

**Original:** "Load when the view is attached to the scene (default)"

**Revised:** "Load when the view is attached to the scene"

**Source Evidence:** The `LoadPolicy` enum has `IMMEDIATE = 0` as the first value. The header does not explicitly state which is the default. Removed the "(default)" claim.

### 6. Release Policy Default Removed (Loading Policies section)

**Original:** "Release when detached from scene (default)"

**Revised:** "Release when detached from scene"

**Source Evidence:** The `ReleasePolicy` enum has `DETACHED = 0` as the first value, suggesting it may be the default, but the header does not explicitly confirm this. Removed the "(default)" claim for accuracy.

## Verified Accurate Content

The following sections were verified against source code and found accurate:

- **Creating an AnimatedImageView**: `New()` signature and `SetResourceUrl()` usage confirmed
- **JumpToFrame()**: Method signature and usage confirmed
- **PlayState enum values**: STOPPED, PLAYING, PAUSED match `animated-image-enumerations.h`
- **StopBehavior enum values**: CURRENT_FRAME, FIRST_FRAME, LAST_FRAME match `animated-image-enumerations.h`
- **Frame Information**: `GetCurrentFrame()` and `GetTotalFrame()` signatures confirmed
- **Loop Count behavior**: -1 for infinite, 0 for no play, positive for exact count confirmed
- **Frame Speed Factor**: Range behavior confirmed in header comments
- **Frame Delay**: `SetFrameDelay()` and `GetFrameDelay()` return values confirmed
- **URL Arrays**: `SetResourceUrls()` and `GetResourceUrls()` signatures confirmed
- **Batch and Cache**: `SetBatchSize()`, `SetCacheSize()` signatures confirmed
- **Fitting Mode**: All four enum values match `image-enumerations.h`
- **Image Color**: `SetImageColor()` and `GetImageColor()` signatures confirmed
- **Sampling Mode**: `SetSamplingMode()` signature confirmed
- **Desired Size**: `SetDesiredWidth()`, `SetDesiredHeight()` signatures confirmed
- **Pixel Area**: `SetPixelArea()` signature and normalized coordinate format confirmed
- **Alpha Masking**: `SetAlphaMaskUrl()`, `SetCropToMask()` signatures confirmed
- **Synchronous Loading**: `SetSynchronousLoading()` signature confirmed
- **Placeholder**: `SetPlaceholderUrl()` signature confirmed
- **Signals**: `ResourceReadySignal()` and `AnimationFinishedSignal()` signatures and connection patterns confirmed in sample code
- **Loading Status**: `GetLoadingStatus()` return type confirmed

## Remaining Concerns

None. All prose has been verified against public headers and sample code.
-----END_PROSE_REVIEW_REPORT-----