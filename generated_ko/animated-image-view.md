---
title: 애니메이션 이미지뷰
sidebar_label: 애니메이션 이미지뷰
category: images-visuals
---

# 애니메이션 이미지뷰

`AnimatedImageView`는 GIF 및 애니메이션 WebP 파일과 같은 애니메이션 이미지나 URL로 제공되는 이미지 프레임 시퀀스를 표시합니다.

## 목차

- [AnimatedImageView 생성](#animatedimageview-생성)
- [애니메이션 이미지 로드](#애니메이션-이미지-로드)
- [재생 제어](#재생-제어)
- [애니메이션 설정](#애니메이션-설정)
- [알파 마스킹](#알파-마스킹)
- [로드 및 캐싱](#로드-및-캐싱)
- [시그널](#시그널)
- [이미지 외관](#이미지-외관)

## AnimatedImageView 생성

`AnimatedImageView::New()`를 사용하여 `AnimatedImageView`를 생성합니다. 선택적으로 URL을 전달하여 애니메이션 이미지를 즉시 로드할 수 있습니다.

```cpp
using namespace Dali::Ui;

// 초기 이미지 없이 생성
AnimatedImageView animatedView = AnimatedImageView::New();

// 초기 이미지 URL로 생성
AnimatedImageView animatedView = AnimatedImageView::New("images/spinner.gif");
```

뷰 크기를 설정하고 뷰 계층에 추가합니다:

```cpp
animatedView.SetRequestedWidth(200.0f)
             .SetRequestedHeight(200.0f);
window.Add(animatedView);
```

## 애니메이션 이미지 로드

### 단일 애니메이션 파일

`SetResourceUrl()`을 사용하여 GIF나 WebP와 같은 단일 애니메이션 이미지 파일을 로드합니다:

```cpp
animatedView.SetResourceUrl("animations/loading.webp");
```

### 프레임 시퀀스 (URL 배열)

프레임별 애니메이션을 위해 `SetResourceUrls()`로 이미지 URL 벡터를 제공합니다. 개별 프레임 이미지(예: PNG 시퀀스)가 있을 때 유용합니다:

```cpp
Dali::Vector<Dali::String> frameUrls;
frameUrls.PushBack(Dali::String("frames/frame-001.png"));
frameUrls.PushBack(Dali::String("frames/frame-002.png"));
frameUrls.PushBack(Dali::String("frames/frame-003.png"));
// ... 더 많은 프레임 추가
animatedView.SetResourceUrls(frameUrls);
```

URL 배열을 사용할 때는 최적의 성능을 위해 배치 로드와 캐싱을 구성합니다:

```cpp
animatedView.SetBatchSize(4);  // 배치당 4개 프레임 로드
animatedView.SetCacheSize(10); // 최대 10개 프레임 캐시
```

## 재생 제어

`Play()`, `Pause()`, `Stop()`, `JumpToFrame()`으로 애니메이션 재생을 제어합니다.

### 재생, 일시 정지, 정지

```cpp
// 재생 시작 또는 재개
animatedView.Play();

// 현재 프레임에서 일시 정지
animatedView.Pause();

// 재생 정지 (동작은 StopBehavior에 따라 다름)
animatedView.Stop();
```

### 특정 프레임으로 이동

`JumpToFrame()`을 사용하여 특정 프레임 인덱스로 이동합니다:

```cpp
// 5번 프레임으로 이동
animatedView.JumpToFrame(5);
```

### 재생 상태 조회

현재 상태와 프레임 위치를 확인합니다:

```cpp
Ui::AnimatedImage::PlayState state = animatedView.GetPlayState();
int currentFrame = animatedView.GetCurrentFrame();
int totalFrames = animatedView.GetTotalFrame();

if(state == Ui::AnimatedImage::PlayState::PLAYING)
{
  // 애니메이션이 재생 중
}
```

## 애니메이션 설정

### 반복 횟수

`SetLoopCount()`로 애니메이션이 반복되는 횟수를 제어합니다. 무한 반복에는 `-1`을 사용합니다:

```cpp
// 무한 반복
animatedView.SetLoopCount(-1);

// 정확히 3번 반복
animatedView.SetLoopCount(3);

// 한 번만 재생
animatedView.SetLoopCount(1);
```

참고: 반복 횟수가 `0`이면 애니메이션이 재생되지 않습니다.

### 프레임 속도 계수

`SetFrameSpeedFactor()`로 재생 속도를 조정합니다. `1.0f`보다 큰 값은 애니메이션을 빠르게, `1.0f`보다 작은 값은 느리게 만듭니다:

```cpp
// 절반 속도
animatedView.SetFrameSpeedFactor(0.5f);

// 정상 속도 (기본값)
animatedView.SetFrameSpeedFactor(1.0f);

// 두 배 속도
animatedView.SetFrameSpeedFactor(2.0f);
```

### 프레임 지연

`SetFrameDelay()`로 URL 배열 애니메이션의 프레임 지연을 재정의합니다. 값은 밀리초 단위입니다:

```cpp
// 각 프레임을 500ms 동안 표시
animatedView.SetFrameDelay(500);
```

### 정지 동작

`SetStopBehavior()`로 `Stop()`이 호출될 때 애니메이션이 정지하는 위치를 정의합니다:

```cpp
using namespace Dali::Ui::AnimatedImage;

// 현재 프레임에서 정지
animatedView.SetStopBehavior(StopBehavior::CURRENT_FRAME);

// 첫 번째 프레임으로 돌아가서 정지
animatedView.SetStopBehavior(StopBehavior::FIRST_FRAME);

// 마지막 프레임으로 진행하여 정지
animatedView.SetStopBehavior(StopBehavior::LAST_FRAME);
```

## 알파 마스킹

알파 마스크를 적용하여 애니메이션 이미지의 가시 영역을 형성합니다.

### 기본 알파 마스킹

`SetAlphaMaskUrl()`로 마스크 이미지 URL을 설정합니다:

```cpp
animatedView.SetAlphaMaskUrl("masks/circle-mask.png");
```

### 마스크로 자르기

`SetCropToMask()`로 이미지를 마스크의 경계 상자로 자릅니다:

```cpp
animatedView.SetCropToMask(true);
```

`cropToMask`가 `false`이면 전체 이미지가 마스크의 알파가 적용되어 표시됩니다. `true`이면 이미지가 마스크의 경계 상자로 잘립니다.

### 마스킹 모드

`SetMaskingMode()`로 마스킹이 발생하는 시점을 선택합니다:

```cpp
using namespace Dali::Ui::Image;

// 렌더링 중에 마스크 적용 (기본값)
animatedView.SetMaskingMode(MaskingType::MASKING_ON_RENDERING);

// 로드 중에 마스크 적용 (정적 마스크에 더 효율적)
animatedView.SetMaskingMode(MaskingType::MASKING_ON_LOADING);
```

## 로드 및 캐싱

### 로드 정책

`SetLoadPolicy()`로 이미지가 로드되는 시점을 제어합니다:

```cpp
using namespace Dali::Ui::Image;

// 뷰가 장면에 연결될 때 로드 (기본값)
animatedView.SetLoadPolicy(LoadPolicy::ATTACHED);

// 장면에 추가되기 전에 즉시 로드
animatedView.SetLoadPolicy(LoadPolicy::IMMEDIATE);
```

### 해제 정책

`SetReleasePolicy()`로 이미지 리소스가 해제되는 시점을 제어합니다:

```cpp
using namespace Dali::Ui::Image;

// 뷰가 장면에서 분리될 때 해제
animatedView.SetReleasePolicy(ReleasePolicy::DETACHED);

// 뷰가 소멸될 때 해제
animatedView.SetReleasePolicy(ReleasePolicy::DESTROYED);

// 자동으로 해제하지 않음
animatedView.SetReleasePolicy(ReleasePolicy::NEVER);
```

### 동기 로드

완료될 때까지 차단되는 즉시 로드를 위해 `SetSynchronousLoading()`을 사용합니다:

```cpp
animatedView.SetSynchronousLoading(true);
```

`GetLoadingStatus()`로 로드 상태를 확인합니다:

```cpp
Ui::Visual::ResourceStatus status = animatedView.GetLoadingStatus();
```

### 플레이스홀더 이미지

메인 이미지가 로드되는 동안 플레이스홀더를 표시합니다:

```cpp
animatedView.SetPlaceholderUrl("images/placeholder.png");
```

## 시그널

### 애니메이션 완료 시그널

애니메이션이 모든 루프를 완료했을 때 알림을 받으려면 `AnimationFinishedSignal()`에 연결합니다:

```cpp
animatedView.AnimationFinishedSignal().Connect(this, &MyClass::OnAnimationFinished);

// 핸들러 시그니처
void OnAnimationFinished(Dali::Ui::View view)
{
  // 애니메이션이 모든 루프를 완료함
}
```

### 리소스 준비 시그널

이미지 리소스가 표시 준비가 되었을 때 알림을 받으려면 `ResourceReadySignal()`에 연결합니다:

```cpp
animatedView.ResourceReadySignal().Connect(this, &MyClass::OnResourceReady);

// 핸들러 시그니처
void OnResourceReady(Dali::Ui::View view)
{
  int totalFrames = animatedView.GetTotalFrame();
  // 리소스가 로드되어 준비됨
}
```

## 이미지 외관

### 피팅 모드

`SetFittingMode()`로 이미지가 뷰 경계 내에 맞춰지는 방식을 제어합니다:

```cpp
using namespace Dali::Ui::Image;

// 가로세로비를 유지하며 경계 내에 맞춤 (레터박스 발생 가능)
animatedView.SetFittingMode(FittingMode::FIT_KEEP_ASPECT_RATIO);

// 가로세로비를 유지하지 않고 경계를 채우도록 늘림 (기본값)
animatedView.SetFittingMode(FittingMode::FILL);

// 가로세로비를 유지하며 경계를 채움 (오버플로우 잘림 가능)
animatedView.SetFittingMode(FittingMode::OVER_FIT_KEEP_ASPECT_RATIO);

// 원래 크기로 중앙에 표시
animatedView.SetFittingMode(FittingMode::CENTER);
```

### 샘플링 모드

`SetSamplingMode()`로 필터링 품질을 제어합니다:

```cpp
using namespace Dali::Ui::Image;

// 부드러운 필터링 (기본값)
animatedView.SetSamplingMode(SamplingMode::BOX_THEN_LINEAR);

// 픽셀화/선명한 필터링
animatedView.SetSamplingMode(SamplingMode::NEAREST);
```

### 원하는 크기

메모리 효율성을 향상시킬 수 있는 이미지 로드 대상 크기를 지정합니다:

```cpp
animatedView.SetDesiredWidth(256);
animatedView.SetDesiredHeight(256);
```

### 이미지 색상

`SetImageColor()`로 이미지에 색조를 적용합니다:

```cpp
// 파란색 색조 적용
animatedView.SetImageColor(UiColor(0.2f, 0.4f, 0.8f, 1.0f));

// 반투명 오버레이 적용
animatedView.SetImageColor(UiColor(1.0f, 1.0f, 1.0f, 0.5f));
```

### 픽셀 영역

`SetPixelArea()`로 이미지의 하위 영역을 표시합니다. 값은 정규화된 좌표(0.0~1.0)의 `Vector4`로 `(x, y, width, height)`를 포함합니다:

```cpp
// 이미지의 왼쪽 상단 사분면 표시
animatedView.SetPixelArea(Vector4(0.0f, 0.0f, 0.5f, 0.5f));
```

### 뷰 크기로 로드

메모리 효율성을 위해 뷰 크기로 이미지 로드를 활성화합니다:

```cpp
animatedView.SetImageLoadWithViewSize(true);
```

### 프리멀티플라이드 알파

프리멀티플라이드 알파 처리를 제어합니다:

```cpp
animatedView.SetPreMultipliedAlpha(true);
```
