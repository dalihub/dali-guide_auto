---
title: Image Loader
sidebar_label: Image Loader
category: images-visuals
---

# Image Loader

`Dali::Ui::AsyncImageLoader` loads image pixel data without blocking the dali-ui application flow, while `Dali::Ui::ImageUrl` gives encoded image buffers a URL-style handle for image use.

## Table of Contents

- [Loading images asynchronously](#loading-images-asynchronously)
- [Tracking completion with task IDs](#tracking-completion-with-task-ids)
- [Cancelling queued image work](#cancelling-queued-image-work)
- [Using `ImageUrl` for encoded buffers](#using-imageurl-for-encoded-buffers)
- [Working with handles](#working-with-handles)

## Loading images asynchronously

Use `Dali::Ui::AsyncImageLoader::New()` to create the loader. A load request is started with `Dali::Ui::AsyncImageLoader::Load()`, which returns a task ID. In a dali-ui app, keep this loader alongside the `Dali::Ui::View` or controller that will consume the loaded image data.

Connect `Dali::Ui::AsyncImageLoader::ImageLoadedSignal()` before calling `Load()`. The signal may be emitted quickly, so connecting first avoids missing the completion callback.

```cpp
class ImageLoaderController
{
public:
  ImageLoaderController()
  : mLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mLoader.ImageLoadedSignal().Connect(this, &ImageLoaderController::OnImageLoaded);
  }

  uint32_t LoadImage(const Dali::String& url, Dali::ImageDimensions dimensions)
  {
    return mLoader.Load(url, dimensions);
  }

private:
  void OnImageLoaded(uint32_t loadingTaskId, Dali::PixelData pixelData)
  {
    if(!pixelData)
    {
      return;
    }

    // Use loadingTaskId to match this result to the requesting View state.
    // Apply pixelData through the app's View/image presentation path.
  }

  Dali::Ui::AsyncImageLoader mLoader;
};
```

`Dali::Ui::AsyncImageLoader::Load(const Dali::String& url, ImageDimensions dimensions)` starts work on a worker thread. The returned `uint32_t` is the app's handle for matching the later signal to the original request.

## Tracking completion with task IDs

When a `Dali::Ui::View` can request more than one image, store the IDs returned by `Dali::Ui::AsyncImageLoader::Load()`. In the callback connected to `Dali::Ui::AsyncImageLoader::ImageLoadedSignal()`, compare the callback ID with the stored IDs before updating view state.

```cpp
class ProfileImages
{
public:
  ProfileImages()
  : mLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mLoader.ImageLoadedSignal().Connect(this, &ProfileImages::OnLoaded);
  }

  void LoadBoth(const Dali::String& avatarUrl,
                const Dali::String& bannerUrl,
                Dali::ImageDimensions avatarSize,
                Dali::ImageDimensions bannerSize)
  {
    mAvatarTaskId = mLoader.Load(avatarUrl, avatarSize);
    mBannerTaskId = mLoader.Load(bannerUrl, bannerSize);
  }

private:
  void OnLoaded(uint32_t loadingTaskId, Dali::PixelData pixelData)
  {
    if(!pixelData)
    {
      return;
    }

    if(loadingTaskId == mAvatarTaskId)
    {
      // Update the avatar image owned by the relevant Dali::Ui::View.
    }
    else if(loadingTaskId == mBannerTaskId)
    {
      // Update the banner image owned by the relevant Dali::Ui::View.
    }
  }

  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t mAvatarTaskId{0u};
  uint32_t mBannerTaskId{0u};
};
```

This pattern keeps image ownership clear: `Dali::Ui::AsyncImageLoader` owns the asynchronous request flow, while the app's `Dali::Ui::View` tree owns how loaded image data is presented.

## Cancelling queued image work

Use `Dali::Ui::AsyncImageLoader::Cancel()` when a specific request is no longer needed. It returns `true` when the task can be cancelled while still queued. Use `Dali::Ui::AsyncImageLoader::CancelAll()` when the whole owner is being reset or destroyed.

```cpp
class SearchResultImages
{
public:
  SearchResultImages()
  : mLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mLoader.ImageLoadedSignal().Connect(this, &SearchResultImages::OnLoaded);
  }

  void BeginLoad(const Dali::String& url, Dali::ImageDimensions dimensions)
  {
    mCurrentTaskId = mLoader.Load(url, dimensions);
  }

  bool CancelCurrentLoad()
  {
    return mLoader.Cancel(mCurrentTaskId);
  }

  void ClearAllPendingLoads()
  {
    mLoader.CancelAll();
    mCurrentTaskId = 0u;
  }

private:
  void OnLoaded(uint32_t loadingTaskId, Dali::PixelData pixelData)
  {
    if(loadingTaskId != mCurrentTaskId || !pixelData)
    {
      return;
    }

    // The current result is still relevant to the View state.
  }

  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t mCurrentTaskId{0u};
};
```

`Dali::Ui::AsyncImageLoader::Cancel()` is targeted. `Dali::Ui::AsyncImageLoader::CancelAll()` is broader and is useful when replacing a group of requested images, such as when a list or gallery changes data source.

## Using `ImageUrl` for encoded buffers

`Dali::Ui::ImageUrl` wraps an encoded image buffer and exposes a URL string through `Dali::Ui::ImageUrl::GetUrl()`. Create it with `Dali::Ui::ImageUrl::New(const EncodedImageBuffer& encodedImageBuffer)` and keep the `Dali::Ui::ImageUrl` handle alive while the generated URL is still in use.

```cpp
class EncodedImageSource
{
public:
  Dali::String RegisterEncodedImage(const Dali::EncodedImageBuffer& encodedImageBuffer)
  {
    mImageUrl = Dali::Ui::ImageUrl::New(encodedImageBuffer);
    return mImageUrl.GetUrl();
  }

  const Dali::String& CurrentUrl() const
  {
    return mImageUrl.GetUrl();
  }

private:
  Dali::Ui::ImageUrl mImageUrl;
};
```

The `Dali::Ui::ImageUrl` destructor releases the wrapped buffer from the texture manager when the handle is no longer used. Store the `Dali::Ui::ImageUrl` next to the app state that owns the corresponding image presentation.

## Working with handles

Both `Dali::Ui::AsyncImageLoader` and `Dali::Ui::ImageUrl` are handle types. Use `Dali::Ui::AsyncImageLoader::DownCast()` or `Dali::Ui::ImageUrl::DownCast()` only when receiving a generic `Dali::BaseHandle` from framework-level code.

```cpp
Dali::Ui::AsyncImageLoader GetLoaderFromHandle(Dali::BaseHandle handle)
{
  return Dali::Ui::AsyncImageLoader::DownCast(handle);
}

Dali::Ui::ImageUrl GetImageUrlFromHandle(Dali::BaseHandle handle)
{
  return Dali::Ui::ImageUrl::DownCast(handle);
}
```

Move construction and move assignment are available for handle ownership transfer. After moving, continue using the destination handle.

```cpp
Dali::Ui::AsyncImageLoader CreateLoader()
{
  Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::New();
  return loader;
}

void ReplaceLoader(Dali::Ui::AsyncImageLoader& target)
{
  Dali::Ui::AsyncImageLoader replacement = Dali::Ui::AsyncImageLoader::New();
  target = std::move(replacement);
}

Dali::Ui::ImageUrl KeepImageUrl(Dali::Ui::ImageUrl imageUrl)
{
  Dali::Ui::ImageUrl stored = std::move(imageUrl);
  return stored;
}
```
