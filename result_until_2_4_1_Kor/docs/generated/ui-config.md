---
title: UI 구성
sidebar_label: UI 구성
category: styling-theme-config
---

# UI 구성 {#ui-config}

`Dali::Ui::UiConfig`는 애플리케이션이 `Dali::Ui::View` 트리를 구성하거나 UiConfig에 의존하는 기능을 사용하기 전에 dali-ui가 사용할 전역 기본값을 정의합니다.

## 목차 {#table-of-contents}

- [시작 시 구성 적용](#apply-configuration-at-startup)
- [스케일링 및 DPI 기본값](#scaling-and-dpi-defaults)
- [텍스트 기본값](#text-defaults)
- [키보드, 포커스 및 탭 기본값](#keyboard-focus-and-tap-defaults)
- [손상된 이미지 대체 리소스](#broken-image-fallbacks)
- [텍스트 마키 기본값](#marquee-defaults-for-text)
- [구성 체이닝 확장](#extending-configuration-chaining)

## 시작 시 구성 적용 {#apply-configuration-at-startup}

`Dali::Ui::UiConfig::New()`로 `Dali::Ui::UiConfig`를 만들고 애플리케이션에 필요한 기본값을 설정한 다음, `Dali::Ui::UiConfig::Apply()`를 정확히 한 번 호출합니다. `Apply()`는 구성을 고정하므로, setter 호출은 애플리케이션이 단위 리터럴이나 기본 `Dali::Ui::View` 계층처럼 UiConfig에 의존하는 기능을 사용하기 전의 시작 코드에 두어야 합니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureUi()
{
  Dali::Ui::UiConfig::New()
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.25f)
    .SetDefaultFontSize(18.0f)
    .Apply();
}
```

플루언트 체이닝을 위해 각 타입별 setter가 반환하는 값을 사용합니다. 적용하기 전에 값을 확인해야 한다면 `Dali::Ui::UiConfig` 핸들을 보관하고 그에 대응하는 getter를 호출합니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureAndInspect()
{
  Dali::Ui::UiConfig config = Dali::Ui::UiConfig::New();

  config
    .SetDpi(240)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.0f);

  const int dpi = config.GetDpi();
  const int baselineDpi = config.GetBaselineDpi();
  const float scalingFactor = config.GetScalingFactor();

  (void)dpi;
  (void)baselineDpi;
  (void)scalingFactor;

  config.Apply();
}
```

## 스케일링 및 DPI 기본값 {#scaling-and-dpi-defaults}

`Dali::Ui::UiConfig::SetDpi()`는 대상 디스플레이 DPI를 설정하고, `Dali::Ui::UiConfig::SetBaselineDpi()`는 기준 DPI를 설정하며, `Dali::Ui::UiConfig::SetScalingFactor()`는 `spx` 및 `sdp` 단위에 사용되는 추가 스케일을 설정합니다. `Dali::Ui::UiConfig::Apply()` 이후에는 `Dali::Ui::UiConfig::GetDpiFactor()`가 `dpi / baselineDpi`를 반환하고, `Dali::Ui::UiConfig::GetScaledDpiFactor()`가 해당 DPI 계수에 스케일링 계수를 곱한 값을 반환합니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureDisplayScale()
{
  Dali::Ui::UiConfig config = Dali::Ui::UiConfig::New();

  config
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.5f)
    .Apply();

  const float dpiFactor = config.GetDpiFactor();
  const float scaledDpiFactor = config.GetScaledDpiFactor();

  (void)dpiFactor;
  (void)scaledDpiFactor;
}
```

`Dali::Ui::UiConfig::New()`가 제공하는 기본값은 `scalingFactor = 1.0f`, `dpi = 160`, `baselineDpi = 160`입니다.

## 텍스트 기본값 {#text-defaults}

`Dali::Ui::UiConfig::SetDefaultFontSize()`, `Dali::Ui::UiConfig::SetDefaultTextColor()`, `Dali::Ui::UiConfig::SetDefaultPlaceholderTextColor()`를 사용하여 dali-ui 텍스트 컨트롤의 애플리케이션 전체 텍스트 기본값을 설정합니다. `Dali::Ui::UiConfig::SetShowPlaceholderTextOnFocus()`는 컨트롤에 포커스가 있을 때 플레이스홀더 텍스트를 표시할지 여부를 제어합니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureTextDefaults()
{
  const Dali::Vector4 primaryText(0.05f, 0.05f, 0.05f, 1.0f);
  const Dali::Vector4 placeholderText(0.45f, 0.45f, 0.45f, 0.75f);

  Dali::Ui::UiConfig::New()
    .SetDefaultFontSize(16.0f)
    .SetDefaultTextColor(primaryText)
    .SetDefaultPlaceholderTextColor(placeholderText)
    .SetShowPlaceholderTextOnFocus(false)
    .SetLabelAsyncRendering(true)
    .Apply();
}
```

`Dali::Ui::UiConfig::SetLabelAsyncRendering()`은 `Dali::Ui::Label`이 기본적으로 비동기 텍스트 렌더링을 사용할지 여부를 설정합니다. 현재 텍스트 관련 기본값은 `Dali::Ui::UiConfig::GetDefaultFontSize()`, `Dali::Ui::UiConfig::GetDefaultTextColor()`, `Dali::Ui::UiConfig::GetDefaultPlaceholderTextColor()`, `Dali::Ui::UiConfig::IsPlaceholderTextShownOnFocus()`, `Dali::Ui::UiConfig::IsLabelAsyncRendering()`으로 조회합니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ReadTextDefaults(Dali::Ui::UiConfig config)
{
  const float fontSize = config.GetDefaultFontSize();
  const Dali::Vector4 textColor = config.GetDefaultTextColor();
  const Dali::Vector4 placeholderColor = config.GetDefaultPlaceholderTextColor();
  const bool showPlaceholderOnFocus = config.IsPlaceholderTextShownOnFocus();
  const bool asyncLabelRendering = config.IsLabelAsyncRendering();

  (void)fontSize;
  (void)textColor;
  (void)placeholderColor;
  (void)showPlaceholderOnFocus;
  (void)asyncLabelRendering;
}
```

## 키보드, 포커스 및 탭 기본값 {#keyboard-focus-and-tap-defaults}

`Dali::Ui::UiConfig`는 키 기반 클릭 실행, 키 길게 누름 인식, 포커스 표시, Escape 키를 통한 포커스 해제, 탭 타이밍의 기본값을 설정할 수 있습니다. 이러한 기본값은 `Dali::Ui::View` 트리의 상호작용 가능한 dali-ui 컨트롤에서 사용됩니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

bool AcceptActivationKey(const Dali::String& keyName)
{
  return keyName == "Return" || keyName == "KP_Enter";
}

void ConfigureInputDefaults()
{
  Dali::Ui::UiConfig::New()
    .SetKeyClickPolicy(Dali::Ui::KeyClickPolicy::ON_RELEASE)
    .SetExecutionKeyPredicate(AcceptActivationKey)
    .SetKeyLongPressThreshold(3u)
    .SetTapRecognizerTime(300u)
    .EnableFocusClearOnEscape(true)
    .SetAlwaysShowFocus(true)
    .Apply();
}
```

`Dali::Ui::ExecutionKeyPredicate`는 `bool(const Dali::String&)` 시그니처를 갖는 일반 함수 포인터입니다. 해당 포인터 타입으로 변환할 수 있는 자유 함수나 상태 없는 람다를 사용합니다. `Dali::Ui::UiConfig::SetKeyLongPressThreshold()`는 길게 누름을 인식하는 데 필요한 연속 키 누름 이벤트 수를 받고, `Dali::Ui::UiConfig::SetTapRecognizerTime()`은 밀리초 단위의 시간 제한을 받습니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ReadInputDefaults(Dali::Ui::UiConfig config)
{
  const Dali::Ui::KeyClickPolicy keyClickPolicy = config.GetKeyClickPolicy();
  const Dali::Ui::ExecutionKeyPredicate predicate = config.GetExecutionKeyPredicate();
  const uint32_t longPressThreshold = config.GetKeyLongPressThreshold();
  const uint32_t tapTime = config.GetTapRecognizerTime();
  const bool clearFocusOnEscape = config.IsFocusClearOnEscapeEnabled();
  const bool alwaysShowFocus = config.IsFocusIndicatorAlwaysShown();

  (void)keyClickPolicy;
  (void)predicate;
  (void)longPressThreshold;
  (void)tapTime;
  (void)clearFocusOnEscape;
  (void)alwaysShowFocus;
}
```

## 손상된 이미지 대체 리소스 {#broken-image-fallbacks}

`Dali::Ui::UiConfig::SetBrokenImageUrl()`을 사용하여 이미지 뷰에서 이미지 로드에 실패했을 때 사용할 대체 이미지를 제공합니다. `Dali::Ui::UiConfig::BrokenImageType`은 `SMALL`, `NORMAL`, `LARGE`에 대해 별도의 슬롯을 제공하므로, 애플리케이션은 서로 다른 뷰 크기에 맞는 대체 에셋을 선택할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureBrokenImages()
{
  Dali::Ui::UiConfig::New()
    .SetBrokenImageUrl(
      Dali::Ui::UiConfig::BrokenImageType::SMALL,
      Dali::String("/opt/app/res/images/broken-small.png"))
    .SetBrokenImageUrl(
      Dali::Ui::UiConfig::BrokenImageType::NORMAL,
      Dali::String("/opt/app/res/images/broken-normal.png"))
    .SetBrokenImageUrl(
      Dali::Ui::UiConfig::BrokenImageType::LARGE,
      Dali::String("/opt/app/res/images/broken-large.png"))
    .Apply();
}
```

구성된 URL은 `Dali::Ui::UiConfig::GetBrokenImageUrl()`로 다시 읽을 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ReadBrokenImageUrl(Dali::Ui::UiConfig config)
{
  const Dali::String& normalBrokenImage =
    config.GetBrokenImageUrl(Dali::Ui::UiConfig::BrokenImageType::NORMAL);

  (void)normalBrokenImage;
}
```

## 텍스트 마키 기본값 {#marquee-defaults-for-text}

`Dali::Ui::UiConfig`는 텍스트의 기본 마키 동작도 제공합니다. `Dali::Ui::UiConfig::SetMarqueeSpeed()`, `Dali::Ui::UiConfig::SetMarqueeLoopCount()`, `Dali::Ui::UiConfig::SetMarqueeLoopDelay()`, `Dali::Ui::UiConfig::SetMarqueeGap()`을 사용하여 타이밍과 간격을 정의합니다. `Dali::Ui::UiConfig::SetMarqueeStopMode()` 및 `Dali::Ui::UiConfig::SetMarqueeOrientation()`은 `Dali::Ui::Text` 마키 열거형과 함께 사용합니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureMarqueeDefaults()
{
  Dali::Ui::UiConfig::New()
    .SetMarqueeSpeed(90)
    .SetMarqueeLoopCount(1)
    .SetMarqueeLoopDelay(0.5f)
    .SetMarqueeGap(48.0f)
    .SetMarqueeStopMode(Dali::Ui::Text::MarqueeStopMode::IMMEDIATE)
    .SetMarqueeOrientation(Dali::Ui::Text::MarqueeOrientation::HORIZONTAL)
    .Apply();
}
```

대응하는 getter를 사용하면 텍스트 컨트롤에서 사용하는 구성된 기본값을 읽을 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ReadMarqueeDefaults(Dali::Ui::UiConfig config)
{
  const int speed = config.GetMarqueeSpeed();
  const int loopCount = config.GetMarqueeLoopCount();
  const float loopDelay = config.GetMarqueeLoopDelay();
  const float gap = config.GetMarqueeGap();
  const Dali::Ui::Text::MarqueeStopMode stopMode = config.GetMarqueeStopMode();
  const Dali::Ui::Text::MarqueeOrientation orientation = config.GetMarqueeOrientation();

  (void)speed;
  (void)loopCount;
  (void)loopDelay;
  (void)gap;
  (void)stopMode;
  (void)orientation;
}
```

## 구성 체이닝 확장 {#extending-configuration-chaining}

`ui-config.autogen.h`는 공개 dali-ui 구성 API의 일부입니다. 이 파일은 `DALI_UI_CHAIN_UICONFIG_METHODS`를 제공합니다. 이 매크로는 `Dali::Ui::UiConfig`에서 파생되고, 체이닝용으로 생성된 `Dali::Ui::UiConfig` setter가 플루언트 체이닝을 위해 파생 타입을 반환하기를 원하는 구성 타입에서 사용됩니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

class ProductUiConfig : public Dali::Ui::UiConfig
{
public:
  DALI_UI_CHAIN_UICONFIG_METHODS(ProductUiConfig)

  static ProductUiConfig New();
};
```

이후 파생 구성은 동일한 `Dali::Ui::UiConfig::Apply()` 흐름을 유지하면서 제품별 설정을 노출할 수 있습니다.

```cpp
#include <dali-ui-foundation/public-api/ui-config.h>

void ConfigureProductUi(ProductUiConfig config)
{
  config
    .SetDpi(320)
    .SetBaselineDpi(160)
    .SetScalingFactor(1.25f)
    .SetKeyLongPressThreshold(4u)
    .Apply();
}
```
