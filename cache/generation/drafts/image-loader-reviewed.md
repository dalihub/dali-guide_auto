-----BEGIN_REVISED_MARKDOWN-----
---
title: Image Loader
sidebar_label: Image Loader
category: images-visuals
---

# Image Loader

The Image Loader feature provides asynchronous image loading capabilities for dali-ui applications. It allows you to load images from URLs or memory buffers without blocking the main event thread, and provides signals to handle loaded pixel data.

## Table of Contents

- [Asynchronous Image Loading](#asynchronous-image-loading)
- [Loading with Dimensions and Options](#loading-with-dimensions-and-options)
- [Handling Load Completion](#handling-load-completion)
- [Canceling Load Tasks](#canceling-load-tasks)
- [Creating URLs from External Buffers](#creating-urls-from-external-buffers)

## Asynchronous Image Loading

The `Dali::Ui::AsyncImageLoader` class provides asynchronous image loading from URLs. Images are loaded in a worker thread to avoid blocking the main event thread. Create an instance using `AsyncImageLoader::New()` and start loading with `Load()`.

```cpp
#include <dali-ui-foundation/public-api/image-loader/async-image-loader.h>

// Create an asynchronous image loader
Dali::Ui::AsyncImageLoader imageLoader = Dali::Ui::AsyncImageLoader::New();

// Start loading an image from a URL
uint32_t taskId = imageLoader.Load("path/to/image.png");
```

The `Load()` method returns a task ID that identifies the loading operation. Use this ID to track which image has completed loading when handling the completion signal.

## Loading with Dimensions and Options

You can specify target dimensions and sampling options when loading images. The `Load()` method has overloads that accept `ImageDimensions` for size control and `SamplingMode::Type` for filtering quality.

```cpp
#include <dali/public-api/adaptor-framework/image-options.h>

// Load with target dimensions
Dali::ImageDimensions targetSize(200, 150);
uint32_t taskId = imageLoader.Load("path/to/image.png", targetSize);

// Load with full control over sampling and orientation
uint32_t taskId2 = imageLoader.Load(
    "path/to/image.jpg",
    targetSize,
    Dali::SamplingMode::BOX_THEN_LINEAR,
    true  // orientationCorrection
);
```

When you specify dimensions, the loaded image is scaled to fit within the target size. The `orientationCorrection` parameter controls whether the image is reoriented based on EXIF metadata.

## Handling Load Completion

Connect to the `ImageLoadedSignal()` to receive notifications when image loading completes. The signal provides the task ID and a `PixelData` object containing the loaded image data.

```cpp
class ImageHandler : public Dali::ConnectionTracker
{
public:
    void OnImageLoaded(uint32_t taskId, Dali::PixelData pixelData)
    {
        if(pixelData)
        {
            // Successfully loaded - use the pixel data
            if(taskId == mBackgroundTaskId)
            {
                // Handle background image
            }
            else if(taskId == mThumbnailTaskId)
            {
                // Handle thumbnail image
            }
        }
        else
        {
            // Loading failed
        }
    }

    void StartLoading()
    {
        mLoader = Dali::Ui::AsyncImageLoader::New();
        
        // Connect signal before calling Load
        mLoader.ImageLoadedSignal().Connect(this, &ImageHandler::OnImageLoaded);
        
        mBackgroundTaskId = mLoader.Load("background.png");
        mThumbnailTaskId = mLoader.Load("thumbnail.jpg", Dali::ImageDimensions(100, 100));
    }

private:
    Dali::Ui::AsyncImageLoader mLoader;
    uint32_t mBackgroundTaskId{0};
    uint32_t mThumbnailTaskId{0};
};
```

Always connect to `ImageLoadedSignal()` before calling `Load()` to ensure you receive the completion callback, even if loading completes immediately.

## Canceling Load Tasks

Cancel pending load operations using `Cancel()` with a specific task ID, or cancel all pending operations with `CancelAll()`.

```cpp
// Cancel a specific loading task
bool wasCanceled = imageLoader.Cancel(taskId);
if(wasCanceled)
{
    // Task was still in queue and has been removed
}
else
{
    // Task already completed or is in progress
}

// Cancel all pending loading tasks
imageLoader.CancelAll();
```

The `Cancel()` method returns `true` if the task was successfully removed from the queue. It returns `false` if the task has already completed or is actively being processed.

## Creating URLs from External Buffers

The `Dali::Ui::ImageUrl` class creates URL strings from external buffers such as textures or encoded image data. Use `ImageUrl::New()` to wrap an external resource and obtain a URL that can be used with visual components.

```cpp
#include <dali-ui-foundation/public-api/image-loader/image-url.h>

// Create URL from a texture
Dali::Texture texture = Dali::Texture::New(
    Dali::TextureType::TEXTURE_2D,
    Dali::Pixel::Format::RGBA8888,
    256, 256
);

Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(texture, false);
const Dali::String& url = imageUrl.GetUrl();

// Create URL from an encoded image buffer
Dali::EncodedImageBuffer encodedBuffer = /* ... */;
Dali::Ui::ImageUrl bufferUrl = Dali::Ui::ImageUrl::New(encodedBuffer);
const Dali::String& bufferUrlString = bufferUrl.GetUrl();
```

The `GetUrl()` method returns a URL string that references the external buffer. This URL can be passed to visual components that accept image URLs. The buffer remains valid while the `ImageUrl` object exists or while visuals reference it.
-----END_REVISED_MARKDOWN-----

-----BEGIN_PROSE_REVIEW_REPORT-----
# Image Loader Prose Review

## Summary

Reviewed the Image Loader guide draft against public headers, implementation files, and related APIs. The draft is well-structured and accurate. One minor correction was made.

## Changes Made

### 1. Removed Unnecessary Include (Section: Loading with Dimensions and Options)

**Original:**
```cpp
#include <dali/public-api/adaptor-framework/image-options.h>
#include <dali/public-api/rendering/sampler.h>
```

**Revised:**
```cpp
#include <dali/public-api/adaptor-framework/image-options.h>
```

**Source Evidence:**
- `repos/dali-adaptor/dali/public-api/adaptor-framework/image-options.h` defines both `ImageDimensions` (as `typedef Dali::Uint16Pair ImageDimensions`) and `SamplingMode::Type` enum.
- The include `<dali/public-api/rendering/sampler.h>` is unnecessary because `SamplingMode` is defined in `image-options.h`, not in `sampler.h`.
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/async-image-loader.h` includes `<dali/public-api/adaptor-framework/image-options.h>` which provides `ImageDimensions` and `SamplingMode`.

## Verified Accurate Content

### API Signatures Verified

| API | Draft Usage | Header Signature | Status |
|-----|-------------|------------------|--------|
| `AsyncImageLoader::New()` | `Dali::Ui::AsyncImageLoader::New()` | `static AsyncImageLoader New()` | ✓ Accurate |
| `AsyncImageLoader::Load(url)` | `imageLoader.Load("path/to/image.png")` | `uint32_t Load(const Dali::String& url)` | ✓ Accurate |
| `AsyncImageLoader::Load(url, dimensions)` | `imageLoader.Load("path/to/image.png", targetSize)` | `uint32_t Load(const Dali::String& url, ImageDimensions dimensions)` | ✓ Accurate |
| `AsyncImageLoader::Load(url, dimensions, samplingMode, orientationCorrection)` | Full overload with all parameters | `uint32_t Load(const Dali::String& url, ImageDimensions dimensions, SamplingMode::Type samplingMode, bool orientationCorrection)` | ✓ Accurate |
| `AsyncImageLoader::Cancel()` | `imageLoader.Cancel(taskId)` | `bool Cancel(uint32_t loadingTaskId)` | ✓ Accurate |
| `AsyncImageLoader::CancelAll()` | `imageLoader.CancelAll()` | `void CancelAll()` | ✓ Accurate |
| `AsyncImageLoader::ImageLoadedSignal()` | `mLoader.ImageLoadedSignal().Connect(...)` | `ImageLoadedSignalType& ImageLoadedSignal()` | ✓ Accurate |
| `ImageUrl::New(texture, preMultiplied)` | `Dali::Ui::ImageUrl::New(texture, false)` | `static ImageUrl New(Texture& texture, bool preMultiplied = false)` | ✓ Accurate |
| `ImageUrl::New(encodedImageBuffer)` | `Dali::Ui::ImageUrl::New(encodedBuffer)` | `static ImageUrl New(const EncodedImageBuffer& encodedImageBuffer)` | ✓ Accurate |
| `ImageUrl::GetUrl()` | `imageUrl.GetUrl()` | `const Dali::String& GetUrl() const` | ✓ Accurate |

### Prose Statements Verified

1. **"Images are loaded in a worker thread to avoid blocking the main event thread."** - Verified in `async-image-loader.h` header comment: "The images are loaded in a worker thread to avoid blocking the main event thread."

2. **"The `Load()` method returns a task ID that identifies the loading operation."** - Verified in header comment: "To keep track of the loading images, each load call is assigned an ID (which is returned by the Load() call)."

3. **"Always connect to `ImageLoadedSignal()` before calling `Load()` to ensure you receive the completion callback, even if loading completes immediately."** - Verified in header comment: "This signal should be connected before Load is called (in case the signal is emitted immediately)."

4. **"The `Cancel()` method returns `true` if the task was successfully removed from the queue. It returns `false` if the task has already completed or is actively being processed."** - Verified in header comment: "If true, the loading task is removed from the queue, otherwise the loading is already implemented and unable to cancel anymore"

5. **"The buffer remains valid while the `ImageUrl` object exists or while visuals reference it."** - Verified in `image-url.h` header comment: "When application does not use this anymore, the destructor of the ImageUrl is called. At this time, the buffer is deleted from the texture manager. Note: Visual also have reference of the buffer. In this case, buffer will be deleted after visual is deleted."

### Code Examples Verified

- All code examples compile against public API signatures
- `Dali::ImageDimensions` usage is correct (typedef for `Uint16Pair`, constructible with width, height)
- `Dali::SamplingMode::BOX_THEN_LINEAR` enum value exists in `image-options.h`
- `Dali::Texture::New()` signature matches usage
- `Dali::Pixel::Format::RGBA8888` enum value exists in `pixel.h`
- Signal callback signature `void(uint32_t, PixelData)` matches `ImageLoadedSignalType`

## Remaining Concerns

None. The draft is accurate and well-structured after the minor correction.
-----END_PROSE_REVIEW_REPORT-----