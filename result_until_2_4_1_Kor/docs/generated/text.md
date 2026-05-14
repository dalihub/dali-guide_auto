---
title: 텍스트
sidebar_label: 텍스트
category: text
---

# 텍스트 {#text}

`Dali::Ui::View` 기반 dali-ui 인터페이스에서 `Dali::Ui::Text` 값 타입과 프로퍼티 인덱스를 사용해 텍스트 스타일, 맞춤, 글꼴 배리언트, 입력 동작을 설정합니다.

## 목차 {#table-of-contents}

- [텍스트 스타일 값](#text-styling-values)
- [텍스트 맞춤 설정](#text-fit-configuration)
- [가변 글꼴 설정](#variable-font-settings)
- [입력 필드 프로퍼티 인덱스](#input-field-property-indices)
- [입력 필터링](#input-filtering)

## 텍스트 스타일 값 {#text-styling-values}

`Dali::Ui::Text::Bevel`은 텍스트 스타일 값 타입입니다. 이 타입은 독립적으로 준비한 뒤 `Dali::Ui::Label`처럼 베벨 스타일을 지원하는 dali-ui 텍스트 뷰에 전달할 수 있는 명암 베벨 효과를 설명합니다.

타입이 지정된 setter로 `Dali::Ui::Text::Bevel`을 설정합니다. setter는 `Dali::Ui::Text::Bevel&`를 반환하므로, fluent 체이닝 방식으로 사용하는 것이 좋습니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::Text::Bevel CreateRaisedTextBevel()
{
  return Dali::Ui::Text::Bevel()
    .SetDirection(Dali::Vector2(-1.0f, -1.0f))
    .SetIntensity(2.0f)
    .SetLightColor(Dali::Ui::UiColor(0x808080))
    .SetShadowColor(Dali::Ui::UiColor(0x0D0D0D));
}
```

방향 벡터를 사용해 겉으로 보이는 빛의 방향을 선택합니다. 예를 들어 `Dali::Vector2(-1.0f, -1.0f)`는 일반적인 왼쪽 위 광원 방향으로 문서화되어 있으며, 공개 베벨 샘플에서도 같은 방식으로 사용됩니다. 빛 색상과 그림자 색상을 서로 바꾸면 음각처럼 들어가 보이는 변형을 만들 수 있습니다.

```cpp
Dali::Ui::Text::Bevel CreateSunkenTextBevel()
{
  return Dali::Ui::Text::Bevel()
    .SetDirection(Dali::Vector2(-1.0f, -1.0f))
    .SetIntensity(2.0f)
    .SetLightColor(Dali::Ui::UiColor(0x0D0D0D))
    .SetShadowColor(Dali::Ui::UiColor(0x808080));
}
```

`Dali::Ui::Text::Bevel`은 설정된 값을 다시 읽기 위한 getter도 제공합니다. `Dali::Ui::View` 기반 화면에서 스타일 프리셋을 애플리케이션 상태로 보관할 때 유용합니다.

```cpp
float ReadBevelIntensity(const Dali::Ui::Text::Bevel& bevel)
{
  return bevel.GetIntensity();
}

const Dali::Vector2& ReadBevelDirection(const Dali::Ui::Text::Bevel& bevel)
{
  return bevel.GetDirection();
}

const Dali::Ui::UiColor& ReadBevelLightColor(const Dali::Ui::Text::Bevel& bevel)
{
  return bevel.GetLightColor();
}

const Dali::Ui::UiColor& ReadBevelShadowColor(const Dali::Ui::Text::Bevel& bevel)
{
  return bevel.GetShadowColor();
}
```

## 텍스트 맞춤 설정 {#text-fit-configuration}

`Dali::Ui::Text::FitRange`와 `Dali::Ui::Text::FitCandidate`는 텍스트 맞춤 설정 값 타입입니다.

`Dali::Ui::Label::SetTextFit`이 연속적인 글꼴 크기 범위에서 검색해야 할 때는 `Dali::Ui::Text::FitRange`를 사용합니다. 이 범위는 최소 글꼴 크기, 최대 글꼴 크기, 글꼴 크기 단계를 모두 픽셀 단위로 저장합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::Text::FitRange CreateTitleFitRange()
{
  return Dali::Ui::Text::FitRange()
    .SetMinimumFontSize(18.0f)
    .SetMaximumFontSize(36.0f)
    .SetFontSizeStep(1.0f);
}
```

애플리케이션 로직에서 프리셋을 텍스트 뷰에 적용하기 전에 검사하거나 복사해야 할 때는 getter를 사용합니다.

```cpp
bool HasUsableFitRange(const Dali::Ui::Text::FitRange& range)
{
  return range.GetMinimumFontSize() <= range.GetMaximumFontSize() &&
         range.GetFontSizeStep() > 0.0f;
}
```

디자인에서 승인된 텍스트 크기가 명시적으로 정해져 있다면 `Dali::Ui::Text::FitCandidate`를 사용합니다. 각 후보는 글꼴 크기와 절대 줄 높이를 저장하며, 후보 기반 텍스트 맞춤은 사용 가능한 레이아웃 공간에 들어가는 후보 중 가장 큰 후보를 선택합니다.

```cpp
Dali::Ui::Text::FitCandidate CreateCompactCandidate()
{
  return Dali::Ui::Text::FitCandidate()
    .SetFontSize(16.0f)
    .SetLineHeight(20.0f);
}

Dali::Ui::Text::FitCandidate CreateComfortableCandidate()
{
  return Dali::Ui::Text::FitCandidate()
    .SetFontSize(20.0f)
    .SetLineHeight(26.0f);
}
```

`Dali::Ui::Text::FitCandidate::GetFontSize`와 `Dali::Ui::Text::FitCandidate::GetLineHeight`는 설정된 픽셀 값을 반환합니다.

```cpp
float CalculateCandidateLineExtra(const Dali::Ui::Text::FitCandidate& candidate)
{
  return candidate.GetLineHeight() - candidate.GetFontSize();
}
```

## 가변 글꼴 설정 {#variable-font-settings}

`Dali::Ui::Text::FontVariationAxis`는 하나의 OpenType 가변 글꼴 축을 나타냅니다. 이 타입은 `"wght"` 또는 `"wdth"` 같은 네 글자 축 태그와 숫자 값을 저장합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::Text::FontVariationAxis CreateWeightAxis()
{
  return Dali::Ui::Text::FontVariationAxis("wght", 700.0f);
}

Dali::Ui::Text::FontVariationAxis CreateWidthAxis()
{
  return Dali::Ui::Text::FontVariationAxis()
    .SetTag("wdth")
    .SetValue(90.0f);
}
```

축은 `Dali::Ui::Text::FontVariationAxis::GetTag`와 `Dali::Ui::Text::FontVariationAxis::GetValue`로 다시 읽을 수 있습니다.

```cpp
bool IsWeightAxis(const Dali::Ui::Text::FontVariationAxis& axis)
{
  return axis.GetTag() == "wght";
}

float ReadAxisValue(const Dali::Ui::Text::FontVariationAxis& axis)
{
  return axis.GetValue();
}
```

`Dali::Ui::Text::FontVariation`은 변환 헬퍼를 제공합니다. `Dali::Ui::Text::FontVariation::FromString`을 사용해 설정 문자열을 축 목록으로 파싱하고, `Dali::Ui::Text::FontVariation::ToString`을 사용해 축 목록을 일반적인 네 글자 태그에 쓰이는 표준 `tag=value` 문자열 형식으로 다시 직렬화합니다.

```cpp
Dali::Vector<Dali::Ui::Text::FontVariationAxis> ParseFontVariation()
{
  return Dali::Ui::Text::FontVariation::FromString("wght=700,wdth=90");
}

Dali::String BuildFontVariationString()
{
  Dali::Vector<Dali::Ui::Text::FontVariationAxis> axes;
  axes.PushBack(Dali::Ui::Text::FontVariationAxis("wght", 700.0f));
  axes.PushBack(Dali::Ui::Text::FontVariationAxis("wdth", 90.0f));

  return Dali::Ui::Text::FontVariation::ToString(axes);
}
```

## 입력 필드 프로퍼티 인덱스 {#input-field-property-indices}

`Dali::Ui::Text::InputFieldPropertyIndex`는 dali-ui 입력 필드 텍스트 프로퍼티의 프로퍼티 인덱스 상수를 정의합니다. 애플리케이션 코드에서는 사용할 수 있다면 타입이 지정된 `Dali::Ui::InputField` API를 우선 사용하세요. 프로퍼티 중심 계층과 연동하기 위해 프로퍼티 식별자가 필요할 때 이 프로퍼티 인덱스를 사용합니다.

다음 상수는 일반적인 글꼴 및 레이아웃 프로퍼티를 식별합니다.

- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_FAMILY`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_SIZE`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_SIZE_SCALE`
- `Dali::Ui::Text::InputFieldPropertyIndex::MAXIMUM_FONT_SIZE_SCALE`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_WEIGHT`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_WIDTH`
- `Dali::Ui::Text::InputFieldPropertyIndex::FONT_SLANT`
- `Dali::Ui::Text::InputFieldPropertyIndex::HORIZONTAL_ALIGNMENT`
- `Dali::Ui::Text::InputFieldPropertyIndex::LAYOUT_DIRECTION_MODE`
- `Dali::Ui::Text::InputFieldPropertyIndex::MARKUP_ENABLED`

```cpp
int FontSizePropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::FONT_SIZE;
}

int FontFamilyPropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::FONT_FAMILY;
}

int HorizontalAlignmentPropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::HORIZONTAL_ALIGNMENT;
}
```

커서 관련 상수는 편집 가능한 텍스트의 커서 설정을 식별합니다.

- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_BLINK_ENABLED`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_BLINK_INTERVAL`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_COLOR`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_WIDTH`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_POSITION`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_HANDLE_IMAGE`
- `Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_HANDLE_PRESSED_IMAGE`
- `Dali::Ui::Text::InputFieldPropertyIndex::EDITABLE`

```cpp
int CursorColorPropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_COLOR;
}

int CursorBlinkIntervalPropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::CURSOR_BLINK_INTERVAL;
}

int EditablePropertyIndex()
{
  return Dali::Ui::Text::InputFieldPropertyIndex::EDITABLE;
}
```

## 입력 필터링 {#input-filtering}

`Dali::Ui::Text::InputFilter`는 텍스트 입력 규칙 값 타입입니다. 이 타입은 삽입된 입력을 허용하거나 거부할 조건을 설명하는 `std::regex` ECMAScript 패턴을 저장합니다.

긍정 필터링에는 `Dali::Ui::Text::InputFilter::SetAllowPattern`을 사용합니다. 허용 패턴이 설정되어 있으면 해당 패턴과 일치하지 않는 입력은 거부됩니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

Dali::Ui::Text::InputFilter CreateDigitsOnlyFilter()
{
  return Dali::Ui::Text::InputFilter()
    .SetAllowPattern("[0-9]+");
}
```

필터를 `Dali::Ui::View` 기반 입력 화면의 상태로 보관한 뒤, 설정된 값을 `Dali::Ui::InputField::SetInputFilter`에 전달합니다. 필터는 사용자 입력, 입력기 커밋, 붙여넣기 작업처럼 삽입되는 입력에 적용되며, `Dali::Ui::InputField::SetText`로 텍스트를 직접 설정할 때는 적용되지 않습니다.

```cpp
class TextEntryState
{
public:
  TextEntryState()
  : mFilter(CreateDigitsOnlyFilter())
  {
  }

  const Dali::Ui::Text::InputFilter& GetFilter() const
  {
    return mFilter;
  }

private:
  Dali::Ui::Text::InputFilter mFilter;
};
```
