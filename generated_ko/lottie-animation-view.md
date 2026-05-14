---
title: Lottie 애니메이션 뷰
sidebar_label: Lottie 애니메이션 뷰
category: images-visuals
---

# Lottie 애니메이션 뷰

`LottieAnimationView`는 Adobe After Effects에서 Bodymovin 플러그인으로 내보낸 JSON 파일에서 벡터 기반 애니메이션을 렌더링합니다. 고품질의 확장 가능한 애니메이션을 재생 제어, 루핑 모드, 프레임 수준 조작과 함께 제공합니다.

## 목차

- [LottieAnimationView 생성](#lottieanimationview-생성)
- [재생 제어](#재생-제어)
- [루핑 구성](#루핑-구성)
- [프레임 범위 및 마커](#프레임-범위-및-마커)
- [속도 및 타이밍](#속도-및-타이밍)
- [시각적 스타일링](#시각적-스타일링)
- [시그널](#시그널)

## LottieAnimationView 생성

`LottieAnimationView::New()`를 호출하여 `LottieAnimationView`를 생성합니다. 생성 시 Lottie JSON 파일의 URL을 전달하거나 나중에 `SetResourceUrl()`로 설정할 수 있습니다.

```cpp
using namespace Dali::Ui;

// URL로 생성
LottieAnimationView lottieView = LottieAnimationView::New("animations/loading.json");

// 비어있게 생성하고 나중에 URL 설정
LottieAnimationView lottieView = LottieAnimationView::New();
lottieView.SetResourceUrl("animations/loading.json");
```

뷰를 뷰 계층에 추가하고 크기를 구성합니다:

```cpp
lottieView.SetRequestedWidth(200.0f)
          .SetRequestedHeight(200.0f);
window.Add(lottieView);
```

## 재생 제어

`Play()`, `Pause()`, `Stop()`으로 애니메이션 재생을 제어합니다. 모든 재생 메서드는 메서드 체이닝을 위해 뷰의 참조를 반환합니다.

```cpp
// 재생 시작 또는 재개
lottieView.Play();

// 현재 프레임에서 일시 정지
lottieView.Pause();

// 정지 및 정지 동작 적용
lottieView.Stop();
```

`GetPlayState()`로 현재 재생 상태를 조회합니다:

```cpp
if (lottieView.GetPlayState() == Ui::AnimatedImage::PlayState::PLAYING)
{
    // 애니메이션이 현재 재생 중
}
```

`JumpToFrame()`으로 특정 프레임으로 직접 이동합니다:

```cpp
// 프레임 30으로 이동
lottieView.JumpToFrame(30);

// 현재 및 전체 프레임 조회
int currentFrame = lottieView.GetCurrentFrame();
int totalFrames = lottieView.GetTotalFrame();
```

### 정지 동작

`SetStopBehavior()`로 애니메이션이 정지하는 위치를 구성합니다. `StopBehavior` 열거형은 세 가지 옵션을 제공합니다:

- `StopBehavior::CURRENT_FRAME`: 현재 프레임에서 정지 (기본값)
- `StopBehavior::FIRST_FRAME`: 정지하고 첫 번째 프레임으로 이동
- `StopBehavior::LAST_FRAME`: 정지하고 마지막 프레임으로 이동

```cpp
lottieView.SetStopBehavior(Ui::AnimatedImage::StopBehavior::FIRST_FRAME);
lottieView.Stop(); // 첫 번째 프레임에서 정지
```

## 루핑 구성

### 루프 횟수

`SetLoopCount()`로 애니메이션이 반복되는 횟수를 설정합니다. 무한 루프에는 `-1`을 사용합니다. 값이 `0`이면 애니메이션이 재생되지 않습니다.

```cpp
// 무한 루프
lottieView.SetLoopCount(-1);

// 3번 재생
lottieView.SetLoopCount(3);
```

### 루핑 모드

`SetLoopingMode()`로 루핑 동작을 제어합니다:

- `LoopingMode::RESTART` - 처음부터 다시 시작 (기본값)
- `LoopingMode::AUTO_REVERSE` - 앞뒤로 진동

```cpp
lottieView.SetLoopingMode(Ui::AnimatedImage::LoopingMode::AUTO_REVERSE);
```

## 프레임 범위 및 마커

### 프레임 범위 설정

애니메이션의 특정 섹션만 재생합니다:

```cpp
// 프레임 10에서 50까지만 재생
lottieView.SetStartFrame(10);
lottieView.SetEndFrame(50);
```

### 마커 사용

Lottie 파일의 마커를 사용하여 재생 범위를 정의합니다:

```cpp
// 마커 이름으로 범위 설정
lottieView.SetMarker("StartMarker", "EndMarker");

// 마커로 이동
lottieView.JumpToMarker("MyMarker");
```

## 속도 및 타이밍

### 재생 속도

`SetFrameSpeedFactor()`로 재생 속도를 조정합니다:

```cpp
// 2배 속도
lottieView.SetFrameSpeedFactor(2.0f);

// 절반 속도
lottieView.SetFrameSpeedFactor(0.5f);
```

## 시각적 스타일링

### 배경색

```cpp
lottieView.SetBackgroundColor(UiColor(0x000000));
```

### 크기 조정 모드

```cpp
lottieView.SetResizePolicy(ResizePolicy::FILL);
```

## 시그널

LottieAnimationView는 다음 시그널을 제공합니다:

- `AnimationStartedSignal()` - 애니메이션 시작
- `AnimationFinishedSignal()` - 애니메이션 완료
- `AnimationStoppedSignal()` - 애니메이션 정지
- `AnimationPausedSignal()` - 애니메이션 일시 정지

```cpp
class MyController : public ConnectionTracker
{
public:
  void SetupLottie(LottieAnimationView lottieView)
  {
    lottieView.AnimationFinishedSignal().Connect(this, &MyController::OnAnimationFinished);
  }

  void OnAnimationFinished(LottieAnimationView view)
  {
    // 애니메이션 완료 처리
  }
};
```
