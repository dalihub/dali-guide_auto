---
title: Ui 컴포넌트 설정
sidebar_label: Ui 컴포넌트 설정
category: views-components
---

# Ui 컴포넌트 설정

`UiComponentConfig`는 dali-ui-components 기본값에 대한 구성을 제공합니다. `UiConfig`를 확장하며 dali-ui-components 라이브러리를 사용하는 애플리케이션의 기본 구성 객체 역할을 합니다.

## 목차

- [구성 생성 및 적용](#구성-생성-및-적용)
- [상속된 구성 메서드](#상속된-구성-메서드)

## 구성 생성 및 적용

`UiComponentConfig::New()`로 `UiComponentConfig` 인스턴스를 생성하고 메인 루프 진입 전 애플리케이션 시작 시 적용합니다. 구성은 정확히 한 번 적용되어야 합니다. `Apply()`가 호출된 후 구성은 고정되고 setter 메서드를 더 이상 사용할 수 없습니다.

```cpp
#include <dali-ui-components/public-api/ui-component-config.h>
#include <dali/public-api/application/application.h>

int main(int argc, char** argv)
{
  Dali::Application application = Dali::Application::New(&argc, &argv);

  Dali::Ui::UiComponentConfig::New()
    .SetScalingFactor(1.5f)
    .SetDpi(320)
    .SetDefaultFontSize(24.0f)
    .Apply();

  // 애플리케이션 메인 루프
  application.MainLoop();
  return 0;
}
```

모든 setter 메서드는 객체에 대한 참조를 반환하여 위와 같은 플루언트 메서드 체이닝을 가능하게 합니다.

## 상속된 구성 메서드

`UiComponentConfig`는 `UiConfig`의 모든 구성 메서드를 상속합니다. `Apply()` 호출 전에 다음 주요 setter를 사용할 수 있습니다:

| 메서드 | 설명 |
|---|---|
| `SetScalingFactor(float)` | spx 및 sdp 단위에 적용되는 스케일링 팩터 설정 |
| `SetDpi(int)` | dp 및 sdp 단위 계산을 위한 화면 DPI 설정 |
| `SetBaselineDpi(int)` | dp 계산의 참조로 사용되는 기준 DPI 설정 |
| `SetDefaultFontSize(float)` | 텍스트 요소의 기본 폰트 크기 설정 |
| `SetDefaultTextColor(const Vector4&)` | 텍스트 요소의 기본 텍스트 색상 설정 |
| `EnableFocusClearOnEscape(bool)` | Escape 키로 포커스 해제 활성화/비활성화 |

여러 옵션 구성 예:

```cpp
Dali::Ui::UiComponentConfig::New()
  .SetDpi(320)
  .SetBaselineDpi(160)
  .SetScalingFactor(1.0f)
  .SetDefaultFontSize(16.0f)
  .SetDefaultTextColor(Dali::Color::BLACK)
  .EnableFocusClearOnEscape(true)
  .Apply();
```
