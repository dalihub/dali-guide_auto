---
title: 이미지 로더
sidebar_label: 이미지 로더
category: images-visuals
---
# 이미지 로더

이미지 로더 기능은 dali-ui 애플리케이션에 비동기 이미지 로딩 기능을 제공합니다. 메인 이벤트 루프를 차단하지 않고 백그라운드 스레드에서 URL로부터 이미지를 로드할 수 있으며, 다양한 이미지 소스에서 URL을 생성하는 유틸리티를 제공합니다.

## 목차

- [비동기 이미지 로딩](#비동기-이미지-로딩)
- [크기 및 샘플링으로 이미지 로드](#크기-및-샘플링으로-이미지-로드)
- [로드 작업 취소](#로드-작업-취소)
- [이미지 URL 생성](#이미지-url-생성)
- [인코딩된 버퍼로 ImageUrl 사용](#인코딩된-버퍼로-imageurl-사용)

## 비동기 이미지 로딩

`Dali::Ui::AsyncImageLoader` 클래스는 URL로부터 논블로킹 이미지 로딩을 가능하게 합니다. 이미지는 워커 스레드에서 로드되고 결과는 시그널 콜백을 통해 전달됩니다. 각 로드 작업은 작업을 추적하거나 취소하는 데 사용할 수 있는 작업 ID를 반환합니다.

정적 `New()` 메서드로 `AsyncImageLoader` 인스턴스를 생성합니다:

```cpp
Dali::Ui::AsyncImageLoader loader = Dali::Ui::AsyncImageLoader::New();
```

로드 작업을 시작하기 전에 로딩 완료 시 콜백을 받기 위해 `ImageLoadedSignal()`에 연결합니다:

```cpp
class ImageHandler : public Dali::ConnectionTracker
{
public:
  void OnImageLoaded(uint32_t taskId, Dali::PixelData pixelData)
  {
    if (pixelData)
    {
      // 로드된 픽셀 데이터 처리
    }
  }
};

ImageHandler handler;
loader.ImageLoadedSignal().Connect(&handler, &ImageHandler::OnImageLoaded);
```

URL과 함께 `Load()`를 호출하여 로드 작업을 시작합니다. 이 메서드는 추적용 작업 ID를 반환합니다:

```cpp
uint32_t taskId = loader.Load("path/to/image.png");
```

시그널 콜백은 작업 ID와 결과 `PixelData` 객체를 모두 받습니다. 로드 실패를 감지하려면 `PixelData`의 유효성을 확인하세요.

## 크기 및 샘플링으로 이미지 로드

`AsyncImageLoader`는 대상 크기와 샘플링 모드를 제어하는 오버로드된 `Load()` 메서드를 제공합니다.

특정 크기로 이미지를 로드하려면:

```cpp
Dali::ImageDimensions dimensions(256, 256);
uint32_t taskId = loader.Load("path/to/image.png", dimensions);
```

샘플링과 방향을 더 세밀하게 제어하려면:

```cpp
Dali::ImageDimensions dimensions(512, 512);
Dali::SamplingMode::Type samplingMode = Dali::SamplingMode::BOX_THEN_LINEAR;
bool orientationCorrection = true;
uint32_t taskId = loader.Load("path/to/image.png", dimensions, samplingMode, orientationCorrection);
```

`dimensions` 매개변수는 로드된 이미지를 맞출 너비와 높이를 지정합니다. `samplingMode` 매개변수는 크기 조정 중 픽셀 샘플링에 사용되는 필터링 방법을 제어합니다. `orientationCorrection`을 `true`로 설정하면 이미지 헤더의 방향 메타데이터에 따라 이미지를 재방향화합니다.

## 로드 작업 취소

`Cancel()`을 사용하여 아직 대기열에 있는 특정 로딩 작업을 중지합니다:

```cpp
bool canceled = loader.Cancel(taskId);
```

`Cancel()`은 작업이 대기열에서 성공적으로 제거되면 `true`를 반환합니다. 작업이 이미 진행 중이거나 완료된 경우 `false`를 반환합니다.

모든 대기 중인 로드 작업을 취소하려면:

```cpp
loader.CancelAll();
```

## 이미지 URL 생성

`Dali::Ui::ImageUrl` 클래스는 외부 이미지 버퍼를 감싸고 비주얼에서 사용할 수 있는 URL을 생성합니다. `ImageUrlUtils::GenerateUrl()`을 사용하여 다양한 이미지 소스에서 `ImageUrl` 인스턴스를 생성합니다.

`PixelData`에서 URL 생성:

```cpp
Dali::PixelData pixelData = /* ... */;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(pixelData, false);
const Dali::String& url = imageUrl.GetUrl();
```

`FrameBuffer`에서 URL 생성:

```cpp
Dali::FrameBuffer frameBuffer = /* ... */;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(frameBuffer, Pixel::Format::RGBA8888, 512, 512);
```

`NativeImageInterface`에서 URL 생성:

```cpp
Dali::NativeImageInterfacePtr nativeImage = /* ... */;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(nativeImage, false);
```

`preMultiplied` 매개변수는 이미지 데이터가 프리멀티플라이드 알파를 가지고 있는지 여부를 나타냅니다.

## 인코딩된 버퍼로 ImageUrl 사용

`ImageUrl`은 `EncodedImageBuffer`에서 직접 생성할 수 있습니다:

```cpp
Dali::EncodedImageBuffer encodedBuffer = /* ... */;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(encodedBuffer);
const Dali::String& url = imageUrl.GetUrl();
```

또는 동일한 목적으로 `ImageUrlUtils::GenerateUrl()`을 사용합니다:

```cpp
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrlUtils::GenerateUrl(encodedBuffer);
```

`ImageUrl`은 `Texture`에서도 생성할 수 있습니다:

```cpp
Dali::Texture texture = /* ... */;
Dali::Ui::ImageUrl imageUrl = Dali::Ui::ImageUrl::New(texture, false);
```

생성된 URL은 렌더링을 위해 비주얼에 전달할 수 있습니다. `ImageUrl` 객체가 파괴되면 다른 참조가 없는 경우 연관된 버퍼가 텍스처 매니저에서 제거됩니다.
