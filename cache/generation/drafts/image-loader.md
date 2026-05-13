---
title: Image Loader
sidebar_label: Image Loader
category: uncategorized
---

# Image Loader

The Image Loader feature provides asynchronous image loading capabilities for dali-ui applications, allowing images to be loaded in background threads without blocking the main event loop.

## Table of Contents

- [Creating an AsyncImageLoader](#creating-an-asyncimageloader)
- [Loading Images](#loading-images)
- [Handling Load Completion](#handling-load-completion)
- [Canceling Load Operations](#canceling-load-operations)
- [Working with ImageUrl](#working-with-imageurl)

## Creating an AsyncImageLoader

The `Dali::Ui::AsyncImageLoader` class is the primary interface for loading images asynchronously. Create an instance using the static `New()` method:

```cpp
#include <dali-ui-foundation/public-api/image-loader/async-image-loader.h>

// Create an async image loader
Dali::Ui::AsyncImageLoader imageLoader = Dali::Ui::AsyncImageLoader::New();
```

The `AsyncImageLoader` is a handle class that manages the lifecycle of the underlying loader. You can safely copy or move the handle:

```cpp
// Copy construction
Dali::Ui::AsyncImageLoader copiedLoader = imageLoader;

// Move construction
Dali::Ui::AsyncImageLoader movedLoader = std::move(imageLoader);
```

## Loading Images

### Basic Image Loading

Use the `Load()` method to start loading an image from a URL. The method returns a task ID that identifies the loading operation:

```cpp
// Load an image with default settings
uint32_t taskId = imageLoader.Load("path/to/image.png");
```

### Loading with Dimensions

To load an image scaled to specific dimensions, pass the desired size:

```cpp
// Load image scaled to fit 200x200 pixels
uint32_t taskId = imageLoader.Load("path/to/image.png", Dali::ImageDimensions(200, 200));
```

### Loading with Full Options

For complete control over the loading process, specify all parameters:

```cpp
// Load with full control over sampling and orientation
uint32_t taskId = imageLoader.Load(
    "path/to/image.png",                    // URL
    Dali::ImageDimensions(200, 200),        // Target dimensions
    Dali::SamplingMode::BOX_THEN_LINEAR,    // Sampling mode
    true                                     // Enable orientation correction
);
```

The `samplingMode` parameter controls how pixels are sampled when scaling the image. The `orientationCorrection` parameter enables automatic reorientation based on EXIF metadata.

## Handling Load Completion

Connect to the `ImageLoadedSignal()` to receive notifications when image loading completes. Always connect the signal before calling `Load()` to ensure you receive callbacks:

```cpp
class ImageHandler : public Dali::ConnectionTracker
{
public:
    void OnImageLoaded(uint32_t taskId, Dali::PixelData pixelData)
    {
        if (pixelData)
        {
            // Image loaded successfully
            if (taskId == mExpectedTaskId)
            {
                // Process the loaded pixel data
                // Note: PixelData is automatically destroyed when it goes out of scope
            }
        }
        else
        {
            // Image loading failed
        }
    }

    uint32_t mExpectedTaskId{0};
};

// Usage
ImageHandler handler;
Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::New();

// Connect signal before loading
loader.ImageLoadedSignal().Connect(&handler, &ImageHandler::OnImageLoaded);

// Start loading
handler.mExpectedTaskId = loader.Load("path/to/image.png");
```

The signal callback receives:
- `uint32_t taskId`: The task ID returned by the corresponding `Load()` call
- `Dali::PixelData pixelData`: The loaded pixel data, or an invalid handle if loading failed

## Canceling Load Operations

### Canceling a Specific Task

Use `Cancel()` to cancel a specific loading operation by its task ID:

```cpp
uint32_t taskId = imageLoader.Load("large_image.png");

// Cancel if still queued
bool wasCanceled = imageLoader.Cancel(taskId);
if (wasCanceled)
{
    // Task was successfully removed from the queue
}
else
{
    // Task was already in progress or completed
}
```

`Cancel()` returns `true` if the task was successfully removed from the queue, or `false` if the task was already being processed or completed.

### Canceling All Tasks

To cancel all pending load operations at once:

```cpp
imageLoader.CancelAll();
```

This removes all queued loading tasks but does not affect tasks that have already started processing.

## Working with ImageUrl

`Dali::Ui::ImageUrl` provides a way to wrap external buffers and textures for use with visuals. It generates a URL string that can be used to reference the wrapped resource.

### Creating from a Texture

Wrap an existing texture with `ImageUrl::New()`:

```cpp
#include <dali-ui-foundation/public-api/image-loader/image-url.h>

// Create a texture
Dali::Texture texture = Dali::Texture::New(
    Dali::TextureType::TEXTURE_2D,
    Dali::Pixel::Format::RGBA8888,
    256, 256
);

// Wrap the texture with ImageUrl
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(texture, true);

// Get the URL string for use with visuals
const Dali::String& url = imageUrl.GetUrl();
```

The second parameter `preMultiplied` indicates whether the texture uses premultiplied alpha.

### Creating from an Encoded Image Buffer

Wrap encoded image data (such as PNG or JPEG in memory):

```cpp
// Create from encoded image buffer
Dali::EncodedImageBuffer encodedBuffer = /* ... */;

Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(encodedBuffer);

// Get the URL string
const Dali::String& url = imageUrl.GetUrl();
```

### Using ImageUrl with Visuals

The URL obtained from `GetUrl()` can be used with image visuals:

```cpp
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(texture);

// Use the URL with an image visual
Dali::Property::Map visualProperty;
visualProperty[Dali::Toolkit::Visual::Property::TYPE] = Dali::Toolkit::Visual::IMAGE;
visualProperty[Dali::Toolkit::ImageVisual::Property::URL] = imageUrl.GetUrl();
```

When the `ImageUrl` object is destroyed, the associated buffer is removed from the texture manager. If a visual still references the buffer, it will be cleaned up when the visual is destroyed.
