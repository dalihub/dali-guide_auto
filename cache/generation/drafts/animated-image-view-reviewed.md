-----BEGIN_REVISED_MARKDOWN-----
---
title: Animated Image View
sidebar_label: Animated Image View
category: uncategorized
---

# Animated Image View

`AnimatedImageView` displays animated image resources such as GIF and WebP files, with full playback control including play, pause, stop, and frame-by-frame navigation.

## Table of Contents

- [Creating an AnimatedImageView](#creating-an-animatedimageview)
- [Playback Control](#playback-control)
- [Loop and Speed Configuration](#loop-and-speed-configuration)
- [Frame Array Animation](#frame-array-animation)
- [Visual Appearance](#visual-appearance)
- [Alpha Masking](#alpha-masking)
- [Loading Configuration](#loading-configuration)
- [Signals](#signals)

## Creating an AnimatedImageView

Create an `AnimatedImageView` using `AnimatedImageView::New()` with an optional URL to an animated image file:

```cpp
using namespace Dali::Ui;

// Create with a GIF file
AnimatedImageView animatedView = AnimatedImageView::New("animations/spinner.gif");

// Alternatively, create empty and set URL later
AnimatedImageView animatedViewWithUrl = AnimatedImageView::New();
animatedViewWithUrl.SetResourceUrl("animations/loading.webp");
```

Set the view size and add it to your layout:

```cpp
animatedView.SetRequestedWidth(200.0f)
             .SetRequestedHeight(200.0f);
```

## Playback Control

Control animation playback using `Play()`, `Pause()`, and `Stop()`:

```cpp
// Start or resume playback
animatedView.Play();

// Pause at current frame
animatedView.Pause();

// Stop and show frame based on StopBehavior
animatedView.Stop();
```

Jump directly to a specific frame using `JumpToFrame()`:

```cpp
// Jump to frame 5
animatedView.JumpToFrame(5);
```

Query the current playback state with `GetPlayState()`:

```cpp
using namespace Dali::Ui::AnimatedImage;

PlayState state = animatedView.GetPlayState();
if (state == PlayState::PLAYING)
{
  // Animation is actively playing
}
else if (state == PlayState::PAUSED)
{
  // Animation is paused
}
else if (state == PlayState::STOPPED)
{
  // Animation is stopped
}
```

Track the current frame and total frame count:

```cpp
int currentFrame = animatedView.GetCurrentFrame();
int totalFrames = animatedView.GetTotalFrame();
```

## Loop and Speed Configuration

### Loop Count

Set how many times the animation repeats using `SetLoopCount()`:

```cpp
// Loop infinitely (default)
animatedView.SetLoopCount(-1);

// Play exactly 3 times
animatedView.SetLoopCount(3);

// Play once
animatedView.SetLoopCount(1);
```

A value of 0 means the animation will not play.

### Frame Speed Factor

Control playback speed with `SetFrameSpeedFactor()`. Values below 1.0 slow down the animation; values above 1.0 speed it up:

```cpp
// Half speed
animatedView.SetFrameSpeedFactor(0.5f);

// Normal speed (default)
animatedView.SetFrameSpeedFactor(1.0f);

// Double speed
animatedView.SetFrameSpeedFactor(2.0f);
```

### Frame Delay Override

Override the embedded frame timing with `SetFrameDelay()`:

```cpp
// Set 500ms between frames
animatedView.SetFrameDelay(500);
```

### Stop Behavior

Configure which frame displays when the animation stops using `SetStopBehavior()`:

```cpp
using namespace Dali::Ui::AnimatedImage;

// Show current frame when stopped
animatedView.SetStopBehavior(StopBehavior::CURRENT_FRAME);

// Show first frame when stopped
animatedView.SetStopBehavior(StopBehavior::FIRST_FRAME);

// Show last frame when stopped
animatedView.SetStopBehavior(StopBehavior::LAST_FRAME);
```

## Frame Array Animation

Instead of a single animated file, provide an array of individual image URLs for frame-by-frame animation:

```cpp
Dali::Vector<Dali::String> frameUrls;
frameUrls.PushBack("frames/frame-001.png");
frameUrls.PushBack("frames/frame-002.png");
frameUrls.PushBack("frames/frame-003.png");
frameUrls.PushBack("frames/frame-004.png");

animatedView.SetResourceUrls(frameUrls);
```

Configure batch loading and caching for frame arrays:

```cpp
// Pre-load 4 frames at a time
animatedView.SetBatchSize(4);

// Cache up to 10 frames
animatedView.SetCacheSize(10);
```

## Visual Appearance

### Fitting Mode

Control how the image fits within the view bounds using `SetFittingMode()`. The default fitting mode is `FILL`:

```cpp
using namespace Dali::Ui::Image;

// Scale to fit, preserve aspect ratio
animatedView.SetFittingMode(FittingMode::FIT_KEEP_ASPECT_RATIO);

// Stretch to fill the view (default)
animatedView.SetFittingMode(FittingMode::FILL);

// Scale to cover, crop overflow
animatedView.SetFittingMode(FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// Center at original size
animatedView.SetFittingMode(FittingMode::CENTER);
```

### Image Color

Apply a color tint to the animation:

```cpp
// Apply blue tint
animatedView.SetImageColor(UiColor(0x4A90E2));
```

### Sampling Mode

Set the filtering mode used when scaling:

```cpp
using namespace Dali::Ui::Image;

// Smooth scaling
animatedView.SetSamplingMode(SamplingMode::LINEAR);

// Pixelated appearance
animatedView.SetSamplingMode(SamplingMode::NEAREST);

// High quality
animatedView.SetSamplingMode(SamplingMode::LANCZOS);
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
// Show the top-left quadrant
animatedView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));
```

## Alpha Masking

Apply an alpha mask to shape the visible region of the animation:

```cpp
// Set the mask image
animatedView.SetAlphaMaskUrl("masks/circle.png");

// Crop output to mask bounds
animatedView.SetCropToMask(true);
```

Choose when masking is applied using `SetMaskingMode()`:

```cpp
using namespace Dali::Ui::Image;

// Apply mask during rendering (can be changed dynamically)
animatedView.SetMaskingMode(MaskingType::MASKING_ON_RENDERING);

// Apply mask during loading (more efficient, cannot be changed)
animatedView.SetMaskingMode(MaskingType::MASKING_ON_LOADING);
```

## Loading Configuration

### Load Policy

Control when the image loads using `SetLoadPolicy()`:

```cpp
using namespace Dali::Ui::Image;

// Load immediately when created
animatedView.SetLoadPolicy(LoadPolicy::IMMEDIATE);

// Load when attached to the scene (default)
animatedView.SetLoadPolicy(LoadPolicy::ATTACHED);
```

### Release Policy

Control when the texture is released from cache using `SetReleasePolicy()`:

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

Load the image on the main thread, blocking until complete:

```cpp
animatedView.SetSynchronousLoading(true);
```

### Placeholder Image

Display a placeholder while the main image loads:

```cpp
animatedView.SetPlaceholderUrl("placeholders/loading.png");
```

## Signals

### Animation Finished Signal

Receive a callback when the animation completes all loops:

```cpp
animatedView.AnimationFinishedSignal().Connect(this, &MyClass::OnAnimationFinished);

// Handler signature
void OnAnimationFinished(Dali::Ui::View view)
{
  // Animation has finished all loops
}
```

### Resource Ready Signal

Receive a callback when the image finishes loading:

```cpp
animatedView.ResourceReadySignal().Connect(this, &MyClass::OnResourceReady);

// Handler signature
void OnResourceReady(Dali::Ui::View view)
{
  // Image is ready to display
  int totalFrames = animatedView.GetTotalFrame();
}
```

Check the loading status at any time:

```cpp
using namespace Dali::Ui::Visual;

ResourceStatus status = animatedView.GetLoadingStatus();
```
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# Animated Image View Prose Review

## Summary

Reviewed the AnimatedImageView guide draft against public headers (`animated-image-view.h`, `animated-image-enumerations.h`, `image-enumerations.h`) and sample files (`animated-image-view-example.cpp`, `image-alpha-mask-example.cpp`, `image-loading-policy-example.cpp`).

## Changes Made

### 1. Creating an AnimatedImageView - Variable Naming
**Location:** First code block  
**Original:**
```cpp
// Create with a GIF file
AnimatedImageView animatedView = AnimatedImageView::New("animations/spinner.gif");

// Create empty and set URL later
AnimatedImageView animatedView = AnimatedImageView::New();
animatedView.SetResourceUrl("animations/loading.webp");
```
**Revised:**
```cpp
// Create with a GIF file
AnimatedImageView animatedView = AnimatedImageView::New("animations/spinner.gif");

// Alternatively, create empty and set URL later
AnimatedImageView animatedViewWithUrl = AnimatedImageView::New();
animatedViewWithUrl.SetResourceUrl("animations/loading.webp");
```
**Reason:** Two variables with the same name in the same code block is confusing for readers, even though it is valid C++ (variable shadowing). Renamed the second variable to make the example clearer.

### 2. Playback Control - Stop() Description
**Location:** Playback Control section, comment in code block  
**Original:** `// Stop and reset based on StopBehavior`  
**Revised:** `// Stop and show frame based on StopBehavior`  
**Reason:** The header documentation for `Stop()` states it "stops playback of the animation and resets to the first frame." However, `StopBehavior` enum documentation clearly states it controls which frame is shown when stopped (CURRENT_FRAME, FIRST_FRAME, or LAST_FRAME). The revised wording is more accurate and aligns with the `StopBehavior` enum documentation.

### 3. Loop Count - Added Missing Value
**Location:** Loop and Speed Configuration > Loop Count section  
**Added:** "A value of 0 means the animation will not play."  
**Source Evidence:** `animated-image-view.h` line 152-153: "A value of 0 means the animation will not play."  
**Reason:** The header explicitly documents this behavior, which is useful information for developers.

### 4. Fitting Mode - Added Default Value
**Location:** Visual Appearance > Fitting Mode section  
**Added:** "The default fitting mode is `FILL`:" in the prose and updated the code comment to indicate default.  
**Source Evidence:** `animated-image-view.h` line 261: "The default fitting mode is Ui::Image::FittingMode::FILL (stretch to fill)."

## Verified Accurate (No Changes Needed)

The following were verified against public headers and samples:

- **Enum values:** All `PlayState`, `StopBehavior`, `FittingMode`, `SamplingMode`, `LoadPolicy`, `ReleasePolicy`, and `MaskingType` enum values are correctly documented.
- **Method signatures:** All setter methods return `AnimatedImageView&` for fluent chaining as documented.
- **Signal handler signatures:** `AnimationFinishedSignal` and `ResourceReadySignal` use `Signal<void(View)>` as shown in the header.
- **Frame array animation:** `SetResourceUrls()` correctly uses `Dali::Vector<Dali::String>` as shown in the header and samples.
- **Alpha masking:** `SetAlphaMaskUrl()`, `SetCropToMask()`, and `SetMaskingMode()` are correctly documented.
- **Loading configuration:** `SetLoadPolicy()`, `SetReleasePolicy()`, `SetSynchronousLoading()`, and `SetPlaceholderUrl()` are correctly documented.

## Remaining Considerations

1. **SetImageLoadWithViewSize()**: This method exists in the public API but is not documented in the guide. It sets "whether the image is loaded synchronously at the current view size." This is a minor feature that could be added if needed, but was omitted to avoid adding filler content.

2. **SetDepthIndex()**: This method exists in the public API but is not documented. It appears to be a specialized rendering feature.

3. **SetPreMultipliedAlpha()**: This method exists in the public API but is not documented. It's an advanced rendering option.

4. **Property system**: The guide correctly focuses on typed setters rather than the property system (`Handle::GetProperty()`/`SetProperty()`), which is appropriate for an app-facing guide.

## Conclusion

The draft was accurate and well-structured. Only minor corrections were needed:
- Fixed confusing variable naming in the first example
- Clarified Stop() behavior to align with StopBehavior enum documentation
- Added missing documentation for loop count value 0
- Added default value for fitting mode

All code examples were verified against public headers and sample files.
-----END_PROSE_REVIEW_REPORT-----