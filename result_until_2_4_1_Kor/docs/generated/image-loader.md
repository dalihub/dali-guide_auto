---
title: 이미지 로더
sidebar_label: 이미지 로더
category: images-visuals
---

# 이미지 로더 {#image-loader}

`Dali::Ui::AsyncImageLoader`는 dali-ui 앱에서 URL의 이미지 픽셀 데이터를 비동기적으로 로드하고, `Dali::Ui::ImageUrl`은 인코딩된 이미지 버퍼처럼 외부에서 제공된 이미지 리소스를 URL 문자열로 노출합니다.

## 목차 {#table-of-contents}

- [UI를 차단하지 않고 이미지 데이터 로드하기](#loading-image-data-without-blocking-the-ui)
- [로드 결과를 `Dali::Ui::View`와 매칭하기](#matching-load-results-to-a-daliuiview)
- [오래된 요청 취소하기](#cancelling-obsolete-requests)
- [인코딩된 버퍼를 이미지 URL로 사용하기](#using-encoded-buffers-as-image-urls)
- [이미지 로더 핸들 다루기](#working-with-image-loader-handles)

## UI를 차단하지 않고 이미지 데이터 로드하기 {#loading-image-data-without-blocking-the-ui}

`Dali::Ui::AsyncImageLoader::New()`로 로더를 생성합니다. 로더는 작업자 스레드에서 이미지 로드 작업을 시작합니다. 보류 중인 이미지 요청이 완료될 수 있는 동안에는 `Dali::Ui::View`를 소유한 객체가 로더를 계속 유지해야 합니다.

`Dali::Ui::AsyncImageLoader::Load()`를 호출하기 전에 `Dali::Ui::AsyncImageLoader::ImageLoadedSignal()`에 연결합니다. 이 시그널은 `Load()`에서 반환된 태스크 ID와 로드된 픽셀 데이터를 전달하므로, 콜백에서 어떤 뷰 상태를 업데이트할지 결정할 수 있습니다.

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

`Dali::Ui::AsyncImageLoader::Load()`에는 `Dali::ImageDimensions`를 받는 크기 지정 형식이 있습니다. 썸네일, 타일, 아바타 슬롯처럼 뷰가 의도한 이미지 크기를 이미 알고 있을 때 이 형식을 사용합니다.

```cpp
uint32_t RequestTileImage(Dali::Ui::AsyncImageLoader& loader, const Dali::String& url)
{
  return loader.Load(url, Dali::ImageDimensions(320u, 180u));
}
```

## 로드 결과를 `Dali::Ui::View`와 매칭하기 {#matching-load-results-to-a-daliuiview}

하나의 `Dali::Ui::AsyncImageLoader`가 여러 요청을 시작할 수 있습니다. `Dali::Ui::AsyncImageLoader::Load()`에서 반환된 각 태스크 ID를 저장하고, `Dali::Ui::AsyncImageLoader::ImageLoadedSignal()`을 통해 연결된 콜백에서 이를 비교합니다.

이 방식은 갤러리 셀이나 검색 결과 행처럼 `Dali::Ui::View`가 바뀌는 데이터에 맞춰 재사용될 때 특히 중요합니다. 이전 요청은 뷰가 다른 이미지에 다시 바인딩된 뒤에 완료될 수 있습니다.

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

콜백에서는 유효하지 않은 픽셀 데이터 핸들을 로드 실패로 처리해야 합니다. 오류를 앱에서 어떻게 표시할지 결정하는 것은 `Dali::Ui::AsyncImageLoader`의 역할이 아닙니다. 이 객체는 완료 여부와 로드된 데이터를 보고하므로, 실패 처리는 소유 뷰나 컨트롤러 안에 국한해 두는 것이 좋습니다.

## 오래된 요청 취소하기 {#cancelling-obsolete-requests}

요청 하나가 더 이상 필요하지 않을 때는 `Dali::Ui::AsyncImageLoader::Cancel()`을 사용합니다. 태스크가 아직 대기열에 있어 제거되면 `true`를 반환합니다. 로드가 이미 충분히 진행되어 제거할 수 없는 상태라면 `Cancel()`은 `false`를 반환합니다.

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

목록을 비우거나, 갤러리 데이터 소스를 교체하거나, 소유자를 파괴하는 경우처럼 로더가 추적하는 모든 대기 작업이 오래된 상태라면 `Dali::Ui::AsyncImageLoader::CancelAll()`을 사용합니다.

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

## 인코딩된 버퍼를 이미지 URL로 사용하기 {#using-encoded-buffers-as-image-urls}

`Dali::Ui::ImageUrl`은 인코딩된 이미지 버퍼를 감싸고 `Dali::Ui::ImageUrl::GetUrl()`을 통해 URL 문자열을 제공할 수 있습니다. `Dali::Ui::ImageUrl::New()`로 생성하고, 생성된 URL이 앱의 이미지 프레젠테이션에서 사용되는 동안에는 반환된 핸들을 계속 유지합니다.

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

`Dali::Ui::ImageUrl` 핸들이 파괴되면 해당 외부 리소스 URL의 제거를 요청합니다. 같은 버퍼를 참조하는 비주얼은 자체 리소스 참조를 유지할 수 있으므로, 해당 이미지 사용을 소유하는 뷰 상태와 함께 `ImageUrl` 핸들을 저장합니다.

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

## 이미지 로더 핸들 다루기 {#working-with-image-loader-handles}

`Dali::Ui::AsyncImageLoader`와 `Dali::Ui::ImageUrl`은 핸들 타입입니다. 프레임워크 수준 코드가 일반 `Dali::BaseHandle`을 전달하고 앱에서 타입이 지정된 핸들을 복원해야 할 때는 `Dali::Ui::AsyncImageLoader::DownCast()`와 `Dali::Ui::ImageUrl::DownCast()`를 사용합니다.

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

`Dali::Ui::AsyncImageLoader`와 `Dali::Ui::ImageUrl`은 모두 이동 생성과 이동 대입을 지원합니다. 이는 뷰-컨트롤러 객체 사이에서 소유권을 전달할 때 유용합니다.

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
