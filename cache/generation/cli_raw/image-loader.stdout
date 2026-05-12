---
title: Image Loader
sidebar_label: Image Loader
category: images-visuals
---

# Image Loader

`Dali::Ui::AsyncImageLoader` loads image data asynchronously for dali-ui apps, while `Dali::Ui::ImageUrl` turns an encoded image buffer into a URL-style handle that can be passed through image-oriented code.

## Table of Contents

- [Load Images Without Blocking the View Flow](#load-images-without-blocking-the-view-flow)
- [Track and Cancel Loading Tasks](#track-and-cancel-loading-tasks)
- [React to Image Loading Completion](#react-to-image-loading-completion)
- [Use ImageUrl for Encoded Image Buffers](#use-imageurl-for-encoded-image-buffers)
- [Keep Loader Handles in App State](#keep-loader-handles-in-app-state)

## Load Images Without Blocking the View Flow

In a dali-ui application, screen code should keep the app-facing model in terms of `Dali::Ui::View`. `Dali::Ui::AsyncImageLoader` is not itself a view; it is a helper object used by view controllers or image-related services when image decoding should run asynchronously.

Create the loader with `Dali::Ui::AsyncImageLoader::New`, then call `Dali::Ui::AsyncImageLoader::Load` with a URL and requested `ImageDimensions`. The returned task id identifies that single load request.

```cpp
class GalleryImageLoader
{
public:
  GalleryImageLoader()
  : mLoader(Dali::Ui::AsyncImageLoader::New())
  {
  }

  uint32_t RequestImage(const Dali::String& url, ImageDimensions dimensions)
  {
    return mLoader.Load(url, dimensions);
  }

private:
  Dali::Ui::AsyncImageLoader mLoader;
};
```

Use this pattern when a `Dali::Ui::View`-based screen needs decoded image data before updating an image view, cache, or model object. The view tree remains owned by your screen code; `Dali::Ui::AsyncImageLoader` only owns the asynchronous loading work.

## Track and Cancel Loading Tasks

Each call to `Dali::Ui::AsyncImageLoader::Load` returns a `uint32_t` loading task id. Store that id when the result is still relevant to the current screen state. If the user navigates away, changes the requested image, or closes the image panel, cancel the stale request with `Dali::Ui::AsyncImageLoader::Cancel`.

```cpp
class PreviewLoader
{
public:
  PreviewLoader()
  : mLoader(Dali::Ui::AsyncImageLoader::New()),
    mLoadingTaskId(0u)
  {
  }

  void LoadPreview(const Dali::String& url, ImageDimensions dimensions)
  {
    if(mLoadingTaskId != 0u)
    {
      mLoader.Cancel(mLoadingTaskId);
      mLoadingTaskId = 0u;
    }

    mLoadingTaskId = mLoader.Load(url, dimensions);
  }

  bool CancelPreview()
  {
    if(mLoadingTaskId == 0u)
    {
      return false;
    }

    bool cancelled = mLoader.Cancel(mLoadingTaskId);
    mLoadingTaskId = 0u;
    return cancelled;
  }

private:
  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t                   mLoadingTaskId;
};
```

Use `Dali::Ui::AsyncImageLoader::CancelAll` when the controller is discarding every pending request it owns.

```cpp
class ImageSearchSession
{
public:
  ImageSearchSession()
  : mLoader(Dali::Ui::AsyncImageLoader::New())
  {
  }

  uint32_t QueueResultImage(const Dali::String& url, ImageDimensions dimensions)
  {
    return mLoader.Load(url, dimensions);
  }

  void ClearPendingResults()
  {
    mLoader.CancelAll();
  }

private:
  Dali::Ui::AsyncImageLoader mLoader;
};
```

`Dali::Ui::AsyncImageLoader::Cancel` is task-specific and returns whether that cancellation request was accepted. `Dali::Ui::AsyncImageLoader::CancelAll` is broader and is useful for screen teardown or replacing an entire result set.

## React to Image Loading Completion

Use `Dali::Ui::AsyncImageLoader::ImageLoadedSignal` as the event source for completed asynchronous loads. Connect the signal on the `Dali::Ui::AsyncImageLoader` instance that owns the tasks, and keep the connection lifetime with the same controller that owns the relevant `Dali::Ui::View` state.

```cpp
class AsyncImageController
{
public:
  AsyncImageController()
  : mLoader(Dali::Ui::AsyncImageLoader::New())
  {
  }

  Dali::Ui::AsyncImageLoader::ImageLoadedSignalType& LoadingFinished()
  {
    return mLoader.ImageLoadedSignal();
  }

  uint32_t LoadForView(const Dali::String& url, ImageDimensions dimensions)
  {
    return mLoader.Load(url, dimensions);
  }

private:
  Dali::Ui::AsyncImageLoader mLoader;
};
```

This keeps event ownership explicit: the controller exposes or connects to `Dali::Ui::AsyncImageLoader::ImageLoadedSignal`, while the visual update still happens through the app's `Dali::Ui::View`-based screen objects.

## Use ImageUrl for Encoded Image Buffers

`Dali::Ui::ImageUrl` is the dali-ui handle for an image URL generated from an `EncodedImageBuffer`. Create it with `Dali::Ui::ImageUrl::New`, then read the URL string with `Dali::Ui::ImageUrl::GetUrl`.

```cpp
Dali::Ui::ImageUrl CreateBufferedImageUrl(const EncodedImageBuffer& encodedImageBuffer)
{
  return Dali::Ui::ImageUrl::New(encodedImageBuffer);
}

const Dali::String& ResolveBufferedImageUrl(Dali::Ui::ImageUrl imageUrl)
{
  return imageUrl.GetUrl();
}
```

This is useful when image bytes are already available in memory and the rest of the image path expects URL-style input. The `Dali::Ui::ImageUrl` handle owns that conversion point; app code can pass the returned URL string into image-loading code without introducing raw actor property updates.

You can also store or replace `Dali::Ui::ImageUrl` handles in ordinary application objects.

```cpp
class BufferedImageReference
{
public:
  void SetImageUrl(const Dali::Ui::ImageUrl& imageUrl)
  {
    mImageUrl = imageUrl;
  }

  const Dali::String& GetUrl() const
  {
    return mImageUrl.GetUrl();
  }

private:
  Dali::Ui::ImageUrl mImageUrl;
};
```

## Keep Loader Handles in App State

Both `Dali::Ui::AsyncImageLoader` and `Dali::Ui::ImageUrl` are handle types. Move them when transferring ownership between services or controllers, and keep them near the `Dali::Ui::View` state that depends on their results.

```cpp
Dali::Ui::AsyncImageLoader CreateLoader()
{
  return Dali::Ui::AsyncImageLoader::New();
}

class ImageLoaderOwner
{
public:
  void SetLoader(Dali::Ui::AsyncImageLoader loader)
  {
    mLoader = std::move(loader);
  }

  uint32_t Load(const Dali::String& url, ImageDimensions dimensions)
  {
    return mLoader.Load(url, dimensions);
  }

private:
  Dali::Ui::AsyncImageLoader mLoader;
};
```

Use `Dali::Ui::AsyncImageLoader::DownCast` and `Dali::Ui::ImageUrl::DownCast` only when your app receives a generic `BaseHandle` and needs to recover the typed dali-ui handle.

```cpp
Dali::Ui::AsyncImageLoader AsAsyncImageLoader(BaseHandle handle)
{
  return Dali::Ui::AsyncImageLoader::DownCast(handle);
}

Dali::Ui::ImageUrl AsImageUrl(BaseHandle handle)
{
  return Dali::Ui::ImageUrl::DownCast(handle);
}
```

The image loader should remain a supporting service around your `Dali::Ui::View` screens. Keep loading, cancellation, and signal handling in the controller layer, then apply completed image results through the dali-ui view objects that make up the visible application.
