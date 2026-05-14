---
title: 이미지뷰
sidebar_label: 이미지뷰
category: views-components
---

# 이미지뷰

`ImageView`는 URL, 파일 경로 또는 메모리 내 픽셀 데이터에서 정적 이미지를 표시하는 뷰 컴포넌트입니다. JPEG, PNG, WebP, GIF(정적 프레임)를 포함한 다양한 이미지 형식을 지원합니다. 애니메이션 이미지의 경우 `AnimatedImageView`를 사용하세요.

## 목차

- [ImageView 생성](#imageview-생성)
- [이미지 소스 설정](#이미지-소스-설정)
- [피팅 모드](#피팅-모드)
- [샘플링 및 원하는 크기](#샘플링-및-원하는-크기)
- [알파 마스킹](#알파-마스킹)
- [부분 표시를 위한 픽셀 영역](#부분-표시를-위한-픽셀-영역)
- [이미지 색상 틴팅](#이미지-색상-틴팅)
- [로드 정책 및 리소스 관리](#로드-정책-및-리소스-관리)
- [플레이스홀더 이미지](#플레이스홀더-이미지)
- [N-패치 이미지](#n-패치-이미지)
- [리소스 준비 시그널](#리소스-준비-시그널)

## ImageView 생성

`New()` 팩토리 메서드로 `ImageView`를 생성합니다. 빈 뷰를 생성하거나 생성 시 URL을 제공할 수 있습니다.

```cpp
// 빈 ImageView 생성
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New();

// URL로 ImageView 생성
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New("path/to/image.jpg");
```

생성 중에 플루언트 체이닝을 사용하여 뷰를 구성합니다:

```cpp
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::New("path/to/image.png")
  .SetRequestedWidth(MATCH_PARENT)
  .SetRequestedHeight(MATCH_PARENT)
  .SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

## 이미지 소스 설정

`ImageView` 생성 후 `SetResourceUrl()`로 이미지 소스를 변경할 수 있습니다.

```cpp
imageView.SetResourceUrl("path/to/another-image.jpg");
```

현재 리소스 URL을 조회하려면:

```cpp
Dali::String url = imageView.GetResourceUrl();
```

`IMAGE` 속성을 사용하여 이미지를 설정할 수도 있습니다:

```cpp
imageView.SetProperty(Dali::Ui::ImageView::Property::IMAGE, "path/to/image.jpg");
```

## 피팅 모드

`FittingMode`는 이미지가 뷰 영역에 맞춰 크기 조정되는 방식을 제어합니다. `SetFittingMode()`로 모드를 지정합니다.

| 모드 | 설명 |
|------|------|
| `FIT_KEEP_ASPECT_RATIO` | 가로세로비를 유지하며 뷰 내에 맞춤. 이미지가 전체 뷰를 채우지 않을 수 있음. |
| `FILL` | 전체 뷰 영역을 채우도록 이미지 늘림. 가로세로비 왜곡 가능. |
| `OVER_FIT_KEEP_ASPECT_RATIO` | 가로세로비를 유지하며 뷰를 덮도록 크기 조정. 일부 잘림 가능. |
| `CENTER` | 원래 크기로 크기 조정 없이 이미지 중앙 배치. |

```cpp
imageView.SetFittingMode(Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);
```

현재 피팅 모드를 조회합니다:

```cpp
Dali::Ui::Image::FittingMode mode = imageView.GetFittingMode();
```

## 샘플링 및 원하는 크기

### 샘플링 모드

`SamplingMode`는 이미지 크기 조정 시 사용되는 필터링 방법을 제어합니다. 작은 이미지가 원래 크기보다 크게 표시될 때 특히 눈에 띕니다.

| 모드 | 설명 |
|------|------|
| `NEAREST` | 최근접 이웃 샘플링. 빠르지만 픽셀화될 수 있음. |
| `LINEAR` | 이중선형 필터링. 대부분의 경우 부드러운 결과. |
| `BOX_THEN_NEAREST` | 박스 필터 후 최근접 이웃. 축소에 적합. |
| `BOX_THEN_LINEAR` | 박스 필터 후 선형 필터링. 부드러운 축소. |

```cpp
imageView.SetSamplingMode(Dali::Ui::Image::SamplingMode::LINEAR);
```

### 원하는 크기

`DesiredWidth`와 `DesiredHeight`는 이미지 로더에 대상 해상도에 대한 힌트를 제공합니다. 메모리 절약을 위해 큰 이미지를 낮은 해상도로 로드할 때 유용합니다.

```cpp
imageView.SetDesiredWidth(128);
imageView.SetDesiredHeight(128);
imageView.Reload(); // 새 원하는 크기 적용에 필요
```

현재 원하는 크기를 조회합니다:

```cpp
int width = imageView.GetDesiredWidth();
int height = imageView.GetDesiredHeight();
```

## 알파 마스킹

`ImageView`는 이미지에 커스텀 모양을 적용하기 위한 알파 마스킹을 지원합니다. `SetAlphaMaskUrl()`로 알파 채널이 가시 영역을 정의하는 마스크 이미지를 지정합니다.

```cpp
imageView.SetAlphaMaskUrl("path/to/mask.png");
```

### 마스크로 자르기

기본적으로 이미지는 마스크가 적용된 원래 크기를 유지합니다. 결과를 마스크의 경계 상자로 자르려면 `CropToMask`를 활성화합니다.

```cpp
imageView.SetCropToMask(true);
```

### 마스킹 모드

`SetMaskingMode()`로 마스킹이 적용되는 시점을 제어합니다:

| 모드 | 설명 |
|------|------|
| `MASKING_ON_RENDERING` | 렌더링 중에 마스크 적용. 동적 마스크 변경 지원. |
| `MASKING_ON_LOADING` | 이미지 로드 중에 마스크 적용. 더 효율적이지만 마스크 변경 후 `Reload()` 필요. |

```cpp
imageView.SetMaskingMode(Dali::Ui::Image::MaskingType::MASKING_ON_RENDERING);
```

현재 마스킹 구성을 조회합니다:

```cpp
Dali::String maskUrl = imageView.GetAlphaMaskUrl();
bool cropToMask = imageView.IsCropToMask();
Dali::Ui::Image::MaskingType maskingMode = imageView.GetMaskingMode();
```

## 부분 표시를 위한 픽셀 영역

`PixelArea`는 이미지의 하위 영역을 표시할 수 있습니다. 영역은 [0, 1] 범위의 정규화된 좌표 (x, y, width, height)로 지정합니다.

```cpp
// 왼쪽 상단 사분면 표시
imageView.SetPixelArea(Dali::Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// 중앙 영역 표시
imageView.SetPixelArea(Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));
```

현재 픽셀 영역을 조회합니다:

```cpp
Dali::Vector4 pixelArea = imageView.GetPixelArea();
```

`PIXEL_AREA` 속성은 애니메이션 가능하여 부드러운 팬 및 줌 효과를 가능하게 합니다:

```cpp
Dali::Animation animation = Dali::Animation::New(2.0f);
animation.AnimateTo(
  Dali::Property(imageView, Dali::Ui::ImageView::Property::PIXEL_AREA),
  Dali::Vector4(0.25f, 0.25f, 0.5f, 0.5f));
animation.Play();
```

## 이미지 색상 틴팅

`SetImageColor()`는 이미지에 색상 승수를 적용합니다. 원래 이미지를 표시하려면 흰색(1.0, 1.0, 1.0, 1.0)을 사용하거나 시각 효과를 위해 틴팅을 적용합니다.

```cpp
// 틴트 없음 (원래 색상)
imageView.SetImageColor(Dali::UiColor(1.0f, 1.0f, 1.0f, 1.0f));

// 빨간색 틴트
imageView.SetImageColor(Dali::UiColor(1.0f, 0.0f, 0.0f, 1.0f));

// 50% 투명
imageView.SetImageColor(Dali::UiColor(1.0f, 1.0f, 1.0f, 0.5f));

// 그레이스케일 효과
imageView.SetImageColor(Dali::UiColor(0.5f, 0.5f, 0.5f, 1.0f));
```

현재 이미지 색상을 조회합니다:

```cpp
Dali::UiColor color = imageView.GetImageColor();
```

## 로드 정책 및 리소스 관리

### 로드 정책

`LoadPolicy`는 이미지 로딩이 시작되는 시점을 제어합니다.

| 정책 | 설명 |
|--------|-------------|
| `ATTACHED` | 뷰가 장면에 추가될 때 로딩 시작. |
| `IMMEDIATE` | 장면 연결 전, URL 설정 시 즉시 로딩 시작. |

```cpp
imageView.SetLoadPolicy(Dali::Ui::Image::LoadPolicy::ATTACHED);
```

### 해제 정책

`ReleasePolicy`는 이미지 데이터가 메모리에서 해제되는 시점을 제어합니다.

| 정책 | 설명 |
|--------|-------------|
| `DETACHED` | 뷰가 장면에서 제거될 때 해제. |
| `DESTROYED` | 뷰가 소멸될 때 해제. |
| `NEVER` | 해제하지 않음; 이미지를 메모리에 유지. |

```cpp
imageView.SetReleasePolicy(Dali::Ui::Image::ReleasePolicy::DETACHED);
```

### 동기 로딩

차단 로드가 허용되는 작은 이미지의 경우 동기 로딩을 활성화합니다:

```cpp
imageView.SetSynchronousLoading(true);
```

### 패스트랙 업로드

이미지 크기가 뷰 크기와 일치할 때 더 빠른 GPU 업로드를 위해 패스트랙 업로드를 활성화합니다:

```cpp
imageView.SetFastTrackUpload(true);
```

현재 정책을 조회합니다:

```cpp
Dali::Ui::Image::LoadPolicy loadPolicy = imageView.GetLoadPolicy();
Dali::Ui::Image::ReleasePolicy releasePolicy = imageView.GetReleasePolicy();
bool syncLoading = imageView.IsSynchronousLoading();
bool fastTrack = imageView.IsFastTrackUploadEnabled();
```

## 플레이스홀더 이미지

메인 이미지가 로드되는 동안 표시할 플레이스홀더 이미지를 설정합니다:

```cpp
imageView.SetPlaceholderUrl("path/to/placeholder.png");
```

메인 이미지 로드가 완료되면 플레이스홀더가 자동으로 교체됩니다.

```cpp
Dali::String placeholderUrl = imageView.GetPlaceholderUrl();
```

## N-패치 이미지

N-패치(9-패치) 이미지는 고정된 테두리와 늘어날 수 있는 영역을 허용합니다. `SetNPatchBorder()`로 테두리를 설정합니다:

```cpp
// 테두리: 왼쪽, 오른쪽, 위, 아래
imageView.SetNPatchBorder(Dali::Vector4(10.0f, 10.0f, 10.0f, 10.0f));
```

테두리만 표시하려면 (프레임 같은 UI 요소용):

```cpp
imageView.SetNPatchBorderOnly(true);
```

N-패치 설정을 조회합니다:

```cpp
Dali::Vector4 border = imageView.GetNPatchBorder();
bool borderOnly = imageView.IsNPatchBorderOnly();
```

## 리소스 준비 시그널

이미지 로드가 완료되면 알림을 받으려면 `ResourceReadySignal()`에 연결합니다:

```cpp
void OnResourceReady(Dali::Ui::ImageView view)
{
  Dali::Ui::Visual::ResourceStatus status = view.GetLoadingStatus();
  // 로드된 이미지 처리
}

imageView.ResourceReadySignal().Connect(&OnResourceReady);
```

언제든지 로딩 상태를 확인합니다:

```cpp
Dali::Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();
```

### 이미지 다시 로드

현재 이미지를 다시 로드하려면 `Reload()`을 호출합니다. 원하는 크기나 마스킹 모드와 같이 로딩에 영향을 주는 속성을 변경한 후 유용합니다:

```cpp
imageView.SetDesiredWidth(256);
imageView.SetDesiredHeight(256);
imageView.Reload();
```

## 추가 속성

| 속성 | 설명 |
|----------|-------------|
| `ORIENTATION_CORRECTION` | EXIF 데이터에서 자동 방향 보정 활성화/비활성화. |
| `PRE_MULTIPLIED_ALPHA` | 프리멀티플라이드 알파 블렌딩 사용. |
| `IMAGE_LOAD_WITH_VIEW_SIZE` | 이미지 로딩 해상도 힌트로 뷰 크기 사용. |
| `DEPTH_INDEX` | 렌더링 순서를 위한 깊이 인덱스 설정. |

```cpp
imageView.SetOrientationCorrection(true);
imageView.SetPreMultipliedAlpha(false);
imageView.SetImageLoadWithViewSize(true);
imageView.SetDepthIndex(0);
```

이 설정들을 조회합니다:

```cpp
bool orientationCorrection = imageView.IsOrientationCorrectionEnabled();
bool preMultiplied = imageView.IsPreMultipliedAlpha();
bool loadWithViewSize = imageView.IsImageLoadWithViewSizeEnabled();
```

## 타입 캐스팅

`DownCast()`를 사용하여 `BaseHandle`이나 `View`를 `ImageView`로 안전하게 캐스팅합니다:

```cpp
Dali::Ui::ImageView imageView = Dali::Ui::ImageView::DownCast(view);
if (imageView)
{
  // ImageView로 성공적으로 캐스팅
  imageView.SetResourceUrl("new-image.jpg");
}
```
