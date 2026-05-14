---
title: 이미지
sidebar_label: 이미지
category: images-visuals
---

# 이미지

ImageView와 AnimatedImageView는 dali-ui 애플리케이션에서 정적 및 애니메이션 이미지를 표시하는 기본 API를 제공합니다.

## 목차

- [ImageView 생성](#imageview-생성)
- [이미지 피팅 및 샘플링](#이미지-피팅-및-샘플링)
- [로드 정책](#로드-정책)
- [애니메이션 이미지](#애니메이션-이미지)
- [픽셀 영역 및 하위 영역](#픽셀-영역-및-하위-영역)
- [알파 마스킹](#알파-마스킹)
- [리소스 시그널](#리소스-시그널)

## ImageView 생성

`ImageView::New()`로 이미지 뷰를 생성합니다. 파일 경로나 리소스에서 이미지를 로드하려면 URL을 전달합니다.

```cpp
#include <dali-ui-foundation/public-api/image-view.h>

using namespace Dali;
using namespace Dali::Ui;

// 빈 ImageView 생성
ImageView imageView = ImageView::New();

// 이미지 URL로 ImageView 생성
ImageView imageView = ImageView::New("images/photo.jpg");

// 생성 후 이미지 URL 설정
imageView.SetResourceUrl("images/another.png");
```

이미지는 기본적으로 비동기로 로드됩니다. 로드 완료 알림을 받으려면 `ResourceReadySignal()`을 사용합니다.

## 이미지 피팅 및 샘플링

`SetFittingMode()`로 이미지가 뷰 경계에 맞춰 크기 조정되는 방식을 제어합니다. 기본값은 `Ui::Image::FittingMode::FILL`로, 이미지를 늘려 뷰를 채웁니다.

```cpp
// 가로세로비 유지 (빈 영역 표시 가능)
imageView.SetFittingMode(Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO);

// 뷰를 채우도록 늘림 (왜곡 가능)
imageView.SetFittingMode(Ui::Image::FittingMode::FILL);

// 가로세로비 유지하며 채움, 오버플로우 잘림
imageView.SetFittingMode(Ui::Image::FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// 원래 이미지 크기로 표시
imageView.SetFittingMode(Ui::Image::FittingMode::CENTER);
```

`SetSamplingMode()`로 크기 조정 시 필터링 품질을 제어합니다:

```cpp
// 최근접 이웃 샘플링 (픽셀화된 외관)
imageView.SetSamplingMode(Ui::Image::SamplingMode::NEAREST);

// 선형 필터링 (부드러움)
imageView.SetSamplingMode(Ui::Image::SamplingMode::LINEAR);

// 고품질 Lanczos 필터링
imageView.SetSamplingMode(Ui::Image::SamplingMode::LANCZOS);
```

## 로드 정책

이미지가 로드되고 텍스처 캐시에서 해제되는 시점을 제어합니다.

### 로드 정책

`Ui::Image::LoadPolicy`는 로딩이 시작되는 시점을 결정합니다:

```cpp
// 뷰가 생성될 때 즉시 로드
imageView.SetLoadPolicy(Ui::Image::LoadPolicy::IMMEDIATE);

// 뷰가 장면에 추가될 때까지 로드 지연 (기본값)
imageView.SetLoadPolicy(Ui::Image::LoadPolicy::ATTACHED);
```

### 해제 정책

`Ui::Image::ReleasePolicy`는 텍스처가 캐시에서 해제되는 시점을 제어합니다:

```cpp
// 뷰가 장면에서 제거될 때 해제
imageView.SetReleasePolicy(Ui::Image::ReleasePolicy::DETACHED);

// 뷰가 소멸될 때 해제
imageView.SetReleasePolicy(Ui::Image::ReleasePolicy::DESTROYED);

// 해제 안 함 (캐시 유지)
imageView.SetReleasePolicy(Ui::Image::ReleasePolicy::NEVER);
```

### 동기 로딩

차단이 허용되는 작은 이미지의 경우 동기 로딩을 활성화합니다:

```cpp
imageView.SetSynchronousLoading(true);
```

### 패스트랙 업로딩

메인 스레드 지연을 줄이기 위해 백그라운드 GPU 업로드를 활성화합니다:

```cpp
imageView.SetFastTrackUpload(true);
```

## 애니메이션 이미지

`AnimatedImageView`는 재생 제어 기능으로 애니메이션 GIF, WebP, 프레임 시퀀스를 표시합니다.

### 기본 재생

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

AnimatedImageView animView = AnimatedImageView::New("animations/spinner.gif");

// 재생 시작
animView.Play();

// 재생 일시 정지
animView.Pause();

// 정지하고 첫 프레임으로 재설정
animView.Stop();
```

### 반복 횟수

애니메이션이 반복되는 횟수를 설정합니다:

```cpp
// 무한 반복
animView.SetLoopCount(-1);

// 정확히 3번 반복
animView.SetLoopCount(3);

// 한 번만 재생
animView.SetLoopCount(1);
```

### 프레임 제어

특정 프레임으로 이동하고 재생 속도를 제어합니다:

```cpp
// 5번 프레임으로 이동
animView.JumpToFrame(5);

// 현재 프레임 가져오기
int currentFrame = animView.GetCurrentFrame();

// 전체 프레임 수 가져오기
int totalFrames = animView.GetTotalFrame();

// 재생 속도 높이기 (2배)
animView.SetFrameSpeedFactor(2.0f);

// 재생 속도 낮추기 (0.5배)
animView.SetFrameSpeedFactor(0.5f);
```

### 정지 동작

정지 시 표시할 프레임을 제어합니다:

```cpp
// 정지 시 현재 프레임 표시
animView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::CURRENT_FRAME);

// 정지 시 첫 프레임 표시
animView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::FIRST_FRAME);

// 정지 시 마지막 프레임 표시
animView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::LAST_FRAME);
```

### URL로부터 프레임 시퀀스

개별 프레임 이미지에서 애니메이션을 로드합니다:

```cpp
Dali::Vector<Dali::String> urls;
urls.PushBack(Dali::String("frames/frame-001.png"));
urls.PushBack(Dali::String("frames/frame-002.png"));
urls.PushBack(Dali::String("frames/frame-003.png"));

animView.SetBatchSize(4);  // 한 번에 4개 프레임 프리로드
animView.SetCacheSize(10); // 10개 프레임 캐시
animView.SetFrameDelay(100); // 프레임 간 100ms
animView.SetResourceUrls(urls);
animView.Play();
```

## 픽셀 영역 및 하위 영역

`SetPixelArea()`를 사용하여 이미지의 일부를 표시합니다. 영역은 `[0, 1]` 범위의 정규화된 좌표 `(x, y, width, height)`로 지정합니다.

```cpp
// 전체 이미지 표시
imageView.SetPixelArea(Vector4(0.0f, 0.0f, 1.0f, 1.0f));

// 왼쪽 상단 사분면 표시
imageView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));

// 중앙 영역 표시
imageView.SetPixelArea(Vector4(0.25f, 0.25f, 0.5f, 0.5f));
```

픽셀 영역은 `ImageView`와 `AnimatedImageView` 모두에서 작동합니다:

```cpp
AnimatedImageView animView = AnimatedImageView::New("animation.gif");
animView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));
animView.Play();
```

## 알파 마스킹

알파 마스크를 적용하여 이미지 모양을 형성합니다:

```cpp
// 마스크 이미지 URL 설정
imageView.SetAlphaMaskUrl("masks/rounded-corner-mask.png");

// 이미지를 마스크 경계로 자르기
imageView.SetCropToMask(true);

// 마스킹 모드 설정
imageView.SetMaskingMode(Ui::Image::MaskingType::MASKING_ON_LOADING);
```

## 리소스 시그널

이미지 로드 완료 알림을 받으려면 `ResourceReadySignal()`에 연결합니다:

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupImageView()
  {
    mImageView = ImageView::New("images/large-photo.jpg");
    mImageView.ResourceReadySignal().Connect(this, &MyController::OnImageReady);
  }

private:
  void OnImageReady(View view)
  {
    ImageView iv = ImageView::DownCast(view);
    if(iv && iv.GetLoadingStatus() == Ui::Visual::ResourceStatus::READY)
    {
      // 이미지 표시 준비 완료
    }
  }

  ImageView mImageView;
};
```

애니메이션 이미지의 경우, 재생 완료 감지를 위해 `AnimationFinishedSignal()`에 연결합니다:

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupAnimatedView()
  {
    mAnimView = AnimatedImageView::New("animation.gif");
    mAnimView.SetLoopCount(3);
    mAnimView.AnimationFinishedSignal().Connect(this, &MyController::OnAnimationFinished);
    mAnimView.Play();
  }

private:
  void OnAnimationFinished(View view)
  {
    // 애니메이션이 모든 루프를 완료함
  }

  AnimatedImageView mAnimView;
};
```

### 로딩 상태 확인

현재 로딩 상태를 조회합니다:

```cpp
Ui::Visual::ResourceStatus status = imageView.GetLoadingStatus();

if(status == Ui::Visual::ResourceStatus::PREPARING)
{
  // 로딩 중
}
else if(status == Ui::Visual::ResourceStatus::READY)
{
  // 준비됨
}
else if(status == Ui::Visual::ResourceStatus::FAILED)
{
  // 로드 실패
}
```

### 이미지 다시 로드

현재 이미지를 강제로 다시 로드합니다:

```cpp
imageView.Reload();
```
