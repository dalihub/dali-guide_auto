---
title: Image Loader
sidebar_label: Image Loader
category: images-visuals
---

# Image Loader

`Dali::Ui::AsyncImageLoader` loads image pixel data without blocking the dali-ui application thread, and `Dali::Ui::ImageUrl` lets image data backed by an encoded buffer be exposed as a URL string for dali-ui image presentation paths.

## Table of Contents

- [Load Images Asynchronously](#load-images-asynchronously)
- [Handle Completion and Failed Loads](#handle-completion-and-failed-loads)
- [Cancel Work That Is No Longer Needed](#cancel-work-that-is-no-longer-needed)
- [Use `ImageUrl` for Encoded Buffers](#use-imageurl-for-encoded-buffers)
- [Pass Loader Handles Through Application Code](#pass-loader-handles-through-application-code)

## Load Images Asynchronously

Create a loader with `Dali::Ui::AsyncImageLoader::New`, connect `Dali::Ui::AsyncImageLoader::ImageLoadedSignal`, then start work with `Dali::Ui::AsyncImageLoader::Load`. Each load returns a task ID that you can store and compare when the signal is emitted.

```cpp
#include <dali-ui-foundation/public-api/image-loader/async-image-loader.h>

class GalleryImageLoader : public Dali::ConnectionTracker
{
public:
  GalleryImageLoader()
  : mImageLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mImageLoader.ImageLoadedSignal().Connect(this, &GalleryImageLoader::OnImageLoaded);
  }

  void LoadPreview(const Dali::String& url, Dali::ImageDimensions targetSize)
  {
    mPreviewTaskId = mImageLoader.Load(url, targetSize);
  }

private:
  void OnImageLoaded(uint32_t taskId, Dali::PixelData pixelData)
  {
    if(taskId != mPreviewTaskId)
    {
      return;
    }

    if(pixelData)
    {
      // Use the decoded pixels in your dali-ui view or visual update path.
    }
  }

  Dali::Ui::AsyncImageLoader mImageLoader;
  uint32_t                   mPreviewTaskId{0u};
};
```

Connect the signal before calling `Dali::Ui::AsyncImageLoader::Load`, because the public API allows completion to be emitted immediately after a load request has a result.

## Handle Completion and Failed Loads

`Dali::Ui::AsyncImageLoader::ImageLoadedSignal` provides the task ID and a `Dali::PixelData` handle. Treat a valid `Dali::PixelData` as the successful path, and treat an empty handle as a failed load.

```cpp
#include <dali-ui-foundation/public-api/image-loader/async-image-loader.h>

class AvatarLoader : public Dali::ConnectionTracker
{
public:
  AvatarLoader()
  : mLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mLoader.ImageLoadedSignal().Connect(this, &AvatarLoader::OnLoaded);
  }

  void RequestAvatar(const Dali::String& url, Dali::ImageDimensions size)
  {
    mCurrentTaskId = mLoader.Load(url, size);
  }

private:
  void OnLoaded(uint32_t taskId, Dali::PixelData pixels)
  {
    if(taskId != mCurrentTaskId)
    {
      return;
    }

    if(!pixels)
    {
      // Keep the current dali-ui view state, or switch to your fallback image path.
      return;
    }

    // Apply the loaded pixel data through your dali-ui image presentation code.
  }

  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t                   mCurrentTaskId{0u};
};
```

Use the returned task ID rather than the URL as your completion key. This is safer when a view starts a second request for the same image slot before the first request completes.

## Cancel Work That Is No Longer Needed

Use `Dali::Ui::AsyncImageLoader::Cancel` when a specific request is no longer relevant. Use `Dali::Ui::AsyncImageLoader::CancelAll` when the owning view model or screen is being cleared and no pending image request should continue.

```cpp
#include <dali-ui-foundation/public-api/image-loader/async-image-loader.h>

class ThumbnailRequest : public Dali::ConnectionTracker
{
public:
  ThumbnailRequest()
  : mLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mLoader.ImageLoadedSignal().Connect(this, &ThumbnailRequest::OnLoaded);
  }

  void Start(const Dali::String& url, Dali::ImageDimensions size)
  {
    CancelCurrent();
    mTaskId = mLoader.Load(url, size);
  }

  void CancelCurrent()
  {
    if(mTaskId != 0u)
    {
      const bool removedFromQueue = mLoader.Cancel(mTaskId);
      mTaskId = 0u;

      if(!removedFromQueue)
      {
        // The request may already be running or completed; ignore stale callbacks by task ID.
      }
    }
  }

  void CancelEverything()
  {
    mLoader.CancelAll();
    mTaskId = 0u;
  }

private:
  void OnLoaded(uint32_t taskId, Dali::PixelData pixels)
  {
    if(taskId != mTaskId || !pixels)
    {
      return;
    }

    // Consume pixels for the active request.
  }

  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t                   mTaskId{0u};
};
```

`Dali::Ui::AsyncImageLoader::Cancel` returns `true` only when the task was still queued and could be removed. Always keep the task ID check in the callback, because cancellation can be too late for work that has already started.

## Use `ImageUrl` for Encoded Buffers

`Dali::Ui::ImageUrl` wraps an encoded image buffer and exposes a URL string through `Dali::Ui::ImageUrl::GetUrl`. Use that string in dali-ui image presentation code that accepts image URL values.

```cpp
#include <dali-ui-foundation/public-api/image-loader/image-url.h>

class EncodedPreviewUrl
{
public:
  void SetEncodedPreview(const Dali::EncodedImageBuffer& encodedBuffer)
  {
    mImageUrl       = Dali::Ui::ImageUrl::New(encodedBuffer);
    mImageVisualUrl = mImageUrl.GetUrl();

    // Pass mImageVisualUrl to your dali-ui image visual setup.
  }

  const Dali::String& GetUrl() const
  {
    return mImageVisualUrl;
  }

private:
  Dali::Ui::ImageUrl mImageUrl;
  Dali::String       mImageVisualUrl;
};
```

Keep the `Dali::Ui::ImageUrl` handle alive while its URL is still in use by your image presentation flow. When the application no longer holds the `Dali::Ui::ImageUrl`, the wrapped buffer is requested for removal; visual references can keep the resource alive until the visual is also released.

## Pass Loader Handles Through Application Code

`Dali::Ui::AsyncImageLoader` and `Dali::Ui::ImageUrl` are handle types. They can be copied or moved with normal handle semantics when ownership of a loader or generated URL moves between app objects.

```cpp
#include <dali-ui-foundation/public-api/image-loader/async-image-loader.h>
#include <dali-ui-foundation/public-api/image-loader/image-url.h>

#include <utility>

class ImageLoadingState
{
public:
  explicit ImageLoadingState(Dali::Ui::AsyncImageLoader loader)
  : mLoader(std::move(loader))
  {
  }

  void ReplaceLoader(Dali::Ui::AsyncImageLoader loader)
  {
    mLoader = std::move(loader);
  }

  void StoreEncodedUrl(Dali::Ui::ImageUrl imageUrl)
  {
    mImageUrl = imageUrl;
  }

private:
  Dali::Ui::AsyncImageLoader mLoader;
  Dali::Ui::ImageUrl         mImageUrl;
};
```

When a loader or URL has been stored as a generic handle, recover the typed handle with `Dali::Ui::AsyncImageLoader::DownCast` or `Dali::Ui::ImageUrl::DownCast`.

```cpp
#include <dali-ui-foundation/public-api/image-loader/async-image-loader.h>
#include <dali-ui-foundation/public-api/image-loader/image-url.h>

void UseStoredLoader(Dali::BaseHandle handle,
                     const Dali::String& url,
                     Dali::ImageDimensions size)
{
  Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::DownCast(handle);

  if(loader)
  {
    loader.Load(url, size);
  }
}

Dali::String ResolveStoredImageUrl(Dali::BaseHandle handle)
{
  Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::DownCast(handle);

  if(imageUrl)
  {
    return imageUrl.GetUrl();
  }

  return Dali::String();
}
```
