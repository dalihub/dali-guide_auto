---
title: Image Loader
sidebar_label: Image Loader
category: images-visuals
---

# Image Loader

The Image Loader feature provides asynchronous image loading capabilities for dali-ui applications. It allows loading images from URLs in background threads without blocking the main event loop, and provides utilities for generating URLs from various image sources.

## Table of Contents

- [Asynchronous Image Loading](#asynchronous-image-loading)
- [Loading Images with Dimensions and Sampling](#loading-images-with-dimensions-and-sampling)
- [Canceling Load Operations](#canceling-load-operations)
- [Image URL Generation](#image-url-generation)
- [Using ImageUrl with Encoded Buffers](#using-imageurl-with-encoded-buffers)

## Asynchronous Image Loading

The `Dali::Ui::AsyncImageLoader` class enables non-blocking image loading from URLs. Images are loaded in a worker thread, and the result is delivered through a signal callback. Each load operation returns a task ID that can be used to track or cancel the operation.

Create an `AsyncImageLoader` instance using the static `New()` method:

```cpp
Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::New();
```

Before initiating any load operations, connect to the `ImageLoadedSignal()` to receive callbacks when loading completes:

```cpp
class ImageHandler : public Dali::ConnectionTracker
{
public:
    void OnImageLoaded(uint32_t taskId, Dali::PixelData pixelData)
    {
        if (pixelData)
        {
            // Process the loaded pixel data
        }
    }
};

ImageHandler handler;
loader.ImageLoadedSignal().Connect(&handler, &ImageHandler::OnImageLoaded);
```

Start a load operation by calling `Load()` with a URL. The method returns a task ID for tracking:

```cpp
uint32_t taskId = loader.Load("path/to/image.png");
```

The signal callback receives both the task ID and the resulting `PixelData` object. Check the validity of `PixelData` to detect load failures.

## Loading Images with Dimensions and Sampling

`AsyncImageLoader` provides overloaded `Load()` methods to control the target dimensions and sampling mode.

To load an image with specific dimensions:

```cpp
Dali::ImageDimensions dimensions(256, 256);
uint32_t taskId = loader.Load("path/to/image.png", dimensions);
```

For finer control over the sampling and orientation:

```cpp
Dali::ImageDimensions dimensions(512, 512);
Dali::SamplingMode::Type samplingMode = Dali::SamplingMode::BOX_THEN_LINEAR;
bool orientationCorrection = true;

uint32_t taskId = loader.Load("path/to/image.png", dimensions, samplingMode, orientationCorrection);
```

The `dimensions` parameter specifies the maximum width and height to fit the loaded image. The `samplingMode` parameter controls the filtering method used when sampling pixels during resizing. Set `orientationCorrection` to `true` to respect orientation metadata in the image header.

## Canceling Load Operations

Use `Cancel()` to stop a specific loading task if it is still queued:

```cpp
bool canceled = loader.Cancel(taskId);
```

`Cancel()` returns `true` if the task was successfully removed from the queue. It returns `false` if the task is already in progress or completed.

To cancel all pending load operations:

```cpp
loader.CancelAll();
```

## Image URL Generation

The `Dali::Ui::ImageUrl` class wraps external image buffers and generates URLs that can be used with visuals. Use `ImageUrlUtils::GenerateUrl()` to create `ImageUrl` instances from various image sources.

Generate a URL from `PixelData`:

```cpp
Dali::PixelData pixelData = /* ... */;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(pixelData, false);

const Dali::String& url = imageUrl.GetUrl();
```

Generate a URL from a `FrameBuffer`:

```cpp
Dali::FrameBuffer frameBuffer = /* ... */;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(frameBuffer, Pixel::Format::RGBA8888, 512, 512);
```

Generate a URL from a `NativeImageInterface`:

```cpp
Dali::NativeImageInterfacePtr nativeImage = /* ... */;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(nativeImage, false);
```

The `preMultiplied` parameter indicates whether the image data has pre-multiplied alpha.

## Using ImageUrl with Encoded Buffers

`ImageUrl` can be created directly from an `EncodedImageBuffer`:

```cpp
Dali::EncodedImageBuffer encodedBuffer = /* ... */;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(encodedBuffer);

const Dali::String& url = imageUrl.GetUrl();
```

Alternatively, use `ImageUrlUtils::GenerateUrl()` for the same purpose:

```cpp
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(encodedBuffer);
```

The generated URL can be passed to visuals for rendering. When the `ImageUrl` object is destroyed, the associated buffer is removed from the texture manager if no other references exist.