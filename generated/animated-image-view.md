---
title: Animated ImageView
sidebar_label: Animated ImageView
category: images-visuals
---

# Animated ImageView

`AnimatedImageView` displays animated images such as GIF and animated WebP files, or a sequence of image frames provided as URLs.

## Table of Contents

- [Creating an AnimatedImageView](#creating-an-animatedimageview)
- [Loading Animated Images](#loading-animated-images)
- [Playback Control](#playback-control)
- [Animation Settings](#animation-settings)
- [Alpha Masking](#alpha-masking)
- [Loading and Caching](#loading-and-caching)
- [Signals](#signals)
- [Image Appearance](#image-appearance)

## Creating an AnimatedImageView

Create an `AnimatedImageView` using `AnimatedImageView::New()`. You can pass an optional URL to load an animated image immediately.

```cpp
using namespace Dali::Ui;

// Create without an initial image
AnimatedImageView animatedView = AnimatedImageView::New();

// Create with an initial image URL
AnimatedImageView animatedView = AnimatedImageView::New("images/spinner.gif");
```

Set the view size and add it to your view hierarchy:

```cpp
animatedView.SetRequestedWidth(200.0f)
             .SetRequestedHeight(200.0f);
window.Add(animatedView);
```

## Loading Animated Images

### Single Animated File

Use `SetResourceUrl()` to load a single animated image file such as GIF or WebP:

```cpp
animatedView.SetResourceUrl("animations/loading.webp");
```

### Frame Sequence (URL Array)

For frame-by-frame animation, provide a vector of image URLs using `SetResourceUrls()`. This is useful when you have individual frame images (e.g., PNG sequences):

```cpp
Dali::Vector<Dali::String> frameUrls;
frameUrls.PushBack(Dali::String("frames/frame-001.png"));
frameUrls.PushBack(Dali::String("frames/frame-002.png"));
frameUrls.PushBack(Dali::String("frames/frame-003.png"));
// ... add more frames
animatedView.SetResourceUrls(frameUrls);
```

When using URL arrays, configure batch loading and caching for optimal performance:

```cpp
animatedView.SetBatchSize(4);  // Load 4 frames per batch
animatedView.SetCacheSize(10); // Cache up to 10 frames
```

## Playback Control

Control animation playback using `Play()`, `Pause()`, `Stop()`, and `JumpToFrame()`.

### Play, Pause, and Stop

```cpp
// Start or resume playback
animatedView.Play();

// Pause at the current frame
animatedView.Pause();

// Stop playback (behavior depends on StopBehavior)
animatedView.Stop();
```

### Jump to a Specific Frame

Use `JumpToFrame()` to navigate to a specific frame index:

```cpp
// Jump to frame 5
animatedView.JumpToFrame(5);
```

### Query Playback State

Check the current state and frame position:

```cpp
Ui::AnimatedImage::PlayState state = animatedView.GetPlayState();
int currentFrame = animatedView.GetCurrentFrame();
int totalFrames = animatedView.GetTotalFrame();

if(state == Ui::AnimatedImage::PlayState::PLAYING)
{
  // Animation is actively playing
}
```

## Animation Settings

### Loop Count

Control how many times the animation repeats using `SetLoopCount()`. Use `-1` for infinite looping:

```cpp
// Loop infinitely
animatedView.SetLoopCount(-1);

// Play exactly 3 times
animatedView.SetLoopCount(3);

// Play once (no looping)
animatedView.SetLoopCount(1);
```

Note: A loop count of `0` means the animation will not play.

### Frame Speed Factor

Adjust playback speed using `SetFrameSpeedFactor()`. Values greater than `1.0f` speed up the animation, while values less than `1.0f` slow it down:

```cpp
// Half speed
animatedView.SetFrameSpeedFactor(0.5f);

// Normal speed (default)
animatedView.SetFrameSpeedFactor(1.0f);

// Double speed
animatedView.SetFrameSpeedFactor(2.0f);
```

### Frame Delay

Override the frame delay for URL array animations using `SetFrameDelay()`. The value is in milliseconds:

```cpp
// Display each frame for 500ms
animatedView.SetFrameDelay(500);
```

### Stop Behavior

Define where the animation stops when `Stop()` is called using `SetStopBehavior()`:

```cpp
using namespace Dali::Ui::AnimatedImage;

// Stop at the current frame
animatedView.SetStopBehavior(StopBehavior::CURRENT_FRAME);

// Stop and return to the first frame
animatedView.SetStopBehavior(StopBehavior::FIRST_FRAME);

// Stop and advance to the last frame
animatedView.SetStopBehavior(StopBehavior::LAST_FRAME);
```

## Alpha Masking

Apply an alpha mask to shape the visible area of the animated image.

### Basic Alpha Masking

Set a mask image URL using `SetAlphaMaskUrl()`:

```cpp
animatedView.SetAlphaMaskUrl("masks/circle-mask.png");
```

### Crop to Mask

Use `SetCropToMask()` to crop the image to the mask's bounding box:

```cpp
animatedView.SetCropToMask(true);
```

When `cropToMask` is `false`, the full image displays with the mask's alpha applied. When `true`, the image is cropped to the mask's bounding box.

### Masking Mode

Choose when masking occurs using `SetMaskingMode()`:

```cpp
using namespace Dali::Ui::Image;

// Apply mask during rendering (default)
animatedView.SetMaskingMode(MaskingType::MASKING_ON_RENDERING);

// Apply mask during loading (more efficient for static masks)
animatedView.SetMaskingMode(MaskingType::MASKING_ON_LOADING);
```

## Loading and Caching

### Load Policy

Control when the image loads using `SetLoadPolicy()`:

```cpp
using namespace Dali::Ui::Image;

// Load when the view is attached to the scene (default)
animatedView.SetLoadPolicy(LoadPolicy::ATTACHED);

// Load immediately, before adding to the scene
animatedView.SetLoadPolicy(LoadPolicy::IMMEDIATE);
```

### Release Policy

Control when image resources are released using `SetReleasePolicy()`:

```cpp
using namespace Dali::Ui::Image;

// Release when the view is detached from the scene
animatedView.SetReleasePolicy(ReleasePolicy::DETACHED);

// Release when the view is destroyed
animatedView.SetReleasePolicy(ReleasePolicy::DESTROYED);

// Never release automatically
animatedView.SetReleasePolicy(ReleasePolicy::NEVER);
```

### Synchronous Loading

For immediate loading that blocks until complete, use `SetSynchronousLoading()`:

```cpp
animatedView.SetSynchronousLoading(true);
```

Check the loading status with `GetLoadingStatus()`:

```cpp
Ui::Visual::ResourceStatus status = animatedView.GetLoadingStatus();
```

### Placeholder Image

Display a placeholder while the main image loads:

```cpp
animatedView.SetPlaceholderUrl("images/placeholder.png");
```

## Signals

### Animation Finished Signal

Connect to `AnimationFinishedSignal()` to be notified when the animation completes all its loops:

```cpp
animatedView.AnimationFinishedSignal().Connect(this, &MyClass::OnAnimationFinished);

// Handler signature
void OnAnimationFinished(Dali::Ui::View view)
{
  // Animation has finished all loops
}
```

### Resource Ready Signal

Connect to `ResourceReadySignal()` to be notified when the image resource is ready for display:

```cpp
animatedView.ResourceReadySignal().Connect(this, &MyClass::OnResourceReady);

// Handler signature
void OnResourceReady(Dali::Ui::View view)
{
  int totalFrames = animatedView.GetTotalFrame();
  // Resource is loaded and ready
}
```

## Image Appearance

### Fitting Mode

Control how the image fits within the view bounds using `SetFittingMode()`:

```cpp
using namespace Dali::Ui::Image;

// Scale to fit within bounds, preserving aspect ratio (may letterbox)
animatedView.SetFittingMode(FittingMode::FIT_KEEP_ASPECT_RATIO);

// Stretch to fill bounds, not preserving aspect ratio (default)
animatedView.SetFittingMode(FittingMode::FILL);

// Scale to fill bounds, preserving aspect ratio (may crop overflow)
animatedView.SetFittingMode(FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// Display at original size, centered
animatedView.SetFittingMode(FittingMode::CENTER);
```

### Sampling Mode

Control filtering quality using `SetSamplingMode()`:

```cpp
using namespace Dali::Ui::Image;

// Smooth filtering (default)
animatedView.SetSamplingMode(SamplingMode::BOX_THEN_LINEAR);

// Pixelated/sharp filtering
animatedView.SetSamplingMode(SamplingMode::NEAREST);
```

### Desired Size

Specify a target size for image loading, which can improve memory efficiency:

```cpp
animatedView.SetDesiredWidth(256);
animatedView.SetDesiredHeight(256);
```

### Image Color

Tint the image using `SetImageColor()`:

```cpp
// Apply a blue tint
animatedView.SetImageColor(UiColor(0.2f, 0.4f, 0.8f, 1.0f));

// Apply a semi-transparent overlay
animatedView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 0.5f));
```

### Pixel Area

Display a sub-region of the image using `SetPixelArea()`. The value is a `Vector4` containing `(x, y, width, height)` in normalized coordinates (0.0 to 1.0):

```cpp
// Display the top-left quarter of the image
animatedView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));
```

### Load with View Size

Enable loading the image at the view size for memory efficiency:

```cpp
animatedView.SetImageLoadWithViewSize(true);
```

### Premultiplied Alpha

Control premultiplied alpha handling:

```cpp
animatedView.SetPreMultipliedAlpha(true);
```
