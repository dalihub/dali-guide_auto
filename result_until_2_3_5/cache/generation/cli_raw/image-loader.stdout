---
title: Image Loader
sidebar_label: Image Loader
category: images-visuals
---

# Image Loader

`Dali::Ui::AsyncImageLoader` loads image pixel data from a URL without blocking the main event thread.

## Table of Contents

- [Loading Images Asynchronously](#loading-images-asynchronously)
- [Handling Loaded Pixel Data](#handling-loaded-pixel-data)
- [Tracking and Canceling Work](#tracking-and-canceling-work)
- [Using ImageUrl for External Image Resources](#using-imageurl-for-external-image-resources)
- [Handle Ownership and Passing Loader Objects](#handle-ownership-and-passing-loader-objects)

## Loading Images Asynchronously

Create a loader with `Dali::Ui::AsyncImageLoader::New`, connect `Dali::Ui::AsyncImageLoader::ImageLoadedSignal`, then start loading with `Dali::Ui::AsyncImageLoader::Load`. The load call returns a `uint32_t` task id, which you use later to match the completion callback or cancel the queued task.

Connect the signal before calling `Dali::Ui::AsyncImageLoader::Load`, because completion can be reported as soon as the load is ready.

```cpp
#include <dali-ui-foundation/public-api/image-loader/async-image-loader.h>

class ThumbnailView : public Dali::Ui::View
{
public:
  void StartLoading(const Dali::String& imageUrl, Dali::ImageDimensions dimensions)
  {
    mImageLoader = Dali::Ui::AsyncImageLoader::New();
    mImageLoader.ImageLoadedSignal().Connect(this, &ThumbnailView::OnImageLoaded);

    mLoadingTaskId = mImageLoader.Load(imageUrl, dimensions);
  }

private:
  void OnImageLoaded(uint32_t loadedTaskId, Dali::PixelData pixelData)
  {
    if(loadedTaskId != mLoadingTaskId)
    {
      return;
    }

    if(pixelData)
    {
      // Use the loaded pixels to update this View's image presentation.
    }
    else
    {
      // Handle a failed image load for this View.
    }
  }

  Dali::Ui::AsyncImageLoader mImageLoader;
  uint32_t                   mLoadingTaskId{0u};
};
```

`Dali::Ui::AsyncImageLoader::Load` with `ImageDimensions` fits the loaded image to the requested width and height. For this overload, the public implementation uses the loader defaults for sampling and orientation correction.

## Handling Loaded Pixel Data

`Dali::Ui::AsyncImageLoader::ImageLoadedSignal` returns `Dali::Ui::AsyncImageLoader::ImageLoadedSignalType&`. Its callback signature is:

```cpp
void Callback(uint32_t loadingTaskId, Dali::PixelData pixelData);
```

The `loadingTaskId` is the value returned by `Dali::Ui::AsyncImageLoader::Load`. Check it when a view can have more than one request in flight.

```cpp
class GalleryCellView : public Dali::Ui::View
{
public:
  void QueuePrimaryAndFallback(const Dali::String& primaryUrl,
                               const Dali::String& fallbackUrl,
                               Dali::ImageDimensions dimensions)
  {
    mLoader = Dali::Ui::AsyncImageLoader::New();
    mLoader.ImageLoadedSignal().Connect(this, &GalleryCellView::OnImageLoaded);

    mPrimaryTaskId  = mLoader.Load(primaryUrl, dimensions);
    mFallbackTaskId = mLoader.Load(fallbackUrl, dimensions);
  }

private:
  void OnImageLoaded(uint32_t taskId, Dali::PixelData pixelData)
  {
    if(!pixelData)
    {
      return;
    }

    if(taskId == mPrimaryTaskId)
    {
      // Apply primary image pixels to this View.
      return;
    }

    if(taskId == mFallbackTaskId)
    {
      // Apply fallback image pixels to this View.
      return;
    }
  }

  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t                   mPrimaryTaskId{0u};
  uint32_t                   mFallbackTaskId{0u};
};
```

A valid `Dali::PixelData` in the signal means the image data is available to the callback. If the `Dali::PixelData` handle is not valid, treat the load as failed for that task.

## Tracking and Canceling Work

Use `Dali::Ui::AsyncImageLoader::Cancel` when you only want to cancel one queued request. It takes the task id returned by `Dali::Ui::AsyncImageLoader::Load` and returns `true` when the task was removed from the queue. A `false` return means the task could not be canceled because it was already being processed.

```cpp
class ImageRowView : public Dali::Ui::View
{
public:
  void LoadPreview(const Dali::String& url, Dali::ImageDimensions dimensions)
  {
    mLoader = Dali::Ui::AsyncImageLoader::New();
    mLoader.ImageLoadedSignal().Connect(this, &ImageRowView::OnPreviewLoaded);

    mPreviewTaskId = mLoader.Load(url, dimensions);
  }

  bool CancelPreview()
  {
    if(mPreviewTaskId == 0u)
    {
      return false;
    }

    const bool canceled = mLoader.Cancel(mPreviewTaskId);
    if(canceled)
    {
      mPreviewTaskId = 0u;
    }

    return canceled;
  }

private:
  void OnPreviewLoaded(uint32_t taskId, Dali::PixelData pixelData)
  {
    if(taskId == mPreviewTaskId && pixelData)
    {
      // Update this View with the preview image pixels.
    }
  }

  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t                   mPreviewTaskId{0u};
};
```

Use `Dali::Ui::AsyncImageLoader::CancelAll` when the owning view is changing content and no queued loads are still relevant.

```cpp
class SearchResultsView : public Dali::Ui::View
{
public:
  void ClearResults()
  {
    mLoader.CancelAll();
  }

private:
  Dali::Ui::AsyncImageLoader mLoader{Dali::Ui::AsyncImageLoader::New()};
};
```

## Using ImageUrl for External Image Resources

`Dali::Ui::ImageUrl` wraps an external image resource as a URL-like handle. Create it with `Dali::Ui::ImageUrl::New` from an `EncodedImageBuffer`, then retrieve the generated string with `Dali::Ui::ImageUrl::GetUrl`.

The `Dali::Ui::ImageUrl` handle owns the app-facing lifetime of that generated URL. When the handle is destroyed, the associated buffer can be released from the texture manager once no visual is still using it.

```cpp
#include <dali-ui-foundation/public-api/image-loader/image-url.h>

class EncodedImageView : public Dali::Ui::View
{
public:
  Dali::String RegisterEncodedImage(const Dali::EncodedImageBuffer& encodedImageBuffer)
  {
    mImageUrl = Dali::Ui::ImageUrl::New(encodedImageBuffer);
    return mImageUrl.GetUrl();
  }

private:
  Dali::Ui::ImageUrl mImageUrl;
};
```

Use `Dali::Ui::ImageUrl::GetUrl` when another dali-ui image-facing API needs a URL string. Keep the `Dali::Ui::ImageUrl` handle alive for as long as the view still depends on the generated URL.

## Handle Ownership and Passing Loader Objects

`Dali::Ui::AsyncImageLoader` and `Dali::Ui::ImageUrl` are handle types. They can be copied or moved, and their destructors clean up the handle object when the last relevant owner goes out of scope.

Use move construction or move assignment when transferring ownership between view-level objects.

```cpp
class LoaderOwnerView : public Dali::Ui::View
{
public:
  void SetLoader(Dali::Ui::AsyncImageLoader loader)
  {
    mLoader = std::move(loader);
  }

  Dali::Ui::AsyncImageLoader TakeLoader()
  {
    return std::move(mLoader);
  }

private:
  Dali::Ui::AsyncImageLoader mLoader;
};
```

When a generic handle is known to contain an image-loader object, use `Dali::Ui::AsyncImageLoader::DownCast` or `Dali::Ui::ImageUrl::DownCast` to recover the typed handle.

```cpp
Dali::Ui::AsyncImageLoader GetTypedLoader(Dali::BaseHandle handle)
{
  return Dali::Ui::AsyncImageLoader::DownCast(handle);
}

Dali::Ui::ImageUrl GetTypedImageUrl(Dali::BaseHandle handle)
{
  return Dali::Ui::ImageUrl::DownCast(handle);
}
```
