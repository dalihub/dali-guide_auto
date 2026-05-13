---
title: Image Loader
sidebar_label: Image Loader
category: images-visuals
---

# Image Loader

`Dali::Ui::AsyncImageLoader` loads image pixel data asynchronously for dali-ui apps, and `Dali::Ui::ImageUrl` exposes an encoded image buffer as a URL-style handle.

## Table of Contents

- [Loading image data without blocking the UI](#loading-image-data-without-blocking-the-ui)
- [Matching load results to a `Dali::Ui::View`](#matching-load-results-to-a-daliuiview)
- [Cancelling obsolete requests](#cancelling-obsolete-requests)
- [Using encoded buffers as image URLs](#using-encoded-buffers-as-image-urls)
- [Working with image-loader handles](#working-with-image-loader-handles)

## Loading image data without blocking the UI

Create a loader with `Dali::Ui::AsyncImageLoader::New()`. The loader owns the asynchronous request flow; your `Dali::Ui::View` owner should keep the loader alive for as long as pending image requests can complete.

Connect `Dali::Ui::AsyncImageLoader::ImageLoadedSignal()` before calling `Dali::Ui::AsyncImageLoader::Load()`. The signal uses the task ID returned from `Load()` and the loaded pixel data, so the callback can decide which view state to update.

```cpp
class ThumbnailLoader
{
public:
  explicit ThumbnailLoader(Dali::Ui::View ownerView)
  : mOwnerView(ownerView),
    mLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mLoader.ImageLoadedSignal().Connect(this, &ThumbnailLoader::OnImageLoaded);
  }

  void LoadThumbnail(const Dali::String& imageUrl)
  {
    mTaskId = mLoader.Load(imageUrl, Dali::ImageDimensions(160u, 160u));
  }

private:
  void OnImageLoaded(uint32_t loadedTaskId, Dali::PixelData pixelData)
  {
    if(loadedTaskId != mTaskId)
    {
      return;
    }

    if(pixelData)
    {
      // Use the loaded pixel data to update image presentation owned by mOwnerView.
    }
  }

  Dali::Ui::View             mOwnerView;
  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t                   mTaskId{0u};
};
```

`Dali::Ui::AsyncImageLoader::Load()` has a size-aware form that accepts `Dali::ImageDimensions`. Use this when the view already knows the intended image size, such as a thumbnail, tile, or avatar slot.

```cpp
uint32_t RequestTileImage(Dali::Ui::AsyncImageLoader& loader, const Dali::String& url)
{
  return loader.Load(url, Dali::ImageDimensions(320u, 180u));
}
```

## Matching load results to a `Dali::Ui::View`

A single `Dali::Ui::AsyncImageLoader` can start more than one request. Store each task ID returned by `Dali::Ui::AsyncImageLoader::Load()` and compare it in the callback connected through `Dali::Ui::AsyncImageLoader::ImageLoadedSignal()`.

This is especially important when a `Dali::Ui::View` is reused for changing data, such as a gallery cell or search result row. The old request may complete after the view has been rebound to a different image.

```cpp
class ProfileImageSet
{
public:
  explicit ProfileImageSet(Dali::Ui::View ownerView)
  : mOwnerView(ownerView),
    mLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mLoader.ImageLoadedSignal().Connect(this, &ProfileImageSet::OnImageLoaded);
  }

  void LoadImages(const Dali::String& avatarUrl, const Dali::String& bannerUrl)
  {
    mAvatarTaskId = mLoader.Load(avatarUrl, Dali::ImageDimensions(96u, 96u));
    mBannerTaskId = mLoader.Load(bannerUrl, Dali::ImageDimensions(640u, 240u));
  }

private:
  void OnImageLoaded(uint32_t loadedTaskId, Dali::PixelData pixelData)
  {
    if(!pixelData)
    {
      return;
    }

    if(loadedTaskId == mAvatarTaskId)
    {
      // Apply avatar pixel data to the avatar presentation for mOwnerView.
      return;
    }

    if(loadedTaskId == mBannerTaskId)
    {
      // Apply banner pixel data to the banner presentation for mOwnerView.
      return;
    }
  }

  Dali::Ui::View             mOwnerView;
  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t                   mAvatarTaskId{0u};
  uint32_t                   mBannerTaskId{0u};
};
```

The callback should treat an invalid pixel data handle as a load failure. Keep failure handling local to the owning view or controller, because `Dali::Ui::AsyncImageLoader` reports completion and loaded data rather than deciding how the app should present errors.

## Cancelling obsolete requests

Use `Dali::Ui::AsyncImageLoader::Cancel()` when one request is no longer useful. It returns `true` when the task is removed while it is still queued. If loading has already progressed far enough that it cannot be removed, `Cancel()` returns `false`.

```cpp
class SearchResultImage
{
public:
  explicit SearchResultImage(Dali::Ui::View ownerView)
  : mOwnerView(ownerView),
    mLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mLoader.ImageLoadedSignal().Connect(this, &SearchResultImage::OnImageLoaded);
  }

  void ReplaceImage(const Dali::String& nextUrl)
  {
    if(mTaskId != 0u)
    {
      const bool removedFromQueue = mLoader.Cancel(mTaskId);
      (void)removedFromQueue;
    }

    mTaskId = mLoader.Load(nextUrl, Dali::ImageDimensions(240u, 240u));
  }

private:
  void OnImageLoaded(uint32_t loadedTaskId, Dali::PixelData pixelData)
  {
    if(loadedTaskId == mTaskId && pixelData)
    {
      // Update the current search-result image presentation.
    }
  }

  Dali::Ui::View             mOwnerView;
  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t                   mTaskId{0u};
};
```

Use `Dali::Ui::AsyncImageLoader::CancelAll()` when all pending work owned by the loader is obsolete, such as when clearing a list, replacing a gallery data source, or destroying the owner.

```cpp
class GalleryPageImages
{
public:
  explicit GalleryPageImages(Dali::Ui::View pageView)
  : mPageView(pageView),
    mLoader(Dali::Ui::AsyncImageLoader::New())
  {
    mLoader.ImageLoadedSignal().Connect(this, &GalleryPageImages::OnImageLoaded);
  }

  ~GalleryPageImages()
  {
    mLoader.CancelAll();
  }

  void Reset()
  {
    mLoader.CancelAll();
    mHeroTaskId = 0u;
  }

private:
  void OnImageLoaded(uint32_t loadedTaskId, Dali::PixelData pixelData)
  {
    if(loadedTaskId == mHeroTaskId && pixelData)
    {
      // Apply the loaded hero image for mPageView.
    }
  }

  Dali::Ui::View             mPageView;
  Dali::Ui::AsyncImageLoader mLoader;
  uint32_t                   mHeroTaskId{0u};
};
```

## Using encoded buffers as image URLs

`Dali::Ui::ImageUrl` wraps an encoded image buffer and provides a URL string through `Dali::Ui::ImageUrl::GetUrl()`. Create it with `Dali::Ui::ImageUrl::New()` and keep the returned handle alive while the generated URL is still being used by the app's image presentation.

```cpp
class EncodedPreviewImage
{
public:
  explicit EncodedPreviewImage(Dali::Ui::View ownerView)
  : mOwnerView(ownerView)
  {
  }

  const Dali::String& UseEncodedBuffer(const Dali::EncodedImageBuffer& encodedImageBuffer)
  {
    mImageUrl = Dali::Ui::ImageUrl::New(encodedImageBuffer);
    return mImageUrl.GetUrl();
  }

  const Dali::String& CurrentUrl() const
  {
    return mImageUrl.GetUrl();
  }

private:
  Dali::Ui::View     mOwnerView;
  Dali::Ui::ImageUrl mImageUrl;
};
```

When the `Dali::Ui::ImageUrl` handle is destroyed and no visual still references the buffer, the wrapped buffer can be released from the texture manager. Store the `ImageUrl` handle with the view state that owns the corresponding image usage.

```cpp
class ImageUrlStore
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

## Working with image-loader handles

`Dali::Ui::AsyncImageLoader` and `Dali::Ui::ImageUrl` are handle types. Use `Dali::Ui::AsyncImageLoader::DownCast()` and `Dali::Ui::ImageUrl::DownCast()` when framework-level code passes a generic `Dali::BaseHandle` and the app needs to recover the typed handle.

```cpp
Dali::Ui::AsyncImageLoader GetImageLoader(Dali::BaseHandle handle)
{
  return Dali::Ui::AsyncImageLoader::DownCast(handle);
}

Dali::Ui::ImageUrl GetImageUrl(Dali::BaseHandle handle)
{
  return Dali::Ui::ImageUrl::DownCast(handle);
}
```

Move construction and move assignment are available for both `Dali::Ui::AsyncImageLoader` and `Dali::Ui::ImageUrl`, which is useful when transferring ownership between view-controller objects.

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
