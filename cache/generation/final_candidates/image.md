---
title: Image
sidebar_label: Image
category: images-visuals
---

# Image

`Dali::Ui::View` 기반 dali-ui 앱에서 이미지 관련 뷰를 배치하고, 애니메이션 이미지 이벤트와 Lottie 이미지의 동적 속성 입력을 다룹니다.

## Table of Contents

- [dali-ui 이미지 뷰 모델](#dali-ui-이미지-뷰-모델)
- [애니메이션 이미지 뷰를 앱 구조에 보관하기](#애니메이션-이미지-뷰를-앱-구조에-보관하기)
- [이미지 영역을 레이아웃에 맞추기](#이미지-영역을-레이아웃에-맞추기)
- [이미지 이벤트 연결 수명 관리](#이미지-이벤트-연결-수명-관리)
- [Lottie 이미지의 동적 속성 정보 구성](#lottie-이미지의-동적-속성-정보-구성)

## dali-ui 이미지 뷰 모델

dali-ui 앱에서는 이미지도 화면 트리의 한 항목으로 취급합니다. 앱 코드는 raw actor 중심으로 작성하지 말고, app-facing handle을 `Dali::Ui::View` 계층으로 보관하는 방식이 자연스럽습니다. `Dali::Ui::AnimatedImageView`는 그 계층에서 애니메이션 이미지 리소스를 표시하는 이미지 전용 view입니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

Dali::Ui::AnimatedImageView KeepImageHandle(Dali::Ui::AnimatedImageView source)
{
  Dali::Ui::AnimatedImageView image(std::move(source));
  return image;
}
```

위 예시는 `Dali::Ui::AnimatedImageView::AnimatedImageView` move constructor를 사용해 이미지 view handle을 앱 내부 상태로 넘기는 형태입니다. 실제 화면 구성에서는 이 handle을 `Dali::Ui::View` 기반 트리에 배치하고, 이미지 전용 제어는 `Dali::Ui::AnimatedImageView` handle에서 수행합니다.

## 애니메이션 이미지 뷰를 앱 구조에 보관하기

이미지 화면을 담당하는 controller는 이미지 view handle과 신호 연결 수명을 함께 소유하는 편이 좋습니다. `Dali::ConnectionTracker`는 signal 연결을 controller 생명주기에 묶을 때 사용하는 기본 구성 요소입니다.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

class ImageScreen : public Dali::ConnectionTracker
{
public:
  ImageScreen()
  : Dali::ConnectionTracker()
  {
  }

  void SetImage(Dali::Ui::AnimatedImageView image)
  {
    mImage = Dali::Ui::AnimatedImageView(std::move(image));
  }

private:
  Dali::Ui::AnimatedImageView mImage;
};
```

`Dali::ConnectionTracker::ConnectionTracker`는 controller를 signal owner로 사용할 준비를 합니다. 이미지 뷰 자체는 `Dali::Ui::AnimatedImageView` handle로 보관하되, 앱의 나머지 화면 구성과 상호작용할 때는 `Dali::Ui::View` 기반 흐름 안에서 다루는 것이 dali-ui 앱 모델과 맞습니다.

## 이미지 영역을 레이아웃에 맞추기

이미지 콘텐츠는 보통 다른 controls와 함께 stack layout에 들어갑니다. 이때 per-child layout 정보는 `Dali::Ui::StackLayoutParams`로 복사하거나 재사용할 수 있습니다. 이미지 view를 직접 actor property로 조정하는 대신, view에 붙는 layout parameter를 별도 handle로 관리합니다.

```cpp
#include <dali-ui-foundation/public-api/layouts/stack-layout-params.h>

Dali::Ui::StackLayoutParams CopyImageLayoutParams(Dali::Ui::StackLayoutParams source)
{
  Dali::Ui::StackLayoutParams params(source);
  return params;
}
```

`Dali::Ui::StackLayoutParams::StackLayoutParams` copy constructor는 이미 구성된 per-child layout handle을 복사해 같은 이미지 화면 안의 다른 image slot에 적용할 때 유용합니다. 이렇게 하면 이미지 뷰의 리소스 제어와 레이아웃 제어를 분리해서 유지할 수 있습니다.

## 이미지 이벤트 연결 수명 관리

이미지 리소스 준비, 애니메이션 완료 같은 이벤트는 public signal을 통해 처리합니다. 앱 controller는 `Dali::ConnectionTracker`를 상속해 연결 수명을 controller와 함께 정리되도록 구성합니다.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

class AnimatedImageEvents : public Dali::ConnectionTracker
{
public:
  AnimatedImageEvents()
  : Dali::ConnectionTracker()
  {
  }

  void Track(Dali::Ui::AnimatedImageView image)
  {
    mImage = Dali::Ui::AnimatedImageView(std::move(image));
  }

private:
  Dali::Ui::AnimatedImageView mImage;
};
```

이 구조에서 `Dali::ConnectionTracker`는 signal connection owner 역할을 하고, `Dali::Ui::AnimatedImageView`는 이미지 view handle 역할을 합니다. signal 연결 API는 해당 signal을 소유한 view나 trait에서 호출해야 하며, 연결된 callback은 view 기반 이벤트 흐름 안에서 처리합니다.

## Lottie 이미지의 동적 속성 정보 구성

Lottie 기반 이미지에서는 특정 layer 또는 element path에 대해 per-frame 값을 공급해야 할 수 있습니다. 이때 `Dali::Ui::LottieAnimation::DynamicPropertyInfo`가 동적 속성 입력을 담는 owned data 구조입니다.

`Dali::Ui::LottieAnimation::DynamicPropertyInfo::id`는 callback으로 전달될 식별자이고, `Dali::Ui::LottieAnimation::DynamicPropertyInfo::keyPath`는 대상 layer 또는 element path입니다. `Dali::Ui::LottieAnimation::DynamicPropertyInfo::property`는 동적으로 갱신할 Lottie vector property를 지정하며, `Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback`은 frame마다 값을 계산하는 callback 포인터입니다.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>

Dali::Ui::LottieAnimation::DynamicPropertyInfo MakeLottieDynamicPropertyInfo()
{
  Dali::Ui::LottieAnimation::DynamicPropertyInfo info{};

  info.id       = 1001;
  info.keyPath  = "**";
  info.callback = nullptr;

  return info;
}
```

`Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback`의 ownership은 동적 속성 적용 후 visual 쪽으로 넘어가는 형태로 정의되어 있습니다. callback은 worker thread에서 호출될 수 있으므로, callback 내부에서 DALi object model을 직접 조작하는 방식은 피하고 계산 결과만 반환하도록 설계합니다.
