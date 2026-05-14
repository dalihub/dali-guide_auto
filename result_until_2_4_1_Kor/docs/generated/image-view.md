---
title: 이미지 뷰
sidebar_label: 이미지 뷰
category: views-components
---

# 이미지 뷰 {#image-view}

`Dali::Ui::ImageView`는 dali-ui 뷰 트리에서 이미지 리소스를 표시하며, 애플리케이션에서 이미지 맞춤, 샘플링, 마스킹, 로딩, 렌더링 동작을 제어할 수 있게 합니다. 이미지 로딩은 기본적으로 비동기 방식으로 수행됩니다.

## 목차 {#table-of-contents}

- [이미지 소스 생성 및 설정](#create-and-set-an-image-source)
- [원하는 크기로 맞춤, 샘플링, 로딩](#fit-sample-and-load-at-a-desired-size)
- [표시할 이미지 영역에 색상 적용 및 자르기](#tint-and-crop-the-displayed-image-region)
- [플레이스홀더 이미지와 로딩 시그널 사용](#use-placeholder-images-and-loading-signals)
- [알파 마스크 적용](#apply-alpha-masks)
- [로딩 및 해제 동작 조정](#tune-loading-and-release-behavior)
- [N-Patch 테두리 렌더링](#render-n-patch-borders)
- [속성 상수](#property-constants)

## 이미지 소스 생성 및 설정 {#create-and-set-an-image-source}

`Dali::Ui::ImageView::New()`로 초기화된 `Dali::Ui::ImageView`를 생성한 다음, `SetResourceUrl`로 이미지 URL을 설정합니다. 생성 시점에 소스를 알고 있다면 `Dali::Ui::ImageView::New(const Dali::String&)`에 URL을 전달할 수도 있습니다. 현재 URL은 `GetResourceUrl`로 확인할 수 있으며, URL이 설정되어 있으면 `Reload`가 현재 URL을 다시 로드합니다.

```cpp
Dali::Ui::ImageView thumbnail = Dali::Ui::ImageView::New();

thumbnail
  .SetResourceUrl("images/profile.png")
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

Dali::String currentUrl = thumbnail.GetResourceUrl();

thumbnail.Reload();
```

콜백이나 컨테이너에서 `Dali::Ui::View`를 전달받았고 `ImageView` 전용 API가 필요하다면 `Dali::Ui::ImageView::DownCast`를 사용합니다.

```cpp
void UpdateImageSource(Dali::Ui::View view, const Dali::String& nextUrl)
{
  Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
  if(imageView)
  {
    imageView.SetResourceUrl(nextUrl);
  }
}
```

## 원하는 크기로 맞춤, 샘플링, 로딩 {#fit-sample-and-load-at-a-desired-size}

`SetFittingMode`는 이미지가 뷰 경계 안에 배치되는 방식을 제어합니다. 가로세로비를 유지해야 할 때는 `Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO`를, 사용 가능한 경계까지 늘려도 되는 경우에는 `Dali::Ui::Image::FittingMode::FILL`을, 경계 밖으로 잘리더라도 가로세로비를 유지하는 것이 더 적합할 때는 `Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO`를, 이미지를 원래 크기로 유지해야 할 때는 `Dali::Ui::Image::FittingMode::CENTER`를 사용합니다.

`SetSamplingMode`는 크기 조정 시 사용하는 필터링 방식을 제어합니다. `SetDesiredWidth`와 `SetDesiredHeight`는 픽셀 단위의 래스터화 크기 힌트를 제공하며, `0`은 자연 크기를 의미합니다.

```cpp
Dali::Ui::ImageView preview = Dali::Ui::ImageView::New();

preview
  .SetResourceUrl("images/gallery/large-photo.jpg")
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO)
  .SetSamplingMode(Dali::Ui::Image::SamplingMode::BOX_THEN_LINEAR)
  .SetDesiredWidth(480)
  .SetDesiredHeight(320);

Dali::Ui::Image::FittingMode fittingMode = preview.GetFittingMode();
Dali::Ui::Image::SamplingMode samplingMode = preview.GetSamplingMode();
int desiredWidth = preview.GetDesiredWidth();
int desiredHeight = preview.GetDesiredHeight();
```

최종 뷰 크기를 기준으로 이미지 로드 크기를 결정해야 한다면 `SetImageLoadWithViewSize`를 활성화합니다. 이 설정은 뷰 크기가 변경될 때마다 확정된 뷰 크기로 이미지를 로드하므로, 작은 표시 영역에 전체 해상도 리소스를 로드하는 일을 피할 수 있습니다. 같은 `ImageView`에서 가로세로비 기반 레이아웃과 함께 사용하지 마십시오. 레이아웃에 보고되는 자연 크기가 원본 이미지 크기 대신 현재 뷰 크기가 되기 때문입니다.

```cpp
Dali::Ui::ImageView cardImage = Dali::Ui::ImageView::New();

cardImage
  .SetResourceUrl("images/card-cover.jpg")
  .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO)
  .SetImageLoadWithViewSize(true);

bool loadWithViewSize = cardImage.IsImageLoadWithViewSizeEnabled();
```

## 표시할 이미지 영역에 색상 적용 및 자르기 {#tint-and-crop-the-displayed-image-region}

이미지에 색상 배율을 적용하려면 `SetImageColor`를 사용합니다. 원본 이미지의 정규화된 하위 영역을 표시하려면 `SetPixelArea`를 사용하며, 영역은 `(x, y, width, height)` 형식으로 표현합니다.

```cpp
Dali::Ui::ImageView icon = Dali::Ui::ImageView::New();

icon
  .SetResourceUrl("images/icons/search.png")
  .SetImageColor(Dali::Ui::UiColor(1.0f, 1.0f, 1.0f, 0.65f))
  .SetPreMultipliedAlpha(true);

Dali::Ui::UiColor tint = icon.GetImageColor();
bool preMultiplied = icon.IsPreMultipliedAlpha();
```

```cpp
Dali::Ui::ImageView atlasImage = Dali::Ui::ImageView::New();

atlasImage
  .SetResourceUrl("images/sprite-atlas.png")
  .SetPixelArea(Dali::Vector4(0.25f, 0.0f, 0.25f, 0.5f))
  .SetFittingMode(Dali::Ui::Image::FittingMode::FILL);

Dali::Vector4 displayedArea = atlasImage.GetPixelArea();
```

## 플레이스홀더 이미지와 로딩 시그널 사용 {#use-placeholder-images-and-loading-signals}

기본 이미지가 로드되는 동안 임시 이미지를 표시하려면 `SetPlaceholderUrl`을 설정합니다. 리소스를 표시할 준비가 되면 `ResourceReadySignal`이 `Dali::Ui::View` 매개변수와 함께 발생하며, `GetLoadingStatus`는 현재 `Dali::Ui::Visual::ResourceStatus`를 반환합니다.

```cpp
class ImageController : public Dali::ConnectionTracker
{
public:
  Dali::Ui::ImageView CreateImage()
  {
    Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

    imageView
      .SetPlaceholderUrl("images/placeholder.png")
      .SetResourceUrl("images/remote-photo.jpg")
      .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

    imageView.ResourceReadySignal().Connect(this, &ImageController::OnResourceReady);

    return imageView;
  }

private:
  void OnResourceReady(Dali::Ui::View view)
  {
    Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
    if(imageView)
    {
      Dali::Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
      (void)status;
    }
  }
};
```

`GetPlaceholderUrl`을 사용하면 구성된 플레이스홀더 URL을 확인할 수 있습니다.

```cpp
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

imageView.SetPlaceholderUrl("images/loading.png");

Dali::String placeholderUrl = imageView.GetPlaceholderUrl();
```

## 알파 마스크 적용 {#apply-alpha-masks}

`SetAlphaMaskUrl`은 마스크 이미지의 알파 채널을 기본 이미지에 적용합니다. `SetCropToMask`는 이미지를 마스크 경계에 맞게 자를지 여부를 제어합니다. `SetMaskingMode`는 마스킹을 렌더링 중에 적용할지, 로딩 중에 적용할지 선택합니다.

```cpp
Dali::Ui::ImageView avatar = Dali::Ui::ImageView::New();

avatar
  .SetResourceUrl("images/profile-photo.jpg")
  .SetAlphaMaskUrl("images/masks/circle-mask.png")
  .SetCropToMask(true)
  .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING)
  .SetFittingMode(Dali::Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

Dali::String maskUrl = avatar.GetAlphaMaskUrl();
bool cropToMask = avatar.IsCropToMask();
Dali::Ui::Image::MaskingType maskingMode = avatar.GetMaskingMode();
```

마스킹된 결과를 로딩 과정에서 준비하려면 `Dali::Ui::Image::MaskingType::MASKING_ON_LOADING`을 사용합니다.

```cpp
Dali::Ui::ImageView maskedThumbnail = Dali::Ui::ImageView::New();

maskedThumbnail
  .SetResourceUrl("images/thumbnail.jpg")
  .SetAlphaMaskUrl("images/masks/rounded-rect.png")
  .SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_LOADING);
```

## 로딩 및 해제 동작 조정 {#tune-loading-and-release-behavior}

`SetLoadPolicy`는 로딩이 시작되는 시점을 제어합니다. `Dali::Ui::Image::LoadPolicy::IMMEDIATE`는 이미지 뷰가 생성될 때 로드하고, `Dali::Ui::Image::LoadPolicy::ATTACHED`는 뷰가 장면에 연결될 때까지 로딩을 지연합니다.

`SetReleasePolicy`는 텍스처를 캐시에서 해제하는 시점을 제어합니다. 이미지의 수명과 재사용 패턴에 따라 `Dali::Ui::Image::ReleasePolicy::DETACHED`, `Dali::Ui::Image::ReleasePolicy::DESTROYED`, 또는 `Dali::Ui::Image::ReleasePolicy::NEVER`를 사용합니다. `SetFastTrackUpload`는 조건에 맞는 이미지 리소스에 빠른 업로드 경로를 활성화합니다. 이 경로를 사용할 때는 `LoadPolicy::ATTACHED`와 `ReleasePolicy::DETACHED`를 함께 사용하고, 같은 이미지에서 동기 로딩이나 알파 마스크 로딩을 사용하지 마십시오.

```cpp
Dali::Ui::ImageView reusableImage = Dali::Ui::ImageView::New();

reusableImage
  .SetResourceUrl("images/navigation/background.jpg")
  .SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED)
  .SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED)
  .SetFastTrackUpload(true)
  .SetOrientationCorrection(true);

Dali::Ui::Image::LoadPolicy loadPolicy = reusableImage.GetLoadPolicy();
Dali::Ui::Image::ReleasePolicy releasePolicy = reusableImage.GetReleasePolicy();
bool fastTrackUpload = reusableImage.IsFastTrackUploadEnabled();
bool orientationCorrection = reusableImage.IsOrientationCorrectionEnabled();
```

블로킹 로드 동작이 허용되는 경우에는 `SetSynchronousLoading`으로 메인 스레드에서 동기 이미지 로딩을 활성화할 수 있습니다.

```cpp
Dali::Ui::ImageView immediateIcon = Dali::Ui::ImageView::New();

immediateIcon
  .SetSynchronousLoading(true)
  .SetResourceUrl("images/icons/local-action.png");

bool synchronous = immediateIcon.IsSynchronousLoading();
```

## N-Patch 테두리 렌더링 {#render-n-patch-borders}

고정된 가장자리 영역을 유지해야 하는 확장 가능한 패널 이미지에는 `SetNPatchBorder`를 사용합니다. 테두리는 `(left, top, right, bottom)` 픽셀 값으로 표현되며, 0이 아닌 테두리를 설정하면 N-patch 렌더링이 활성화됩니다. 가운데 영역을 생략해야 할 때는 `SetNPatchBorderOnly`로 테두리 영역만 렌더링합니다.

```cpp
Dali::Ui::ImageView panel = Dali::Ui::ImageView::New();

panel
  .SetResourceUrl("images/panels/dialog.9.png")
  .SetNPatchBorder(Dali::Vector4(12.0f, 12.0f, 12.0f, 12.0f))
  .SetNPatchBorderOnly(false);

Dali::Vector4 border = panel.GetNPatchBorder();
bool borderOnly = panel.IsNPatchBorderOnly();
```

`SetDepthIndex`는 같은 레이어 안에서 이미지 비주얼의 렌더링 순서를 조정합니다. 값이 클수록 값이 작은 비주얼보다 위에 렌더링됩니다.

```cpp
Dali::Ui::ImageView background = Dali::Ui::ImageView::New();
Dali::Ui::ImageView foreground = Dali::Ui::ImageView::New();

background
  .SetResourceUrl("images/background.png")
  .SetDepthIndex(0);

foreground
  .SetResourceUrl("images/foreground.png")
  .SetDepthIndex(1);
```

## 속성 상수 {#property-constants}

애플리케이션 코드에서는 `SetResourceUrl`, `SetFittingMode`, `SetSamplingMode`, `SetAlphaMaskUrl`, `SetReleasePolicy` 같은 타입 지정 메서드를 우선적으로 사용하십시오. 속성 상수는 `Dali::Ui::ImageView::Property`가 소유하며, 속성 메타데이터가 필요한 시스템을 위해 `Dali::Ui::ImageViewPropertyIndex` 정의에 매핑됩니다.

| 속성 상수 | 목적 |
| --- | --- |
| `Dali::Ui::ImageView::Property::IMAGE` | 이미지 리소스 URL |
| `Dali::Ui::ImageView::Property::FITTING_MODE` | `Dali::Ui::Image::FittingMode` 값 |
| `Dali::Ui::ImageView::Property::SAMPLING_MODE` | `Dali::Ui::Image::SamplingMode` 값 |
| `Dali::Ui::ImageView::Property::DESIRED_WIDTH` | 픽셀 단위의 원하는 래스터화 너비 힌트 |
| `Dali::Ui::ImageView::Property::DESIRED_HEIGHT` | 픽셀 단위의 원하는 래스터화 높이 힌트 |
| `Dali::Ui::ImageView::Property::IMAGE_COLOR` | 이미지 색상 배율 |
| `Dali::Ui::ImageView::Property::PRE_MULTIPLIED_ALPHA` | 선곱 알파 플래그 |
| `Dali::Ui::ImageView::Property::PLACEHOLDER_IMAGE` | 플레이스홀더 이미지 URL |
| `Dali::Ui::ImageView::Property::IMAGE_LOAD_WITH_VIEW_SIZE` | 뷰 크기 기반 로딩 플래그 |
| `Dali::Ui::ImageView::Property::ALPHA_MASK_URL` | 알파 마스크 이미지 URL |
| `Dali::Ui::ImageView::Property::CROP_TO_MASK` | 마스크에 맞춰 자르기 플래그 |
| `Dali::Ui::ImageView::Property::MASKING_MODE` | `Dali::Ui::Image::MaskingType` 값 |
| `Dali::Ui::ImageView::Property::LOAD_POLICY` | `Dali::Ui::Image::LoadPolicy` 값 |
| `Dali::Ui::ImageView::Property::RELEASE_POLICY` | `Dali::Ui::Image::ReleasePolicy` 값 |
| `Dali::Ui::ImageView::Property::SYNCHRONOUS_LOADING` | 동기 로딩 플래그 |
| `Dali::Ui::ImageView::Property::FAST_TRACK_UPLOADING` | 빠른 GPU 업로드 플래그 |
| `Dali::Ui::ImageView::Property::ORIENTATION_CORRECTION` | EXIF 방향 보정 플래그 |
| `Dali::Ui::ImageView::Property::N_PATCH_BORDER` | N-patch 테두리 인셋 |
| `Dali::Ui::ImageView::Property::N_PATCH_BORDER_ONLY` | N-patch 테두리 전용 렌더링 플래그 |
| `Dali::Ui::ImageView::Property::PIXEL_AREA` | 정규화된 표시 이미지 하위 영역 |

`Dali::Ui::ImageViewPropertyIndex::PropertyRange`는 `ImageView`의 속성 인덱스 범위를 정의하며, `Dali::Ui::ImageViewPropertyIndex::IMAGE`, `Dali::Ui::ImageViewPropertyIndex::FITTING_MODE`, `Dali::Ui::ImageViewPropertyIndex::PLACEHOLDER_IMAGE`, `Dali::Ui::ImageViewPropertyIndex::PIXEL_AREA` 같은 멤버는 `Dali::Ui::ImageView::Property`가 사용하는 기반 인덱스 정의입니다.
