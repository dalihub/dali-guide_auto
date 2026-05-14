---
title: 레이블
sidebar_label: 레이블
category: views-components
---

# 레이블 {#label}

`Dali::Ui::Label`은 애플리케이션 UI에서 편집할 수 없는 텍스트를 표시하는 dali-ui 뷰입니다.

## 목차 {#table-of-contents}

- [레이블 생성](#create-a-label)
- [타이포그래피 구성](#configure-typography)
- [텍스트 정렬 및 배치](#align-and-lay-out-text)
- [UI 밀도에 맞게 텍스트 스케일 조정](#scale-text-for-ui-density)
- [마크업 텍스트에서 앵커 사용](#use-anchors-in-markup-text)
- [비동기 렌더링](#render-asynchronously)
- [레이블 속성 사용](#use-label-properties)

## 레이블 생성 {#create-a-label}

`Dali::Ui::Label::New`로 텍스트를 생성합니다. dali-ui 애플리케이션 코드에서는 레이블을 뷰 트리의 `Dali::Ui::View`로 다루되, 텍스트 전용 API가 필요할 때 사용할 수 있도록 구체 타입인 `Dali::Ui::Label` 핸들을 유지합니다.

```cpp
#include <dali-ui-foundation/public-api/label.h>

Dali::Ui::Label CreateTitleLabel()
{
  return Dali::Ui::Label::New("Account settings")
    .SetFontFamily("SamsungOneUI")
    .SetFontSize(24.0f)
    .SetFontWeight(Dali::Ui::Text::FontWeight::BOLD);
}

Dali::Ui::View CreateHeaderView()
{
  Dali::Ui::Label title = CreateTitleLabel();
  return title;
}
```

`Dali::Ui::Label`은 편집할 수 없습니다. 제목, 캡션, 상태 텍스트, 보조 텍스트 및 기타 표시 전용 문자열에 사용합니다.

## 타이포그래피 구성 {#configure-typography}

글꼴 선택과 스타일에는 `Dali::Ui::Label`의 타입 지정 setter를 사용합니다. `SetFontFamily`, `SetFontSize`, `SetFontWeight`, `SetFontWidth`, `SetFontSlant`는 모두 `Dali::Ui::Label&`를 반환하므로, 체이닝 방식의 설정 코드에 적합합니다.

```cpp
Dali::Ui::Label CreatePriceLabel()
{
  return Dali::Ui::Label::New("$42.00")
    .SetFontFamily("SamsungOneUI")
    .SetFontSize(28.0f)
    .SetFontWeight(Dali::Ui::Text::FontWeight::SEMI_BOLD)
    .SetFontWidth(Dali::Ui::Text::FontWidth::NORMAL)
    .SetFontSlant(Dali::Ui::Text::FontSlant::NORMAL);
}
```

가변 글꼴에는 설정 문자열과 함께 `SetFontVariation`을 사용하거나, 코드에서 이미 축 값을 파싱해 둔 경우 `Dali::Vector<Dali::Ui::Text::FontVariationAxis>`를 전달합니다. 설정 문자열은 `wght=700,wdth=90`처럼 축 태그와 숫자 값을 사용합니다.

```cpp
Dali::Ui::Label CreateVariableFontLabel()
{
  return Dali::Ui::Label::New("Variable font")
    .SetFontFamily("SamsungOneUI")
    .SetFontSize(22.0f)
    .SetFontVariation("wght=700,wdth=90");
}
```

현재 텍스트 구성은 `GetFontFamily`, `GetFontSize`, `GetFontWeight`, `GetFontWidth`, `GetFontSlant`, `GetFontVariation` 같은 getter로 다시 읽을 수 있습니다.

```cpp
void InspectTypography(const Dali::Ui::Label& label)
{
  Dali::String family = label.GetFontFamily();
  float size = label.GetFontSize();
  Dali::Ui::Text::FontWeight weight = label.GetFontWeight();
  Dali::Ui::Text::FontWidth width = label.GetFontWidth();
  Dali::Ui::Text::FontSlant slant = label.GetFontSlant();
}
```

## 텍스트 정렬 및 배치 {#align-and-lay-out-text}

레이블 뷰 안에 텍스트를 배치하려면 `SetHorizontalTextAlignment`와 `SetVerticalTextAlignment`를 사용합니다. `Dali::Ui::Text::Alignment::START`, `Dali::Ui::Text::Alignment::CENTER`, `Dali::Ui::Text::Alignment::END`는 두 축 모두에 사용됩니다.

```cpp
Dali::Ui::Label CreateCenteredMessage()
{
  return Dali::Ui::Label::New("No results")
    .SetFontSize(18.0f)
    .SetHorizontalTextAlignment(Dali::Ui::Text::Alignment::CENTER)
    .SetVerticalTextAlignment(Dali::Ui::Text::Alignment::CENTER);
}
```

제약이 있는 레이아웃에서 레이블이 여러 시각적 줄을 차지할 수 있다면 `SetMultiLine(true)`로 여러 줄 레이아웃을 활성화하고, `SetLineWrapMode`로 줄바꿈 전략을 선택합니다. 줄 간격을 제어하려면 `SetLineHeight`를 사용합니다. `Dali::Ui::Text::LineHeightMode::RELATIVE`에서는 값이 실제 스케일이 적용된 글꼴 픽셀 크기의 배수로 사용되며, `Dali::Ui::Text::LineHeightMode::ABSOLUTE`에서는 값이 절대 픽셀 높이로 처리됩니다. 최종 줄 높이는 글꼴 메트릭에서 얻은 자연 줄 높이보다 작아지지 않도록 제한됩니다.

```cpp
Dali::Ui::Label CreateBodyLabel()
{
  return Dali::Ui::Label::New("Long body text can wrap cleanly in a dali-ui view.")
    .SetFontFamily("SamsungOneUI")
    .SetFontSize(16.0f)
    .SetMultiLine(true)
    .SetLineWrapMode(Dali::Ui::Text::LineWrapMode::WORD)
    .SetLineHeight(1.4f)
    .SetHorizontalTextAlignment(Dali::Ui::Text::Alignment::START);
}
```

방향에 민감한 텍스트의 경우 `SetLayoutDirectionMode`가 텍스트 레이아웃 방향을 결정하는 방식을 제어합니다.

```cpp
Dali::Ui::Label CreateLocaleAwareLabel()
{
  return Dali::Ui::Label::New("Localized label")
    .SetFontSize(16.0f)
    .SetLayoutDirectionMode(Dali::Ui::Text::LayoutDirectionMode::LOCALE);
}
```

현재 레이아웃 너비에서 줄 수가 필요할 때는 레이블 너비가 결정된 뒤 `GetLineCount`를 사용합니다. 가장 최근의 비동기 렌더링 또는 비동기 크기 계산에서 나온 줄 수가 필요하면 `GetAsyncLineCount`를 사용합니다.

```cpp
void UpdateLineDependentState(Dali::Ui::Label label)
{
  int currentLineCount = label.GetLineCount();
  int latestAsyncLineCount = label.GetAsyncLineCount();
}
```

## UI 밀도에 맞게 텍스트 스케일 조정 {#scale-text-for-ui-density}

`SetFontSizeScale`은 현재 글꼴 크기에 배율을 곱합니다. `SetMinimumFontSizeScale`과 `SetMaximumFontSizeScale`은 최종 조정 배율의 범위를 제한합니다. `SetSystemFontSizeScaleEnabled`로 시스템 텍스트 스케일링을 활성화하면, 최소/최대 제한이 적용되기 전에 시스템 배율이 레이블 배율과 결합됩니다.

```cpp
Dali::Ui::Label CreateScalableLabel()
{
  return Dali::Ui::Label::New("Readable text")
    .SetFontSize(18.0f)
    .SetFontSizeScale(1.1f)
    .SetMinimumFontSizeScale(0.85f)
    .SetMaximumFontSizeScale(1.4f)
    .SetSystemFontSizeScaleEnabled(true);
}
```

제한과 시스템 스케일링이 결정된 뒤 실제로 사용될 배율을 확인하려면 `GetAdjustedFontSizeScale`을 사용합니다.

```cpp
float GetEffectiveScale(Dali::Ui::Label label)
{
  float requestedScale = label.GetFontSizeScale();
  float minimumScale = label.GetMinimumFontSizeScale();
  float maximumScale = label.GetMaximumFontSizeScale();
  float adjustedScale = label.GetAdjustedFontSizeScale();

  return adjustedScale;
}
```

## 마크업 텍스트에서 앵커 사용 {#use-anchors-in-markup-text}

마크업 처리가 활성화되어 있으면 `Dali::Ui::Label`은 마크업 텍스트의 앵커를 지원합니다. 앵커 스타일에는 `SetAnchorColor`와 `SetAnchorClickedColor`를 사용합니다. 앵커 처리는 `AnchorClickedSignal`로 노출되며, 콜백은 클릭된 `Dali::Ui::View`와 앵커 `href`를 받습니다.

```cpp
class HelpController : public Dali::ConnectionTracker
{
public:
  Dali::Ui::Label CreateHelpLabel()
  {
    Dali::Ui::Label label = Dali::Ui::Label::New(
      "Read the <a href='privacy'>privacy policy</a>.")
      .SetFontSize(16.0f)
      .SetMarkupEnabled(true)
      .SetAnchorColor(Dali::Ui::UiColor(0x1A73E8))
      .SetAnchorClickedColor(Dali::Ui::UiColor(0x174EA6));

    label.AnchorClickedSignal().Connect(this, &HelpController::OnAnchorClicked);
    return label;
  }

private:
  void OnAnchorClicked(Dali::Ui::View view, const Dali::String& href)
  {
    Dali::Ui::Label label = Dali::Ui::Label::DownCast(view);
    Dali::String clickedHref = href;
  }
};
```

애플리케이션에서 레이블 스타일을 현재 UI 테마와 동기화해야 한다면 `GetAnchorColor`와 `GetAnchorClickedColor`를 사용합니다.

```cpp
void CopyAnchorColors(Dali::Ui::Label source, Dali::Ui::Label target)
{
  target
    .SetAnchorColor(source.GetAnchorColor())
    .SetAnchorClickedColor(source.GetAnchorClickedColor());
}
```

## 비동기 렌더링 {#render-asynchronously}

레이블이 relayout 중에 비동기 텍스트 렌더링을 요청해야 한다면 `SetAsyncRendering`으로 비동기 텍스트 렌더링을 활성화합니다. 기본적으로 레이블 텍스트는 동기적으로 렌더링됩니다. 렌더링 결과는 `AsyncRenderFinishedSignal`로 보고됩니다.

```cpp
class TextRenderController : public Dali::ConnectionTracker
{
public:
  Dali::Ui::Label CreateAsyncLabel()
  {
    Dali::Ui::Label label = Dali::Ui::Label::New("Prepared off the main render path")
      .SetFontSize(16.0f)
      .SetLineHeight(1.3f)
      .SetAsyncRendering(true)
      .SetRenderScale(1.5f);

    label.AsyncRenderFinishedSignal().Connect(
      this,
      &TextRenderController::OnAsyncRenderFinished);

    return label;
  }

private:
  void OnAsyncRenderFinished(Dali::Ui::View view, float width, float height)
  {
    Dali::Ui::Label label = Dali::Ui::Label::DownCast(view);
    int lines = label.GetAsyncLineCount();
  }
};
```

`SetRenderScale`은 비동기 렌더링과 함께만 사용하고, `1.0f` 이상의 값을 사용합니다. 이 설정은 텍스트를 더 큰 배율로 래스터화한 뒤 결과를 축소하므로, 뷰가 시각적으로 확대 또는 축소될 때 품질을 높일 수 있습니다. `SetRenderScale`은 레이블 레이아웃 크기를 변경하지 않습니다.

```cpp
Dali::Ui::Label CreateScaledRenderLabel()
{
  return Dali::Ui::Label::New("High quality scaled text")
    .SetFontSize(20.0f)
    .SetAsyncRendering(true)
    .SetRenderScale(2.0f);
}
```

## 레이블 속성 사용 {#use-label-properties}

`Dali::Ui::Label::Property`는 레이블 전용 텍스트 상태의 속성 ID를 보유합니다. 애플리케이션 설정 코드에서는 `SetFontSize`, `SetFontFamily`, `SetLineHeight`, `SetFontSizeScale`, `SetAsyncRendering` 같은 타입 지정 setter를 우선 사용합니다. 속성 인덱스가 필요한 API와 통합할 때는 속성 ID를 사용합니다.

일반적인 레이블 속성 ID는 다음과 같습니다.

| 속성 ID | 구성되는 값 |
| --- | --- |
| `Dali::Ui::Label::Property::TEXT` | 표시 텍스트 |
| `Dali::Ui::Label::Property::FONT_FAMILY` | 글꼴 패밀리 |
| `Dali::Ui::Label::Property::FONT_SIZE` | 픽셀 단위 글꼴 크기 |
| `Dali::Ui::Label::Property::FONT_SIZE_SCALE` | 레이블 글꼴 배율 |
| `Dali::Ui::Label::Property::MINIMUM_FONT_SIZE_SCALE` | 허용되는 최소 글꼴 배율 |
| `Dali::Ui::Label::Property::MAXIMUM_FONT_SIZE_SCALE` | 허용되는 최대 글꼴 배율 |
| `Dali::Ui::Label::Property::SYSTEM_FONT_SIZE_SCALE_ENABLED` | 시스템 글꼴 스케일링 적용 여부 |
| `Dali::Ui::Label::Property::HORIZONTAL_ALIGNMENT` | 가로 텍스트 정렬 |
| `Dali::Ui::Label::Property::VERTICAL_ALIGNMENT` | 세로 텍스트 정렬 |
| `Dali::Ui::Label::Property::LINE_WRAP_MODE` | 줄바꿈 전략 |
| `Dali::Ui::Label::Property::LINE_HEIGHT` | 줄 높이 값 |
| `Dali::Ui::Label::Property::LINE_HEIGHT_MODE` | 줄 높이 해석 방식 |
| `Dali::Ui::Label::Property::LAYOUT_DIRECTION_MODE` | 텍스트 방향 결정 모드 |
| `Dali::Ui::Label::Property::MARKUP_ENABLED` | 마크업 파싱 |
| `Dali::Ui::Label::Property::ANCHOR_COLOR` | 앵커 텍스트 색상 |
| `Dali::Ui::Label::Property::ANCHOR_CLICKED_COLOR` | 클릭된 앵커 색상 |
| `Dali::Ui::Label::Property::ASYNC_RENDERING` | 비동기 렌더링 |
| `Dali::Ui::Label::Property::RENDER_SCALE` | 비동기 텍스트 렌더링 배율 |
| `Dali::Ui::Label::Property::CUTOUT_ENABLED` | 컷아웃 텍스트 렌더링 |
| `Dali::Ui::Label::Property::TEXT_BACKGROUND_COLOR` | 글리프 뒤에 렌더링되는 배경 |
| `Dali::Ui::Label::Property::TEXT_COLOR` | 텍스트 색상 |

동등한 타입 지정 설정 방식은 더 명확하며 수신자 타입도 명시적으로 유지합니다.

```cpp
Dali::Ui::Label CreateStatusLabel()
{
  return Dali::Ui::Label::New("Connected")
    .SetFontFamily("SamsungOneUI")
    .SetFontSize(14.0f)
    .SetFontWeight(Dali::Ui::Text::FontWeight::MEDIUM)
    .SetFontSizeScale(1.0f)
    .SetLineHeight(1.2f)
    .SetAsyncRendering(false);
}
```
