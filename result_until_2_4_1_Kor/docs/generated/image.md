---
title: 이미지
sidebar_label: 이미지
category: images-visuals
---

# 이미지 {#image}

애플리케이션에 표시되는 이미지 콘텐츠에는 dali-ui 이미지 뷰를 사용하고, 이미지 관련 이벤트, 로딩 동작, 레이아웃, 애니메이션 사용자 정의는 `Dali::Ui::View` 계층에서 처리하십시오.

## 목차 {#table-of-contents}

- [dali-ui의 이미지 뷰](#image-views-in-dali-ui)
- [이미지 코드를 뷰 계층에 유지하기](#keeping-image-code-in-the-view-layer)
- [Lottie 동적 이미지 속성](#lottie-dynamic-image-properties)
- [이미지 이벤트의 연결 수명](#connection-lifetime-for-image-events)

## dali-ui의 이미지 뷰 {#image-views-in-dali-ui}

dali-ui 앱에서 이미지 UI는 `Dali::Ui::View` 콘텐츠로 모델링해야 합니다. `Dali::Ui::AnimatedImageView`는 GIF 파일과 같은 애니메이션 이미지 리소스를 애플리케이션에서 다루기 위한 뷰 타입입니다. 원시 액터 스타일의 이미지 처리로 내려가지 말고, 뷰 트리의 일부로 다루십시오.

`Dali::Ui::AnimatedImageView`는 리소스 URL, 재생 제어, 반복 횟수, 맞춤 방식, 로딩, 마스킹, 리소스 이벤트를 위한 타입화된 API를 제공하는 `Dali::Ui::View` 하위 클래스입니다. UI 구성에 배치한 이미지 뷰 객체에서 이러한 공개 setter와 signal을 사용하십시오.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

class ImagePanel
{
public:
  void StoreImageView(Ui::AnimatedImageView imageView)
  {
    mImageView = imageView;
  }

  Ui::AnimatedImageView GetImageView() const
  {
    return mImageView;
  }

private:
  Ui::AnimatedImageView mImageView;
};
```

`Dali::Ui::AnimatedImageView`는 `Dali::Ui::View` 기반 타입이므로, UI 계층에서 뷰 객체를 받는 API를 통해 전달하십시오. 이렇게 하면 이미지 콘텐츠가 dali-ui 애플리케이션 모델의 다른 부분과 일관되게 유지됩니다.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

struct ImageSlot
{
  Ui::AnimatedImageView image;
};

void ReplaceImageSlot(ImageSlot& slot, Ui::AnimatedImageView nextImage)
{
  slot.image = nextImage;
}
```

## 이미지 코드를 뷰 계층에 유지하기 {#keeping-image-code-in-the-view-layer}

애플리케이션 설정과 수명 주기 소유권에는 `Dali::Application`을 사용하십시오. 이미지 뷰 코드는 애플리케이션이 직접 다루는 뷰 중심 코드로 유지해야 합니다. `Dali::Accessibility::Application`은 접근성 인프라이며, 이미지 UI 모델을 바꾸지 않습니다. 이미지 UI는 여전히 dali-ui 뷰로 표현해야 합니다.

```cpp
#include <dali/public-api/adaptor-framework/application.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

class ImageApplicationState
{
public:
  explicit ImageApplicationState(Dali::Application application)
  : mApplication(std::move(application))
  {
  }

  void SetPrimaryImage(Ui::AnimatedImageView view)
  {
    mPrimaryImage = view;
  }

private:
  Dali::Application    mApplication;
  Ui::AnimatedImageView mPrimaryImage;
};
```

레이아웃별 설정이 필요할 때 `Dali::Ui::StackLayoutParams`는 `Dali::Ui::StackLayout`의 자식별 레이아웃 매개변수를 저장합니다. 이미지 소유권, 이벤트 처리, 레이아웃을 쉽게 이해할 수 있도록 이러한 레이아웃 데이터는 이미지 동작과 분리해서 유지하십시오.

```cpp
#include <dali-ui-foundation/public-api/animated-image-view.h>
#include <dali-ui-foundation/public-api/layouts/stack-layout-params.h>

namespace Ui = Dali::Ui;

struct ImageLayoutBinding
{
  Ui::AnimatedImageView image;
  Ui::StackLayoutParams layout;
};

ImageLayoutBinding MakeImageLayoutBinding(
  Ui::AnimatedImageView image,
  Ui::StackLayoutParams layout)
{
  return ImageLayoutBinding{image, layout};
}
```

## Lottie 동적 이미지 속성 {#lottie-dynamic-image-properties}

`Dali::Ui::LottieAnimation::DynamicPropertyInfo`는 Lottie 애니메이션 콘텐츠의 프레임별 동적 속성 대상을 설명합니다. 이 구조체의 필드는 대상 경로, 애니메이션할 벡터 속성, `SetDynamicProperty()`에서 사용하는 callback을 정의합니다.

callback에 전달할 식별자로는 `Dali::Ui::LottieAnimation::DynamicPropertyInfo::id`를 사용하십시오. Lottie 레이어 또는 요소 경로를 지정할 때는 `Dali::Ui::LottieAnimation::DynamicPropertyInfo::keyPath`를 사용하십시오. 애니메이션할 벡터 속성 선택에는 `Dali::Ui::LottieAnimation::DynamicPropertyInfo::property`를, 프레임별 값 제공자에는 `Dali::Ui::LottieAnimation::DynamicPropertyInfo::callback`을 사용하십시오.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>

namespace Ui = Dali::Ui;

void SetDynamicTarget(
  Ui::LottieAnimation::DynamicPropertyInfo& info,
  int targetId,
  const char* keyPath)
{
  info.id      = targetId;
  info.keyPath = keyPath;
}
```

`callback` 멤버는 작업자 스레드에서 호출되며, `SetDynamicProperty()`가 호출된 뒤 해당 callback의 소유권은 visual로 이전됩니다. callback 안에서 DALi API를 호출하지 마십시오. 대신 애플리케이션 모델이 소유한 데이터에서 값을 준비하십시오.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>

namespace Ui = Dali::Ui;

class LottieImageDynamicTarget
{
public:
  void ConfigureTarget(Ui::LottieAnimation::DynamicPropertyInfo& info)
  {
    info.id      = 1001;
    info.keyPath = "**";
  }

  int GetTargetId(const Ui::LottieAnimation::DynamicPropertyInfo& info) const
  {
    return info.id;
  }

  const Dali::String& GetTargetPath(const Ui::LottieAnimation::DynamicPropertyInfo& info) const
  {
    return info.keyPath;
  }
};
```

재사용 가능한 Lottie 애니메이션 코드에서는 visual effect를 소유한 기능 가까이에 `DynamicPropertyInfo`를 저장하십시오. 이렇게 하면 `id`, `keyPath`, `property`, `callback`을 함께 유지할 수 있고, 대상 정의가 서로 관련 없는 UI 코드로 나뉘는 일을 피할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/image/lottie-animation-types.h>

namespace Ui = Dali::Ui;

struct LottieImageEffect
{
  Ui::LottieAnimation::DynamicPropertyInfo dynamicProperty;
};

void AssignDynamicPropertyId(LottieImageEffect& effect, int id)
{
  effect.dynamicProperty.id = id;
}
```

## 이미지 이벤트의 연결 수명 {#connection-lifetime-for-image-events}

이미지 뷰는 사용자 이벤트나 리소스 이벤트에 자주 관여합니다. 공개 signal에 연결하는 객체에는 `Dali::ConnectionTracker`를 소유 기반 클래스로 사용하십시오. 그러면 연결이 컨트롤러 객체의 수명에 묶입니다.

`Dali::ConnectionTracker::ConnectionTracker`는 추적 기반을 생성합니다. 이미지 컨트롤러를 `Dali::ConnectionTracker`에서 파생한 다음, `Dali::Ui::AnimatedImageView::ResourceReadySignal()`, `Dali::Ui::AnimatedImageView::AnimationFinishedSignal()`, `Dali::Ui::InteractiveTrait::ClickedSignal()` 같은 소유 뷰 또는 trait API의 signal에 연결하십시오.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

class ImageEventController : public Dali::ConnectionTracker
{
public:
  ImageEventController()
  : Dali::ConnectionTracker()
  {
  }

  void SetImageView(Ui::AnimatedImageView imageView)
  {
    mImageView = imageView;
  }

private:
  Ui::AnimatedImageView mImageView;
};
```

signal 연결은 이미지 동작을 소유한 객체 범위 안에 두십시오. 이는 dali-ui 뷰 트리 업데이트의 일부로 이미지 뷰가 다시 만들어지거나, 교체되거나, 제거될 때 특히 유용합니다.

```cpp
#include <dali/public-api/signals/connection-tracker.h>
#include <dali-ui-foundation/public-api/animated-image-view.h>

namespace Ui = Dali::Ui;

class ImageScreenController : public Dali::ConnectionTracker
{
public:
  ImageScreenController()
  : Dali::ConnectionTracker()
  {
  }

  void BindImage(Ui::AnimatedImageView image)
  {
    mImage = image;
  }

  Ui::AnimatedImageView GetBoundImage() const
  {
    return mImage;
  }

private:
  Ui::AnimatedImageView mImage;
};
```
