---
title: 애니메이션
sidebar_label: 애니메이션
category: animation-motion
---

# 애니메이션

`Animation` API는 `Dali::Ui::View` 객체에 대한 속성 기반 애니메이션을 제공합니다. 알파 함수, 키프레임, 루핑 동작을 지원하는 플루언트 API를 사용하여 변환 속성, 색상 및 커스텀 애니메이션 가능 속성을 애니메이션할 수 있습니다.

## 목차

- [애니메이션 생성](#애니메이션-생성)
- [속성 애니메이션](#속성-애니메이션)
- [알파 함수](#알파-함수)
- [키프레임 애니메이션](#키프레임-애니메이션)
- [재생 제어](#재생-제어)
- [루핑 및 속도](#루핑-및-속도)
- [애니메이션 시그널](#애니메이션-시그널)
- [뷰 우선 애니메이션 패턴](#뷰-우선-애니메이션-패턴)

## 애니메이션 생성

초 단위 지속 시간으로 `Animation::New()`를 사용하여 `Animation` 인스턴스를 생성합니다:

```cpp
// 500ms 애니메이션 생성
Animation anim = Animation::New(0.5f);
```

고정된 지속 시간 없이 애니메이션을 생성할 수도 있습니다:

```cpp
// 애니메이션 생성; 애니메이션이 추가되면 지속 시간이 연장됨
Animation anim = Animation::New();
```

생성 후 `SetDuration()`으로 지속 시간을 수정할 수 있습니다:

```cpp
anim.SetDuration(1.0f); // 1초
```

## 속성 애니메이션

### AnimateTo

`AnimateTo()`를 사용하여 속성을 목표 값으로 애니메이션합니다:

```cpp
Animation anim = Animation::New(0.3f);

// 불투명도를 0.0으로 애니메이션
anim.AnimateTo(Property(view, Actor::Property::OPACITY), 0.0f);

// 위치 애니메이션
anim.AnimateTo(Property(view, Actor::Property::POSITION), Vector3(100.0f, 50.0f, 0.0f));

// 크기 애니메이션
anim.AnimateTo(Property(view, Actor::Property::SCALE), Vector3(1.5f, 1.5f, 1.0f));

anim.Play();
```

### AnimateBy

`AnimateBy()`를 사용하여 속성을 상대적 오프셋만큼 애니메이션합니다:

```cpp
Animation anim = Animation::New(0.3f);

// 뷰를 Y 방향으로 50픽셀 이동
anim.AnimateBy(Property(view, Actor::Property::POSITION), Vector3(0.0f, 50.0f, 0.0f));

anim.Play();
```

### 시간 구간

`TimePeriod`를 사용하여 지연과 지속 시간을 지정합니다:

```cpp
// 100ms 지연 후 300ms 동안 애니메이션
anim.AnimateTo(
    Property(view, Actor::Property::OPACITY),
    0.0f,
    TimePeriod(0.1f, 0.3f)); // TimePeriod(지연초, 지속초)
```

## 알파 함수

알파 함수는 보간 곡선을 정의합니다. 애니메이션의 기본 알파 함수를 설정하거나 애니메이션별로 지정합니다:

```cpp
// 애니메이션의 기본 알파 함수 설정
anim.SetDefaultAlphaFunction(AlphaFunction::EASE_IN_OUT);

// 또는 AnimateTo 호출별로 알파 함수 지정
anim.AnimateTo(
    Property(view, Actor::Property::POSITION_Y),
    100.0f,
    AlphaFunction::EASE_OUT,
    TimePeriod(0.3f));
```

### 내장 알파 함수

일반적인 알파 함수:

- `AlphaFunction::LINEAR` — 일정한 속도
- `AlphaFunction::EASE_IN` — 느린 시작
- `AlphaFunction::EASE_OUT` — 느린 끝
- `AlphaFunction::EASE_IN_OUT` — 느린 시작과 끝
- `AlphaFunction::BOUNCE` — 튕김 효과
- `AlphaFunction::SIN` — 사인파 이징

### 커스텀 알파 함수

베지어 제어점으로 커스텀 알파 함수를 생성합니다:

```cpp
// 두 개의 제어점을 가진 커스텀 베지어 곡선
AlphaFunction customAlpha(Vector2(0.2f, 0.8f), Vector2(0.3f, 1.0f));
anim.SetDefaultAlphaFunction(customAlpha);
```

## 키프레임 애니메이션

`KeyFrames`를 사용하여 복잡한 애니메이션을 위한 여러 키포인트를 정의합니다:

```cpp
KeyFrames keyFrames = KeyFrames::New();

// 진행 값(0.0~1.0)에서 키프레임 추가
keyFrames.Add(0.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));
keyFrames.Add(0.25f, Property::Value(Vector4(0.0f, 0.0f, 0.5f, 0.5f)));
keyFrames.Add(0.50f, Property::Value(Vector4(0.25f, 0.25f, 0.5f, 0.5f)));
keyFrames.Add(0.75f, Property::Value(Vector4(0.5f, 0.5f, 0.5f, 0.5f)));
keyFrames.Add(1.00f, Property::Value(Vector4(0.0f, 0.0f, 1.0f, 1.0f)));

Animation anim = Animation::New(3.0f);
anim.SetLooping(true);
anim.AnimateBetween(Property(view, Actor::Property::COLOR), keyFrames, AlphaFunction::EASE_IN_OUT);
anim.Play();
```

## 재생 제어

`Play()`, `Pause()`, `Stop()`으로 애니메이션 재생을 제어합니다:

```cpp
Animation anim = Animation::New(0.5f);
anim.AnimateTo(Property(view, Actor::Property::OPACITY), 0.0f);

// 재생 시작
anim.Play();

// 재생 일시 정지
anim.Pause();

// 정지하고 시작점으로 재설정
anim.Stop();
```

### 지연 재생

`PlayAfter()`를 사용하여 지연 후 재생을 시작합니다:

```cpp
// 0.5초 지연 후 애니메이션 시작
anim.PlayAfter(0.5f);
```

### 진행 지점에서 재생

`PlayFrom()`을 사용하여 특정 진행 지점에서 시작합니다:

```cpp
// 50% 진행 지점에서 시작
anim.PlayFrom(0.5f);
```

### 현재 진행

현재 진행을 조회하거나 설정합니다:

```cpp
float progress = anim.GetCurrentProgress();
anim.SetCurrentProgress(0.25f);
```

### 애니메이션 상태

`GetState()`로 현재 상태를 확인합니다:

```cpp
if (anim.GetState() == Animation::PLAYING)
{
    // 애니메이션이 현재 재생 중
}
```

## 루핑 및 속도

### 반복 횟수

`SetLoopCount()`로 반복 횟수를 설정합니다:

```cpp
// 3번 반복
anim.SetLoopCount(3);

// 무한 반복
anim.SetLooping(true);
```

현재 루프를 조회합니다:

```cpp
int32_t currentLoop = anim.GetCurrentLoop();
```

### 루핑 모드

`SetLoopingMode()`로 루핑 동작을 제어합니다:

```cpp
// 각 루프마다 처음부터 다시 시작
anim.SetLoopingMode(Animation::RESTART);

// 각 루프마다 방향 전환
anim.SetLoopingMode(Animation::AUTO_REVERSE);
```

### 속도 계수

`SetSpeedFactor()`로 재생 속도를 조정합니다:

```cpp
// 2배속으로 재생
anim.SetSpeedFactor(2.0f);

// 절반 속도로 재생
anim.SetSpeedFactor(0.5f);
```

### 재생 범위

`SetPlayRange()`로 재생을 하위 범위로 제한합니다:

```cpp
// 애니메이션의 25%~75% 구간만 재생
anim.SetPlayRange(Vector2(0.25f, 0.75f));
```

## 애니메이션 시그널

애니메이션이 완료되면 알림을 받으려면 `FinishedSignal()`에 연결합니다:

```cpp
anim.FinishedSignal().Connect(this, &MyClass::OnAnimationFinished);

void OnAnimationFinished(Animation anim)
{
    // 애니메이션 완료
}
```

### 종료 동작

`SetEndAction()`으로 애니메이션이 끝날 때 발생하는 동작을 제어합니다:

```cpp
// 애니메이션 종료 시 애니메이션된 속성 값 저장 (기본값)
anim.SetEndAction(Animation::BAKE);

// 애니메이션 종료 시 애니메이션된 속성 값 폐기
anim.SetEndAction(Animation::DISCARD);

// 정지된 경우, 애니메이션이 완료된 것처럼 값 저장
anim.SetEndAction(Animation::BAKE_FINAL);
```

### 연결 해제 동작

애니메이션이 연결 해제될 때 동작을 제어합니다:

```cpp
anim.SetDisconnectAction(Animation::BAKE);
```

## 뷰 우선 애니메이션 패턴

dali-ui 프레임워크는 `ViewAnimationBridge`와 `ViewAnimationSpec`을 사용하여 플루언트한 뷰 중심 애니메이션 API를 제공합니다.

### 브리지 패턴

`View::Animate()`를 사용하여 애니메이션 체이닝을 위한 브리지를 얻습니다:

```cpp
Animation anim = Animation::New();
view.Animate(anim)
    .Opacity(0.0f, 500_ms)
    .PositionY(100.0f, 300_ms, AlphaFunction::EASE_OUT);
anim.Play();
```

### 스펙 패턴

`View::NewAnimationSpec()`으로 재사용 가능한 애니메이션 스펙을 생성합니다:

```cpp
// 재사용 가능한 페이드인 스펙 생성
ViewAnimationSpec fadeInSpec = View::NewAnimationSpec()
    .Opacity(1.0f, 500_ms, AlphaFunction::EASE_IN_OUT);

// 임의의 뷰에 적용
Animation anim = Animation::New();
fadeInSpec.ApplyTo(anim, view);
anim.Play();
```

스펙 패턴은 여러 뷰에서 애니메이션 재사용을 가능하게 하고 애니메이션 정의와 적용의 분리를 촉진합니다.

### 지속 시간 리터럴

가독성 있는 지속 시간 값으로 `_ms`와 `_s` 리터럴을 사용합니다:

```cpp
using namespace Dali::Ui;

view.Animate(anim)
    .Opacity(1.0f, 500_ms)      // 500밀리초
    .PositionX(50.0f, 300_ms);  // 300밀리초
```
