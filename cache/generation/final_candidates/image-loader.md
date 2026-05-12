---
title: Image Loader
sidebar_label: Image Loader
category: uncategorized
---

# Image Loader

The Image Loader feature provides asynchronous image loading capabilities for dali-ui applications. It allows loading images from URLs in background threads without blocking the main event loop, delivering loaded pixel data through signals when complete.

## Table of Contents

- [Asynchronous Image Loading](#asynchronous-image-loading)
- [Loading Images with Options](#loading-images-with-options)
- [Canceling Load Operations](#canceling-load-operations)
- [Image URL from External Buffers](#image-url-from-external-buffers)

---

## Asynchronous Image Loading

The `Dali::Ui::AsyncImageLoader` class provides asynchronous image loading from URLs. Images are loaded in a worker thread, and the loaded `Dali::PixelData` is delivered through the `ImageLoadedSignal` when the operation completes.

### Creating an AsyncImageLoader

Create an `AsyncImageLoader` instance using the static `New()` method:

```cpp
#include <dali-ui-foundation/public-api/image-loader/async-image-loader.h>

// Create an asynchronous image loader
Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::New();
```

### Connecting to the Image Loaded Signal

Connect to `ImageLoadedSignal()` before calling `Load()` to ensure callbacks are received:

```cpp
class ImageHandler : public Dali::ConnectionTracker
{
public:
  void OnImageLoaded(uint32_t taskId, Dali::PixelData pixelData)
  {
    if (pixelData)
    {
      // Successfully loaded - use the pixel data
      uint32_t width = pixelData.GetWidth();
      uint32_t height = pixelData.GetHeight();
      // Process the loaded image...
    }
    else
    {
      // Loading failed - handle error
    }
  }

  void SetupLoader()
  {
    mLoader = Dali::Ui::AsyncImageLoader::New();
    mLoader.ImageLoadedSignal().Connect(this, &ImageHandler::OnImageLoaded);
  }

  Dali::Ui::AsyncImageLoader mLoader;
};
```

### Basic Image Loading

Use `Load()` with a URL to start loading an image. The method returns a task ID for tracking:

```cpp
// Load an image from a file path or URL
uint32_t taskId = loader.Load(Dali::String("image.png"));

// Store taskId to identify which image loaded in the callback
```

The signal callback receives both the task ID and the loaded `Dali::PixelData`:

```cpp
void OnImageLoaded(uint32_t taskId, Dali::PixelData pixelData)
{
  if (taskId == mFirstImageTaskId)
  {
    // Handle first image
  }
  else if (taskId == mSecondImageTaskId)
  {
    // Handle second image
  }
}
```

---

## Loading Images with Options

`AsyncImageLoader` provides overloaded `Load()` methods to control dimensions, sampling mode, and orientation correction.

### Loading with Target Dimensions

Specify target dimensions to resize the image during loading:

```cpp
#include <dali/public-api/adaptor-framework/image-options.h>

// Load image scaled to fit within 256x256 pixels
Dali::ImageDimensions targetSize(256, 256);
uint32_t taskId = loader.Load(Dali::String("large_image.png"), targetSize);
```

### Full Control with Sampling Mode and Orientation

For complete control over the loading process:

```cpp
// Load with custom dimensions, sampling mode, and orientation correction
Dali::ImageDimensions dimensions(512, 512);
Dali::SamplingMode::Type samplingMode = Dali::SamplingMode::BOX_THEN_LINEAR;
bool orientationCorrection = true;

uint32_t taskId = loader.Load(
    Dali::String("photo.jpg"),
    dimensions,
    samplingMode,
    orientationCorrection
);
```

### Available Sampling Modes

The `Dali::SamplingMode::Type` enum provides various filtering options:

| Mode | Description |
|------|-------------|
| `BOX` | Iteratively box filter to approximately the desired size |
| `NEAREST` | Read one input pixel per output pixel |
| `LINEAR` | Weighted average of four input pixels |
| `BOX_THEN_NEAREST` | Box filter, then nearest neighbor sampling |
| `BOX_THEN_LINEAR` | Box filter, then linear interpolation (default for `Load()` overloads without sampling mode) |
| `NO_FILTER` | No filtering performed |
| `LANCZOS` | Lanczos resampling algorithm |
| `BOX_THEN_LANCZOS` | Box filter, then Lanczos resampling |

---

## Canceling Load Operations

Cancel pending load operations to stop unnecessary work or handle cleanup.

### Canceling a Specific Load

Use `Cancel()` with the task ID returned from `Load()`:

```cpp
uint32_t taskId = loader.Load(Dali::String("image.png"));

// Later, if the load is no longer needed:
bool wasCanceled = loader.Cancel(taskId);
if (wasCanceled)
{
  // Task was still queued and has been removed
}
else
{
  // Task was already completed or in progress
}
```

### Canceling All Loads

Use `CancelAll()` to cancel all pending load operations:

```cpp
// Cancel all queued loading tasks
loader.CancelAll();
```

---

## Image URL from External Buffers

`Dali::Ui::ImageUrl` provides a way to create URLs from external buffers such as textures or encoded image data. This is useful when working with dynamically generated or externally managed image data.

### Creating an ImageUrl from a Texture

Create an `ImageUrl` from an existing `Dali::Texture`:

```cpp
#include <dali-ui-foundation/public-api/image-loader/image-url.h>

// Create from an existing texture
Dali::Texture texture = CreateTexture(); // Your texture creation logic
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(texture);

// Get the URL string for use with visuals or other components
const Dali::String& url = imageUrl.GetUrl();
```

### Creating an ImageUrl with Pre-multiplication

Specify whether the texture data is pre-multiplied:

```cpp
// Create with pre-multiplied alpha flag
bool preMultiplied = true;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(texture, preMultiplied);
```

### Creating an ImageUrl from Encoded Image Buffer

Create an `ImageUrl` from an `EncodedImageBuffer`:

```cpp
#include <dali/public-api/adaptor-framework/encoded-image-buffer.h>

// Create from encoded image buffer (e.g., JPEG or PNG data in memory)
Dali::EncodedImageBuffer encodedBuffer = GetEncodedBuffer(); // Your buffer source
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(encodedBuffer);

// Use the generated URL
const Dali::String& url = imageUrl.GetUrl();
```

### Lifetime Management

The `ImageUrl` manages the association between the external buffer and the texture manager. When the `ImageUrl` is destroyed, the buffer is removed from the texture manager if no other references (such as from visuals) exist.

```cpp
{
  Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(texture);
  // URL is valid and buffer is managed
  const Dali::String& url = imageUrl.GetUrl();
  // Use url with visuals...
}
// imageUrl destroyed - buffer removed from texture manager if no other references
```

### Downcasting

Use `DownCast()` to safely convert a `BaseHandle` to an `ImageUrl`:

```cpp
Dali::BaseHandle handle = GetSomeHandle(); // May or may not be ImageUrl
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::DownCast(handle);

if (imageUrl)
{
  // Successfully downcast - use the ImageUrl
  const Dali::String& url = imageUrl.GetUrl();
}
