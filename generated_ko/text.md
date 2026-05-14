---
title: 텍스트
sidebar_label: 텍스트
category: text
---

# 텍스트

텍스트 기능은 dali-ui 애플리케이션을 위한 텍스트 스타일링 및 입력 필터링 기능을 제공합니다. 그림자, 외곽선, 베벨과 같은 시각적 효과를 위한 스타일 객체와 입력 필터링 및 폰트 변형 구성이 포함됩니다.

## 목차

- [텍스트 스타일 객체](#텍스트-스타일-객체)
  - [밑줄 스타일](#밑줄-스타일)
  - [그림자 스타일](#그림자-스타일)
  - [외곽선 스타일](#외곽선-스타일)
  - [취소선 스타일](#취소선-스타일)
  - [베벨 스타일](#베벨-스타일)
- [입력 필터링](#입력-필터링)
- [텍스트 맞춤 구성](#텍스트-맞춤-구성)
- [폰트 변형 축](#폰트-변형-축)

## 텍스트 스타일 객체

텍스트 스타일 객체는 `Label`과 `InputField` 컨트롤에서 텍스트의 시각적 외관을 구성하는 값 타입 클래스입니다. 모든 스타일 객체는 편리한 구성을 위한 플루언트 체이닝을 지원합니다.

### 밑줄 스타일

`Dali::Ui::Text::Underline` 클래스는 텍스트의 밑줄 외관을 구성합니다. 실선, 파선, 이중 밑줄 타입을 지원합니다.

```cpp
// 기본 설정으로 실선 밑줄 생성
Label label = Label::New("밑줄 텍스트")
  .SetUnderline(Text::Underline());

// 커스텀 스타일로 파선 밑줄 생성
Label dashedLabel = Label::New("파선 밑줄")
  .SetUnderline(
    Text::Underline()
      .SetColor(UiColor(0x0088FF))
      .SetThickness(2.0f)
      .SetType(Text::Underline::Type::DASHED)
      .SetDashLength(4.0f)
      .SetDashGap(4.0f));
```

`Underline::Type` 열거형은 렌더링 스타일을 정의합니다:

- `Type::SOLID` - 실선 밑줄 그리기
- `Type::DASHED` - 파선 길이와 간격 설정을 사용하여 파선 밑줄 그리기
- `Type::DOUBLE` - 이중 밑줄 그리기

### 그림자 스타일

`Dali::Ui::Text::Shadow` 클래스는 텍스트의 드롭 섀도우 효과를 구성합니다.

```cpp
// 오프셋만 있는 그림자 생성
Label shadowLabel = Label::New("그림자 텍스트")
  .SetShadow(
    Text::Shadow()
      .SetOffset(Vector2(1.0f, 1.0f)));

// 전체 구성으로 그림자 생성
Label styledShadow = Label::New("스타일된 그림자")
  .SetShadow(
    Text::Shadow()
      .SetColor(UiColor(0xFF5500))
      .SetOffset(Vector2(3.0f, 3.0f))
      .SetBlurRadius(2.0f));
```

`SetOffset()`으로 텍스트를 기준으로 그림자 위치를 지정합니다. 양수 값은 오른쪽과 아래로 오프셋합니다. `SetBlurRadius()`로 부드러운 그림자 가장자리를 만듭니다.

### 외곽선 스타일

`Dali::Ui::Text::Outline` 클래스는 텍스트 문자 주변의 외곽선 효과를 구성합니다.

```cpp
// 단순 외곽선 생성
Label outlineLabel = Label::New("외곽선 텍스트")
  .SetOutline(
    Text::Outline()
      .SetWidth(2.0f));

// 모든 속성이 있는 외곽선 생성
Label styledOutline = Label::New("스타일된 외곽선")
  .SetOutline(
    Text::Outline()
      .SetColor(UiColor(0x0066FF))
      .SetOffset(Vector2(1.0f, 1.0f))
      .SetWidth(2.0f)
      .SetBlurRadius(1.0f));
```

`SetWidth()` 메서드는 픽셀 단위의 외곽선 두께를 제어합니다. `SetOffset()`으로 텍스트를 기준으로 외곽선 위치를 이동합니다.

### 취소선 스타일

`Dali::Ui::Text::LineThrough` 클래스는 텍스트의 취소선 효과를 구성합니다.

```cpp
// 기본 취소선 생성
Label strikeLabel = Label::New("취소선")
  .SetLineThrough(Text::LineThrough());

// 스타일된 취소선 생성
Label styledStrike = Label::New("스타일된 취소선")
  .SetLineThrough(
    Text::LineThrough()
      .SetColor(UiColor(0xFF00FF))
      .SetThickness(3.0f));
```

### 베벨 스타일

`Dali::Ui::Text::Bevel` 클래스는 광원과 그림자 색상을 사용하여 텍스트에 3D와 같은 볼록 또는 새김 효과를 만듭니다.

```cpp
// 볼록 효과 (raised text) 생성
Label embossed = Label::New("볼록")
  .SetTextColor(UiColor(0x333333))
  .SetBevel(
    Text::Bevel()
      .SetDirection(Vector2(-1.0f, -1.0f))
      .SetIntensity(2.0f)
      .SetLightColor(UiColor(0x808080))
      .SetShadowColor(UiColor(0x0D0D0D)));

// 새김 효과 (sunken text) 생성
Label engraved = Label::New("새김")
  .SetTextColor(UiColor(0x212121))
  .SetBevel(
    Text::Bevel()
      .SetDirection(Vector2(-1.0f, -1.0f))
      .SetIntensity(2.0f)
      .SetLightColor(UiColor(0x0D0D0D))
      .SetShadowColor(UiColor(0x808080)));
```

`SetDirection()` 메서드는 광원 방향을 설정합니다. `(-1.0f, -1.0f)` 방향은 왼쪽 상단에서 빛이 오는 것을 시뮬레이션합니다. 광원과 그림자 색상을 교환하여 볼록과 새김 효과를 전환합니다.

## 입력 필터링

`Dali::Ui::Text::InputFilter` 클래스는 `InputField`에 입력할 수 있는 문자를 제한합니다. 정규 표현식 패턴을 사용하여 허용 및 거부 입력을 정의합니다.

```cpp
// 숫자만 허용하지만 0-5는 거부하는 입력 필터 생성
Text::InputFilter inputFilter;
inputFilter.SetAllowPattern("[\\d]");
inputFilter.SetDenyPattern("[0-5]");

InputField inputField = InputField::New()
  .SetText("6-9 숫자만 입력")
  .SetInputFilter(inputFilter);
```

필터는 다음과 같이 입력을 평가합니다:

1. 허용 패턴이 설정되면 입력이 패턴과 일치해야 함
2. 거부 패턴이 설정되면 입력이 패턴과 일치하지 않아야 함
3. 둘 다 설정되면 두 조건 모두 만족해야 함

패턴은 `std::regex` ECMAScript 문법을 따릅니다. 거부된 입력을 처리하려면 `InputRejectedSignal`에 연결합니다:

```cpp
inputField.InputRejectedSignal().Connect(this, &MyClass::OnInputRejected);

void OnInputRejected(View view, Text::InputFilter::RejectReason reason)
{
  // reason은 NOT_ALLOWED 또는 DENIED
}
```

제한을 제거하려면 입력 필터를 지웁니다:

```cpp
inputField.ClearInputFilter();
```

## 텍스트 맞춤 구성

`Dali::Ui::Text::FitRange` 클래스는 사용 가능한 공간에 텍스트를 맞추기 위한 범위 기반 자동 폰트 크기 조정을 구성합니다.

```cpp
// 자동 폰트 크기 조정을 위한 맞춤 범위 생성
Text::FitRange fitRange = Text::FitRange()
  .SetMinimumFontSize(12.0f)
  .SetMaximumFontSize(48.0f)
  .SetFontSizeStep(2.0f);
```

범위 기반 텍스트 맞춤 알고리즘은 최대 폰트 크기에서 지정된 단계만큼 아래로 검색하여 레이아웃 공간에 맞는 크기를 찾습니다. 줄 높이는 현재 텍스트 스타일 구성을 따릅니다.

`Dali::Ui::Text::FitCandidate` 클래스는 각 후보가 특정 폰트 크기와 줄 높이를 정의하는 후보 기반 텍스트 맞춤을 구성합니다:

```cpp
Text::FitCandidate candidate = Text::FitCandidate()
  .SetFontSize(24.0f)
  .SetLineHeight(28.0f);
```

후보 기반 텍스트 맞춤에서 알고리즘은 사용 가능한 레이아웃 공간에 맞는 가장 큰 후보를 선택합니다. 범위 기반 맞춤과 달리 각 후보는 절대 줄 높이 값을 지정합니다.

## 폰트 변형 축

`Dali::Ui::Text::FontVariationAxis` 클래스는 세밀한 폰트 제어를 위한 OpenType 가변 폰트 축을 구성합니다.

```cpp
// 굵기를 위한 폰트 변형 축 생성
Text::FontVariationAxis weightAxis = Text::FontVariationAxis()
  .SetTag("wght")
  .SetValue(450.0f);

// 너비를 위한 폰트 변형 축 생성
Text::FontVariationAxis widthAxis = Text::FontVariationAxis()
  .SetTag("wdth")
  .SetValue(75.0f);
```

일반적인 OpenType 축 태그:

- `"wght"` - 폰트 굵기
- `"wdth"` - 폰트 너비
- `"slnt"` - 기울기
- `"opsz"` - 광학 크기

`Dali::Ui::Text::FontVariation::ToString()`으로 축 벡터를 직렬화하고, `FromString()`으로 설정 문자열을 파싱합니다:

```cpp
Dali::Vector<Text::FontVariationAxis> axes;
axes.PushBack(Text::FontVariationAxis("wght", 450.0f));

Dali::String settings = Text::FontVariation::ToString(axes);
// 설정을 다시 축으로 파싱
Dali::Vector<Text::FontVariationAxis> parsedAxes = Text::FontVariation::FromString(settings);
```
