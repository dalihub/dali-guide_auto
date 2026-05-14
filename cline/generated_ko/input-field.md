---
title: 입력 필드
sidebar_label: 입력 필드
category: views-components
---

# 입력 필드

`InputField`는 텍스트 입력 및 편집을 위한 사용자 상호작용을 지원하는 한 줄 편집 가능 텍스트 뷰로, 포괄적인 커서, 선택, 스타일링 기능을 제공합니다.

## 목차

- [입력 필드 생성](#입력-필드-생성)
- [텍스트 및 플레이스홀더](#텍스트-및-플레이스홀더)
- [폰트 구성](#폰트-구성)
- [커서 구성](#커서-구성)
- [텍스트 선택](#텍스트-선택)
- [비밀번호 입력](#비밀번호-입력)
- [폰트 크기 스케일링](#폰트-크기-스케일링)
- [텍스트 스타일링](#텍스트-스타일링)
- [타이핑 스타일](#타이핑-스타일)
- [시그널](#시그널)

## 입력 필드 생성

`InputField::New()`로 `InputField`를 생성하고 플루언트 세터 API로 구성합니다:

```cpp
using namespace Dali::Ui;

InputField inputField = InputField::New()
    .SetPlaceholder("여기에 텍스트 입력")
    .SetPlaceholderColor(UiColor(0x404040))
    .SetFontSize(20.0f)
    .SetCursorWidth(2)
    .SetCursorColor(UiColor(0x222222))
    .SetSelectionColor(UiColor(0xADD8E6))
    .SetMaximumLength(50)
    .SetRequestedWidth(MATCH_PARENT)
    .SetRequestedHeight(60.0f)
    .SetBackgroundColor(UiColor(0xFFFFFF))
    .SetTextColor(UiColor(0x222222))
    .SetPadding(Extents(12, 12, 12, 12))
    .SetVerticalTextAlignment(Text::Alignment::CENTER)
    .SetFocusable(true);
```

뷰 계층에 `InputField`를 추가합니다:

```cpp
window.Add(inputField);
```

## 텍스트 및 플레이스홀더

### 텍스트 설정 및 조회

`SetText()`로 텍스트 내용을 설정하고 `GetText()`로 조회합니다:

```cpp
inputField.SetText("Hello, World!");
Dali::String currentText = inputField.GetText();
```

### 플레이스홀더 텍스트

입력 필드가 비어 있을 때 표시되는 플레이스홀더 텍스트를 구성합니다:

```cpp
inputField.SetPlaceholder("여기에 입력")
          .SetPlaceholderColor(UiColor(0x808080));
```

필드에 포커스가 있을 때 플레이스홀더가 표시되는지 여부를 제어합니다:

```cpp
inputField.SetShowPlaceholderOnFocus(true);
bool showsOnFocus = inputField.IsPlaceholderShownOnFocus();
```

### 텍스트 정렬

수평 및 수직 텍스트 정렬을 설정합니다:

```cpp
inputField.SetHorizontalTextAlignment(Text::Alignment::CENTER)
          .SetVerticalTextAlignment(Text::Alignment::CENTER);
```

### 오버플로우 모드

사용 가능한 공간을 초과할 때 텍스트 표시 방식을 제어합니다:

```cpp
inputField.SetOverflowMode(Text::OverflowMode::ELLIPSIS);
```

## 폰트 구성

### 폰트 패밀리 및 크기

폰트 패밀리와 크기를 구성합니다:

```cpp
inputField.SetFontFamily("SamsungOneUI_400")
          .SetFontSize(24.0f);
```

### 폰트 스타일 속성

폰트 굵기, 너비, 기울기를 설정합니다:

```cpp
inputField.SetFontWeight(Text::FontWeight::BOLD)
          .SetFontWidth(Text::FontWidth::NORMAL)
          .SetFontSlant(Text::FontSlant::ITALIC);
```

### 폰트 변형

OpenType 폰트 변형 축을 구성합니다:

```cpp
inputField.SetFontVariation("wght=700,wdth=90");
```

또는 타입드 API를 사용합니다:

```cpp
Dali::Vector<Text::FontVariationAxis> axes;
axes.PushBack(Text::FontVariationAxis("wght", 700.0f));
axes.PushBack(Text::FontVariationAxis("wdth", 90.0f));
inputField.SetFontVariation(axes);
```

폰트 변형을 지웁니다:

```cpp
inputField.ClearFontVariation();
```

## 커서 구성

### 커서 외관

커서 너비와 색상을 구성합니다:

```cpp
inputField.SetCursorWidth(2)
          .SetCursorColor(UiColor(0x222222));
```

### 커서 깜빡임

커서 깜빡임 활성화/비활성화 및 깜빡임 간격 설정:

```cpp
inputField.SetCursorBlinkEnabled(true)
          .SetCursorBlinkInterval(0.5f);  // 초

bool isBlinking = inputField.IsCursorBlinkEnabled();
float interval = inputField.GetCursorBlinkInterval();
```

### 커서 위치

문자 인덱스로 커서 위치를 설정하고 조회합니다:

```cpp
inputField.SetCursorPosition(5);
uint32_t position = inputField.GetCursorPosition();
```

### 커서 핸들 이미지

커서 핸들 이미지를 커스터마이즈합니다:

```cpp
inputField.SetCursorHandleImage(RESOURCES_DIR "cursor_handle.png");
inputField.SetCursorHandlePressedImage(RESOURCES_DIR "cursor_handle_pressed.png");
```

## 텍스트 선택

### 선택 활성화

텍스트 선택 활성화/비활성화:

```cpp
inputField.SetSelectionEnabled(true);
bool isEnabled = inputField.IsSelectionEnabled();
```

### 선택 색상

선택된 텍스트의 하이라이트 색상을 설정합니다:

```cpp
inputField.SetSelectionColor(UiColor(0xADD8E6));
```

### 프로그래밍 방식 선택

프로그래밍 방식으로 텍스트를 선택합니다:

```cpp
// 범위 선택
inputField.SelectText(0, 5);  // 처음 5문자 선택

// 전체 텍스트 선택
inputField.SelectWholeText();

// 선택 해제
inputField.ClearSelection();
```

### 선택된 텍스트 가져오기

선택된 텍스트와 그 범위를 조회합니다:

```cpp
Dali::String selected = inputField.GetSelectedText();
uint32_t start = inputField.GetSelectedTextStart();
uint32_t end = inputField.GetSelectedTextEnd();
```

### 선택 핸들 이미지

선택 핸들 이미지를 커스터마이즈합니다:

```cpp
inputField.SetSelectionHandleImageLeft(RESOURCES_DIR "selection_handle_left.png");
inputField.SetSelectionHandleImageRight(RESOURCES_DIR "selection_handle_right.png");
inputField.SetSelectionHandlePressedImageLeft(RESOURCES_DIR "selection_handle_left_pressed.png");
inputField.SetSelectionHandlePressedImageRight(RESOURCES_DIR "selection_handle_right_pressed.png");
```

### 텍스트 핸들

텍스트 편집 핸들(커서 및 선택 핸들)을 활성화합니다:

```cpp
inputField.SetTextHandleEnabled(true)
          .SetTextHandleColor(UiColor(0x000080));
```

## 비밀번호 입력

### 비밀번호 모드

비밀번호 표시 모드를 구성합니다:

```cpp
// 모든 문자 숨기기
inputField.SetPasswordMode(Text::PasswordMode::HIDE_ALL);

// 마지막 문자 잠시 표시
inputField.SetPasswordMode(Text::PasswordMode::REVEAL_LAST_CHARACTER);

// 비밀번호 모드 비활성화
inputField.SetPasswordMode(Text::PasswordMode::NONE);
```

현재 모드를 조회합니다:

```cpp
Text::PasswordMode mode = inputField.GetPasswordMode();
```

### 비밀번호 마스크 문자

비밀번호 텍스트를 마스킹하는 데 사용되는 문자를 설정합니다:

```cpp
inputField.SetPasswordMaskCharacter(0x2022);  // '•' BULLET
inputField.SetPasswordMaskCharacter(0x2A);     // '*'

uint32_t maskChar = inputField.GetPasswordMaskCharacter();
```

### 비밀번호 표시 기간

`REVEAL_LAST_CHARACTER` 모드에서 마지막 문자가 표시되는 시간을 설정합니다:

```cpp
inputField.SetPasswordRevealDuration(1000);  // 밀리초
uint32_t duration = inputField.GetPasswordRevealDuration();
```

## 폰트 크기 스케일링

### 기본 폰트 크기 스케일

폰트 크기에 스케일 계수를 적용합니다:

```cpp
inputField.SetFontSizeScale(1.5f);
float scale = inputField.GetFontSizeScale();
```

### 스케일 제약

최소 및 최대 스케일 한계를 설정합니다:

```cpp
inputField.SetMinimumFontSizeScale(0.5f)
          .SetMaximumFontSizeScale(2.0f);
```

### 시스템 폰트 크기 스케일

자동 시스템 폰트 크기 스케일링을 활성화합니다:

```cpp
inputField.SetSystemFontSizeScaleEnabled(true);
bool usesSystem = inputField.IsSystemFontSizeScaleEnabled();
```

### 조정된 폰트 크기 스케일

제약 적용 후 최종 조정된 스케일을 가져옵니다:

```cpp
float adjustedScale = inputField.GetAdjustedFontSizeScale();
```

## 텍스트 스타일링

InputField는 밑줄, 그림자, 외곽선, 취소선, 텍스트 배경색을 포함한 텍스트 스타일링 기능을 지원합니다.

### 밑줄

텍스트에 밑줄을 추가합니다:

```cpp
// 단순 밑줄
inputField.SetUnderline(Text::Underline());

// 커스터마이즈된 밑줄
inputField.SetUnderline(
    Text::Underline()
        .SetColor(UiColor(0x0088FF))
        .SetThickness(2.0f)
        .SetType(Text::Underline::Type::DASHED)
        .SetDashLength(4.0f)
        .SetDashGap(4.0f));
```

밑줄 스타일을 지웁니다:

```cpp
inputField.ClearUnderline();
```

### 그림자

텍스트에 그림자 효과를 추가합니다:

```cpp
// 단순 그림자
inputField.SetShadow(
    Text::Shadow()
        .SetOffset(Vector2(1.0f, 1.0f)));

// 커스터마이즈된 그림자
inputField.SetShadow(
    Text::Shadow()
        .SetColor(UiColor(0xFF5500))
        .SetOffset(Vector2(3.0f, 3.0f))
        .SetBlurRadius(2.0f));
```

그림자 스타일을 지웁니다:

```cpp
inputField.ClearShadow();
```

### 외곽선

텍스트에 외곽선 효과를 추가합니다:

```cpp
// 단순 외곽선
inputField.SetOutline(
    Text::Outline()
        .SetWidth(2.0f));

// 커스터마이즈된 외곽선
inputField.SetOutline(
    Text::Outline()
        .SetColor(UiColor(0x0066FF))
        .SetOffset(Vector2(1.0f, 1.0f))
        .SetWidth(2.0f)
        .SetBlurRadius(1.0f));
```

외곽선 스타일을 지웁니다:

```cpp
inputField.ClearOutline();
```

### 취소선

취소선 효과를 추가합니다:

```cpp
// 단순 취소선
inputField.SetLineThrough(Text::LineThrough());

// 커스터마이즈된 취소선
inputField.SetLineThrough(
    Text::LineThrough()
        .SetColor(UiColor(0xFF00FF))
        .SetThickness(3.0f));
```

취소선 스타일을 지웁니다:

```cpp
inputField.ClearLineThrough();
```

### 텍스트 배경색

텍스트 글리프 뒤에 배경색을 설정합니다:

```cpp
inputField.SetTextBackgroundColor(UiColor(0xFFFF00));
```

텍스트 배경색을 지웁니다:

```cpp
inputField.ClearTextBackgroundColor();
```

## 타이핑 스타일

타이핑 스타일을 사용하여 선택된 텍스트나 커서 위치에 삽입될 텍스트에 스타일을 적용할 수 있습니다.

### 타이핑 텍스트 색상

```cpp
inputField.SetTypingTextColor(UiColor(0xFF00FF));  // 마젠타
UiColor typingColor = inputField.GetTypingTextColor();
```

### 타이핑 폰트 패밀리

```cpp
inputField.SetTypingFontFamily("DejaVu Sans");
Dali::String family = inputField.GetTypingFontFamily();
```

### 타이핑 폰트 크기

```cpp
inputField.SetTypingFontSize(18.0f);
float size = inputField.GetTypingFontSize();
```

### 타이핑 폰트 스타일

```cpp
inputField.SetTypingFontWeight(Text::FontWeight::BOLD);
inputField.SetTypingFontWidth(Text::FontWidth::CONDENSED);
inputField.SetTypingFontSlant(Text::FontSlant::ITALIC);
```

## 시그널

### 텍스트 변경 시그널

텍스트 내용이 변경될 때 발생:

```cpp
void OnTextChanged(View view) {
    InputField field = InputField::DownCast(view);
    if (field) {
        Dali::String newText = field.GetText();
        // 텍스트 변경 처리
    }
}

// 시그널 연결
inputField.TextChangedSignal().Connect(this, &YourClass::OnTextChanged);
```

### 최대 길이 도달 시그널

입력이 최대 길이에 도달할 때 발생:

```cpp
void OnMaximumLengthReached(View view) {
    // 사용자에게 최대 길이 도달 알림
}

inputField.SetMaximumLength(50);
inputField.MaximumLengthReachedSignal().Connect(this, &YourClass::OnMaximumLengthReached);
```

### 커서 위치 변경 시그널

커서 위치가 변경될 때 발생:

```cpp
void OnCursorPositionChanged(View view, uint32_t position) {
    // 커서 이동에 반응
}

inputField.CursorPositionChangedSignal().Connect(this, &YourClass::OnCursorPositionChanged);
```

### 선택 시그널

선택 변경을 모니터링합니다:

```cpp
void OnSelectionStarted(View view) {
    // 선택 시작
}

void OnSelectionChanged(View view, uint32_t start, uint32_t end) {
    // 선택 범위 변경
}

void OnSelectionCleared(View view) {
    // 선택 해제
}

inputField.SelectionStartedSignal().Connect(this, &YourClass::OnSelectionStarted);
inputField.SelectionChangedSignal().Connect(this, &YourClass::OnSelectionChanged);
inputField.SelectionClearedSignal().Connect(this, &YourClass::OnSelectionCleared);
```

### 입력 거부 시그널

입력 필터에 의해 입력이 거부될 때 발생:

```cpp
void OnInputRejected(View view, Text::InputFilter::RejectReason reason) {
    if (reason == Text::InputFilter::RejectReason::NOT_ALLOWED) {
        // 입력이 허용 패턴과 일치하지 않음
    }
}

Text::InputFilter filter;
filter.SetAllowPattern("[\\d]");  // 숫자만
inputField.SetInputFilter(filter);
inputField.InputRejectedSignal().Connect(this, &YourClass::OnInputRejected);
```

### 타이핑 스타일 변경 시그널

커서 위치의 타이핑 스타일이 변경될 때 발생:

```cpp
void OnTypingStyleChanged(View view, Text::TypingStyle::Mask mask) {
    if (mask & Text::TypingStyle::TEXT_COLOR) {
        // 커서의 텍스트 색상 변경
    }
    if (mask & Text::TypingStyle::FONT_FAMILY) {
        // 커서의 폰트 패밀리 변경
    }
}

inputField.TypingStyleChangedSignal().Connect(this, &YourClass::OnTypingStyleChanged);
```

## 추가 구성

### 편집 가능 상태

필드 편집 가능 여부를 제어합니다:

```cpp
inputField.SetEditable(false);
bool isEditable = inputField.IsEditable();
```

### 마크업 지원

텍스트에서 마크업 처리를 활성화합니다:

```cpp
inputField.SetMarkupEnabled(true);
inputField.SetText("<color value='#FF0000'>빨간 텍스트</color>");
```

### 레이아웃 방향

텍스트 방향 결정 방식을 설정합니다:

```cpp
inputField.SetLayoutDirectionMode(Text::LayoutDirectionMode::INHERIT);
```

### 입력 필터

입력할 수 있는 텍스트를 제어하는 입력 필터를 설정합니다:

```cpp
Text::InputFilter filter;
filter.SetAllowPattern("[\\d]")    // 숫자만 허용
      .SetDenyPattern("[0-5]");    // 0-5는 거부
inputField.SetInputFilter(filter);

// 필터 지우기
inputField.ClearInputFilter();
```
