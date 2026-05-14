---
title: 레이블
sidebar_label: 레이블
category: views-components
---

# 레이블

Label은 dali-ui 텍스트 렌더링 백엔드를 사용하여 텍스트를 표시하는 편집 불가능한 View입니다.

## 목차

- [레이블 생성](#레이블-생성)
- [텍스트 및 폰트 구성](#텍스트-및-폰트-구성)
- [텍스트 정렬 및 레이아웃](#텍스트-정렬-및-레이아웃)
- [여러 줄 텍스트 및 오버플로우](#여러-줄-텍스트-및-오버플로우)
- [줄 높이 제어](#줄-높이-제어)
- [마퀴 애니메이션](#마퀴-애니메이션)
- [폰트 크기 스케일링](#폰트-크기-스케일링)
- [비동기 렌더링](#비동기-렌더링)
- [텍스트 스타일 효과](#텍스트-스타일-효과)
- [앵커 링크](#앵커-링크)

## 레이블 생성

정적 `New()` 메서드로 `Label`을 생성합니다. 빈 레이블을 생성하거나 텍스트로 초기화할 수 있습니다.

```cpp
// 빈 레이블 생성
Label label1 = Label::New();

// 초기 텍스트로 레이블 생성
Label label2 = Label::New("Hello, World!");
```

플루언트 체이닝을 사용하여 간결하게 구성합니다:

```cpp
Label label = Label::New("환영합니다")
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(24.0f)
    .SetTextColor(UiColor(0x222222))
    .SetHorizontalTextAlignment(Text::Alignment::CENTER)
    .SetVerticalTextAlignment(Text::Alignment::CENTER);
```

## 텍스트 및 폰트 구성

### 텍스트 설정

`SetText()`로 표시되는 텍스트를 업데이트합니다. `GetText()`로 현재 텍스트를 조회합니다.

```cpp
label.SetText("업데이트된 텍스트 내용");
Dali::String currentText = label.GetText();
```

### 폰트 패밀리

`SetFontFamily()`로 폰트 패밀리를 지정합니다:

```cpp
label.SetFontFamily("Ubuntu Mono");
Dali::String fontFamily = label.GetFontFamily();
```

### 폰트 크기

`SetFontSize()`로 픽셀 단위 폰트 크기를 설정합니다:

```cpp
label.SetFontSize(20.0f);
float fontSize = label.GetFontSize();
```

### 폰트 굵기, 너비, 기울기

`SetFontWeight()`, `SetFontWidth()`, `SetFontSlant()`로 폰트 스타일링을 제어합니다:

```cpp
label.SetFontWeight(Text::FontWeight::BOLD);
label.SetFontWidth(Text::FontWidth::NORMAL);
label.SetFontSlant(Text::FontSlant::ITALIC);

Text::FontWeight weight = label.GetFontWeight();
Text::FontWidth width = label.GetFontWidth();
Text::FontSlant slant = label.GetFontSlant();
```

사용 가능한 폰트 굵기 값은 `THIN`, `LIGHT`, `NORMAL`, `MEDIUM`, `SEMI_BOLD`, `BOLD`, `BLACK`입니다.

### 폰트 변형 축

가변 폰트의 경우 `SetFontVariation()`으로 폰트 변형 축을 구성합니다:

```cpp
// 설정 문자열 사용
label.SetFontVariation("wght=700,wdth=90");

// 축 벡터 사용
Dali::Vector<Text::FontVariationAxis> axes;
axes.PushBack(Text::FontVariationAxis("wght", 700.0f));
axes.PushBack(Text::FontVariationAxis("wdth", 90.0f));
label.SetFontVariation(axes);

// 현재 축 조회
Dali::Vector<Text::FontVariationAxis> currentAxes = label.GetFontVariation();

// 폰트 변형 지우기
label.ClearFontVariation();
```

## 텍스트 정렬 및 레이아웃

### 수평 및 수직 정렬

레이블 경계 내 텍스트 정렬을 제어합니다:

```cpp
label.SetHorizontalTextAlignment(Text::Alignment::CENTER);
label.SetVerticalTextAlignment(Text::Alignment::CENTER);

Text::Alignment hAlign = label.GetHorizontalTextAlignment();
Text::Alignment vAlign = label.GetVerticalTextAlignment();
```

정렬 값은 `START`, `CENTER`, `END`입니다.

### 레이아웃 방향 모드

텍스트 레이아웃 방향 결정 방식을 제어합니다:

```cpp
label.SetLayoutDirectionMode(Text::LayoutDirectionMode::CONTENTS);
Text::LayoutDirectionMode mode = label.GetLayoutDirectionMode();
```

사용 가능한 모드:
- `CONTENTS`: 텍스트 내용에서 방향 결정
- `INHERIT`: 부모 뷰에서 상속
- `LOCALE`: 시스템 로케일 사용

## 여러 줄 텍스트 및 오버플로우

### 여러 줄 레이아웃

`SetMultiLine()`으로 여러 줄 텍스트 레이아웃을 활성화합니다:

```cpp
label.SetMultiLine(true);
bool isMultiLine = label.IsMultiLine();
```

### 줄 바꿈 모드

사용 가능한 너비를 초과할 때 텍스트 줄바꿈 방식을 제어합니다:

```cpp
label.SetLineWrapMode(Text::LineWrapMode::WORD);
Text::LineWrapMode wrapMode = label.GetLineWrapMode();
```

사용 가능한 줄바꿈 모드:
- `WORD`: 단어 경계에서 줄바꿈
- `CHARACTER`: 개별 문자에서 줄바꿈
- `HYPHENATION`: 하이픈을 사용하여 줄바꿈
- `MIXED`: 단어, 하이픈, 문자 순서로 시도

### 오버플로우 모드

레이블 경계를 초과하는 텍스트 처리:

```cpp
label.SetOverflowMode(Text::OverflowMode::ELLIPSIS);
Text::OverflowMode mode = label.GetOverflowMode();
```

사용 가능한 모드:
- `CLIP`: 경계에서 텍스트 자르기
- `ELLIPSIS`: 말줄임표(...)로 자르기

### 줄 수

현재 레이아웃의 줄 수를 조회합니다:

```cpp
int lines = label.GetLineCount();
int linesAtWidth = label.GetLineCount(200.0f);
```

## 줄 높이 제어

`SetLineHeight()`와 `SetLineHeightMode()`로 줄 높이를 제어합니다:

```cpp
// 상대 모드: 폰트 크기의 배수로 줄 높이
label.SetLineHeightMode(Text::LineHeightMode::RELATIVE);
label.SetLineHeight(1.5f);

// 절대 모드: 픽셀 단위 줄 높이
label.SetLineHeightMode(Text::LineHeightMode::ABSOLUTE);
label.SetLineHeight(50.0f);

// 자동: 폰트 메트릭에서 자연스러운 줄 높이 사용
label.SetLineHeight(Text::LINE_HEIGHT_AUTO);
```

현재 설정을 조회합니다:

```cpp
float lineHeight = label.GetLineHeight();
Text::LineHeightMode mode = label.GetLineHeightMode();
```

## 마퀴 애니메이션

마퀴 애니메이션은 사용 가능한 공간을 초과하는 텍스트를 스크롤합니다.

### 트리거 정책

마퀴 시작 시점을 제어합니다:

```cpp
label.SetMarqueeTriggerPolicy(Text::MarqueeTriggerPolicy::MANUAL);
Text::MarqueeTriggerPolicy policy = label.GetMarqueeTriggerPolicy();
```

사용 가능한 정책:
- `MANUAL`: `StartMarquee()`가 호출될 때만 시작
- `ON_OVERFLOW`: 레이아웃 중 텍스트 오버플로우 시 자동 시작

### 방향

스크롤 방향을 설정합니다:

```cpp
label.SetMarqueeOrientation(Text::MarqueeOrientation::HORIZONTAL);
Text::MarqueeOrientation orientation = label.GetMarqueeOrientation();
```

- `HORIZONTAL`: 한 줄 텍스트용
- `VERTICAL`: 여러 줄 텍스트용

### 속도 및 타이밍

마퀴 속도와 타이밍을 구성합니다:

```cpp
label.SetMarqueeSpeed(100);           // 초당 픽셀
label.SetMarqueeLoopCount(3);         // 루프 횟수
label.SetMarqueeLoopDelay(0.5f);      // 루프 간 지연 (초)
label.SetMarqueeGap(50);              // 래핑 전 간격 (픽셀)

int speed = label.GetMarqueeSpeed();
int loopCount = label.GetMarqueeLoopCount();
float delay = label.GetMarqueeLoopDelay();
int gap = label.GetMarqueeGap();
```

### 정지 모드

마퀴 정지 방식을 제어합니다:

```cpp
label.SetMarqueeStopMode(Text::MarqueeStopMode::FINISH_LOOP);
Text::MarqueeStopMode stopMode = label.GetMarqueeStopMode();
```

사용 가능한 모드:
- `IMMEDIATE`: 즉시 정지
- `FINISH_LOOP`: 현재 루프 완료 후 정지

### 시작 및 정지

프로그래밍 방식으로 마퀴를 제어합니다:

```cpp
label.StartMarquee();
label.StopMarquee();
bool isRunning = label.IsMarqueeRunning();
```

## 폰트 크기 스케일링

접근성 또는 반응형 디자인을 위해 폰트 크기 스케일링을 적용합니다.

### 기본 스케일링

```cpp
label.SetFontSizeScale(1.5f);
float scale = label.GetFontSizeScale();
```

### 최소 및 최대 제약

유효 스케일의 범위를 설정합니다:

```cpp
label.SetMinimumFontSizeScale(0.8f);
label.SetMaximumFontSizeScale(2.0f);

float minScale = label.GetMinimumFontSizeScale();
float maxScale = label.GetMaximumFontSizeScale();
```

### 시스템 폰트 스케일

시스템 폰트 크기 스케일 통합을 활성화합니다:

```cpp
label.SetSystemFontSizeScaleEnabled(true);
bool enabled = label.IsSystemFontSizeScaleEnabled();
```

### 조정된 스케일

모든 제약 후 최종 조정된 스케일을 조회합니다:

```cpp
float adjustedScale = label.GetAdjustedFontSizeScale();
```

## 비동기 렌더링

복잡한 텍스트 레이아웃의 경우 UI 스레드 차단을 피하기 위해 비동기 렌더링을 활성화합니다.

### 비동기 렌더링 활성화

```cpp
label.SetAsyncRendering(true);
bool isAsync = label.IsAsyncRendering();
```

### 렌더 스케일

뷰가 시각적으로 스케일될 때 품질을 향상시킵니다:

```cpp
label.SetRenderScale(2.0f);  // 2배로 렌더링 후 다운스케일
float scale = label.GetRenderScale();
```

### 비동기 크기 계산

비동기로 크기 계산을 요청합니다:

```cpp
label.RequestAsyncNaturalSize();
label.RequestAsyncHeightForWidth(300.0f);
int asyncLines = label.GetAsyncLineCount();
```

### 비동기 시그널

비동기 작업 완료 시그널에 연결합니다:

```cpp
label.AsyncRenderFinishedSignal().Connect(this, &MyClass::OnAsyncRenderFinished);
label.AsyncNaturalSizeComputedSignal().Connect(this, &MyClass::OnAsyncNaturalSize);
label.AsyncHeightForWidthComputedSignal().Connect(this, &MyClass::OnAsyncHeightForWidth);
```

시그널 핸들러 시그니처:

```cpp
void OnAsyncRenderFinished(View view, float width, float height);
void OnAsyncNaturalSize(View view, float width, float height);
void OnAsyncHeightForWidth(View view, float width, float height);
```

## 텍스트 스타일 효과

### 텍스트 색상

`SetTextColor()`로 텍스트 색상을 설정합니다:

```cpp
label.SetTextColor(UiColor(0x333333));
UiColor color = label.GetTextColor();
```

### 텍스트 배경

텍스트 글리프 뒤에 배경색을 설정합니다:

```cpp
label.SetTextBackgroundColor(UiColor(0xFFFF00));
UiColor bgColor = label.GetTextBackgroundColor();
label.ClearTextBackgroundColor();
```

### 밑줄

밑줄 스타일링을 적용합니다:

```cpp
Text::Underline underline;
underline.enable = true;
underline.color = UiColor(0xFF0000);
underline.height = 2.0f;
label.SetUnderline(underline);
label.ClearUnderline();
```

### 그림자

그림자 효과를 적용합니다:

```cpp
Text::Shadow shadow;
shadow.enable = true;
shadow.color = UiColor(0x000000);
shadow.offset = Vector2(2.0f, 2.0f);
label.SetShadow(shadow);
label.ClearShadow();
```

### 외곽선

외곽선 스타일링을 적용합니다:

```cpp
Text::Outline outline;
outline.enable = true;
outline.color = UiColor(0xFFFFFF);
outline.width = 1.0f;
label.SetOutline(outline);
label.ClearOutline();
```

### 취소선

취소선 스타일링을 적용합니다:

```cpp
Text::LineThrough lineThrough;
lineThrough.enable = true;
lineThrough.color = UiColor(0xFF0000);
label.SetLineThrough(lineThrough);
label.ClearLineThrough();
```

### 베벨

베벨 효과를 적용합니다:

```cpp
Text::Bevel bevel;
label.SetBevel(bevel);
label.ClearBevel();
```

### 컷아웃 효과

텍스트를 컷아웃 마스크로 렌더링합니다:

```cpp
label.SetCutoutEnabled(true);
bool isCutout = label.IsCutoutEnabled();
```

### 마스크 효과

다른 뷰를 마스크 소스로 사용합니다:

```cpp
Label label = Label::New("마스크된 텍스트");
View maskView = CreateGradientView();
label.SetMaskEffect(maskView);
label.ClearMaskEffect();
```

## 앵커 링크

텍스트에서 앵커 링크를 지원하기 위해 마크업 처리를 활성화합니다.

### 마크업 활성화

```cpp
label.SetMarkupEnabled(true);
bool isMarkupEnabled = label.IsMarkupEnabled();
```

### 앵커 색상

앵커 외관을 구성합니다:

```cpp
label.SetAnchorColor(UiColor(0x0000FF));
label.SetAnchorClickedColor(UiColor(0xFF0000));

UiColor anchorColor = label.GetAnchorColor();
UiColor clickedColor = label.GetAnchorClickedColor();
```

### 앵커 클릭 처리

링크 상호작용을 처리하기 위해 `AnchorClickedSignal()`에 연결합니다:

```cpp
label.AnchorClickedSignal().Connect(this, &MyClass::OnAnchorClicked);

void OnAnchorClicked(View view, const Dali::String& href)
{
    // 앵커 클릭 처리
    // href에 링크 대상이 포함됨
}
```
