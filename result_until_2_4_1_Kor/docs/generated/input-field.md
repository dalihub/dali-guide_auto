---
title: 입력 필드
sidebar_label: 입력 필드
category: views-components
---

# 입력 필드 {#input-field}

`Dali::Ui::InputField`는 앱의 텍스트 입력, 커서 처리, 텍스트 선택, 입력 관련 시그널을 위한 dali-ui의 한 줄 편집 가능 텍스트 뷰입니다.

## 목차 {#table-of-contents}

- [입력 필드 생성](#create-an-input-field)
- [커서 동작 제어](#control-cursor-behavior)
- [텍스트, 글꼴, 스케일 상태 읽기](#read-text-font-and-scale-state)
- [편집 시그널 처리](#react-to-editing-signals)
- [선택 및 비밀번호 상태](#selection-and-password-state)
- [속성 상수](#property-constants)

## 입력 필드 생성 {#create-an-input-field}

`Dali::Ui::InputField::New`로 입력 필드를 생성합니다. dali-ui 애플리케이션 코드에서는 이 컨트롤을 `Dali::Ui::View`에서 파생된 핸들로 다루고, 사용 가능한 경우 타입 지정 setter로 설정합니다.

```cpp
#include <dali-ui-foundation/dali-ui-foundation.h>

using namespace Dali;
using namespace Dali::Ui;

InputField CreateNameInput()
{
  return InputField::New()
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(20.0f)
    .SetFontSizeScale(1.0f)
    .SetFontSlant(Text::FontSlant::NORMAL)
    .SetEditable(true);
}
```

`Dali::Ui::InputField`는 `Dali::Ui::View`에서 파생되므로, 주변 UI가 공통 뷰 객체 모델만 필요로 할 때 앱 코드에서 `Dali::Ui::View`로 저장하거나 전달할 수 있습니다.

```cpp
View CreateInputView()
{
  InputField input = InputField::New()
    .SetFontFamily("SamsungOneUI_400")
    .SetFontSize(18.0f)
    .SetEditable(true);

  return input;
}
```

입력 필드가 텍스트 관련 상태는 표시하되 사용자 편집은 받지 않아야 할 때 `Dali::Ui::InputField::SetEditable`을 사용합니다.

```cpp
void SetReadOnly(InputField input)
{
  input.SetEditable(false);
}
```

## 커서 동작 제어 {#control-cursor-behavior}

커서 API는 `Dali::Ui::InputField`가 제공합니다. 이 API를 사용해 커서 너비, 색상, 깜박임 타이밍, 핸들 이미지, 현재 삽입 위치를 제어합니다. 커서 위치는 현재 텍스트 안의 문자 인덱스입니다.

```cpp
void ConfigureCursor(InputField input)
{
  input
    .SetCursorWidth(2)
    .SetCursorColor(UiColor(0x222222))
    .SetCursorBlinkEnabled(true)
    .SetCursorBlinkInterval(0.5f)
    .SetCursorPosition(0u);
}
```

커서 핸들 이미지는 일반 상태와 눌린 상태에 대해 별도로 설정합니다.

```cpp
void ConfigureCursorHandle(InputField input)
{
  input
    .SetCursorHandleImage("/opt/apps/example/res/cursor_handle.png")
    .SetCursorHandlePressedImage("/opt/apps/example/res/cursor_handle_pressed.png");
}
```

일치하는 getter를 통해 동일한 커서 상태를 다시 읽을 수 있습니다.

```cpp
void SnapshotCursorState(InputField input)
{
  const uint32_t position = input.GetCursorPosition();
  const int width = input.GetCursorWidth();
  const float blinkInterval = input.GetCursorBlinkInterval();
  const UiColor color = input.GetCursorColor();

  (void)position;
  (void)width;
  (void)blinkInterval;
  (void)color;
}
```

## 텍스트, 글꼴, 스케일 상태 읽기 {#read-text-font-and-scale-state}

`Dali::Ui::InputField`는 텍스트, 글꼴 패밀리, 크기, 스케일, 기울기, 굵기, 너비, 오버플로, 정렬, 플레이스홀더, 레이아웃 방향에 대한 타입 지정 getter를 제공합니다. 앱에서 주변 UI를 현재 입력 설정과 동기화해야 할 때 이러한 getter를 사용합니다.

```cpp
void ReadTypography(InputField input)
{
  const Dali::String text = input.GetText();
  const Dali::String family = input.GetFontFamily();
  const float fontSize = input.GetFontSize();
  const float requestedScale = input.GetFontSizeScale();
  const float adjustedScale = input.GetAdjustedFontSizeScale();
  const Text::FontSlant slant = input.GetFontSlant();
  const Text::FontWeight weight = input.GetFontWeight();
  const Text::FontWidth width = input.GetFontWidth();

  (void)text;
  (void)family;
  (void)fontSize;
  (void)requestedScale;
  (void)adjustedScale;
  (void)slant;
  (void)weight;
  (void)width;
}
```

`Dali::Ui::InputField::GetAdjustedFontSizeScale`이 반환하는 조정된 스케일은 최소, 최대, 시스템 스케일 설정을 반영한 뒤 렌더링에 사용되는 스케일입니다.

```cpp
float GetRenderedFontSize(InputField input)
{
  return input.GetFontSize() * input.GetAdjustedFontSizeScale();
}
```

레이아웃에 민감한 UI에서는 입력 필드에서 텍스트 정렬과 오버플로 정책을 읽습니다.

```cpp
void ReadLayoutPolicy(InputField input)
{
  const Text::Alignment horizontal = input.GetHorizontalTextAlignment();
  const Text::Alignment vertical = input.GetVerticalTextAlignment();
  const Text::OverflowMode overflow = input.GetOverflowMode();
  const Text::LayoutDirectionMode direction = input.GetLayoutDirectionMode();

  (void)horizontal;
  (void)vertical;
  (void)overflow;
  (void)direction;
}
```

## 편집 시그널 처리 {#react-to-editing-signals}

필드 인스턴스에서 `Dali::Ui::InputField` 시그널에 연결합니다. 시그널 콜백은 `Dali::Ui::View`를 받으므로 핸들러가 dali-ui 뷰 객체 모델에 맞게 유지됩니다.

```cpp
class InputFieldHandler
{
public:
  void Attach(InputField input)
  {
    mInput = input;

    mInput.TextChangedSignal().Connect(this, &InputFieldHandler::OnTextChanged);
    mInput.MaximumLengthReachedSignal().Connect(this, &InputFieldHandler::OnMaximumLengthReached);
    mInput.CursorPositionChangedSignal().Connect(this, &InputFieldHandler::OnCursorPositionChanged);
  }

private:
  void OnTextChanged(View view)
  {
    const uint32_t cursor = mInput.GetCursorPosition();
    (void)view;
    (void)cursor;
  }

  void OnMaximumLengthReached(View view)
  {
    const int maximumLength = mInput.GetMaximumLength();
    (void)view;
    (void)maximumLength;
  }

  void OnCursorPositionChanged(View view, uint32_t position)
  {
    (void)view;
    (void)position;
  }

  InputField mInput;
};
```

선택 시그널은 선택이 시작되거나 변경되거나 해제될 때 이를 알립니다.

```cpp
class SelectionHandler
{
public:
  void Attach(InputField input)
  {
    mInput = input;

    mInput.SelectionStartedSignal().Connect(this, &SelectionHandler::OnSelectionStarted);
    mInput.SelectionChangedSignal().Connect(this, &SelectionHandler::OnSelectionChanged);
    mInput.SelectionClearedSignal().Connect(this, &SelectionHandler::OnSelectionCleared);
  }

private:
  void OnSelectionStarted(View view)
  {
    (void)view;
  }

  void OnSelectionChanged(View view, uint32_t start, uint32_t end)
  {
    const Dali::String selected = mInput.GetSelectedText();
    (void)view;
    (void)start;
    (void)end;
    (void)selected;
  }

  void OnSelectionCleared(View view)
  {
    (void)view;
  }

  InputField mInput;
};
```

`Dali::Ui::InputField::InputRejectedSignal`은 설정된 `Dali::Ui::Text::InputFilter`에 의해 입력이 거부되었음을 알리고, `Dali::Ui::InputField::TypingStyleChangedSignal`은 현재 커서 위치 또는 선택된 텍스트 범위에서 감지된 입력 스타일 변경을 알립니다.

```cpp
class InputPolicyHandler
{
public:
  void Attach(InputField input)
  {
    mInput = input;

    mInput.InputRejectedSignal().Connect(this, &InputPolicyHandler::OnInputRejected);
    mInput.TypingStyleChangedSignal().Connect(this, &InputPolicyHandler::OnTypingStyleChanged);
  }

private:
  void OnInputRejected(View view, Text::InputFilter::RejectReason reason)
  {
    (void)view;
    (void)reason;
  }

  void OnTypingStyleChanged(View view, Text::TypingStyle::Mask mask)
  {
    (void)view;
    (void)mask;
  }

  InputField mInput;
};
```

## 선택 및 비밀번호 상태 {#selection-and-password-state}

선택된 텍스트를 읽으려면 `Dali::Ui::InputField::GetSelectedText`를 사용하고, 이 값이 변경되었을 수 있는 시점은 선택 시그널로 확인합니다.

```cpp
Dali::String CurrentSelection(InputField input)
{
  return input.GetSelectedText();
}
```

비밀번호 관련 상태는 `Dali::Ui::InputField` getter를 통해 제공됩니다. `Dali::Ui::InputField::GetPasswordMode`는 현재 표시 모드를 반환하고, `Dali::Ui::InputField::GetPasswordMaskCharacter`는 마스킹에 사용되는 Unicode 코드 포인트를 반환하며, `Dali::Ui::InputField::GetPasswordRevealDuration`은 표시 지속 시간을 밀리초 단위로 반환합니다.

```cpp
struct PasswordDisplayState
{
  Text::PasswordMode mode;
  uint32_t maskCharacter;
  uint32_t revealDurationMilliseconds;
};

PasswordDisplayState ReadPasswordDisplayState(InputField input)
{
  return PasswordDisplayState{
    input.GetPasswordMode(),
    input.GetPasswordMaskCharacter(),
    input.GetPasswordRevealDuration()
  };
}
```

## 속성 상수 {#property-constants}

`Dali::Ui::InputField::Property`는 입력 필드 설정에 사용되는 속성 상수를 소유합니다. 애플리케이션 코드에서는 가능한 경우 타입 지정 setter와 getter를 우선 사용하고, 속성 기반 코드 경로와 통합할 때 이러한 속성 식별자를 사용합니다.

| 영역 | 소유한 속성 상수 |
| --- | --- |
| 텍스트 내용 및 색상 | `Dali::Ui::InputField::Property::TEXT`, `Dali::Ui::InputField::Property::TEXT_COLOR`, `Dali::Ui::InputField::Property::TEXT_BACKGROUND_COLOR` |
| 글꼴 | `Dali::Ui::InputField::Property::FONT_FAMILY`, `Dali::Ui::InputField::Property::FONT_SIZE`, `Dali::Ui::InputField::Property::FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::FONT_WEIGHT`, `Dali::Ui::InputField::Property::FONT_WIDTH`, `Dali::Ui::InputField::Property::FONT_SLANT` |
| 글꼴 스케일링 | `Dali::Ui::InputField::Property::MINIMUM_FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::MAXIMUM_FONT_SIZE_SCALE`, `Dali::Ui::InputField::Property::SYSTEM_FONT_SIZE_SCALE_ENABLED` |
| 플레이스홀더 | `Dali::Ui::InputField::Property::PLACEHOLDER`, `Dali::Ui::InputField::Property::PLACEHOLDER_COLOR`, `Dali::Ui::InputField::Property::SHOW_PLACEHOLDER_ON_FOCUS` |
| 커서 | `Dali::Ui::InputField::Property::CURSOR_WIDTH`, `Dali::Ui::InputField::Property::CURSOR_COLOR`, `Dali::Ui::InputField::Property::CURSOR_BLINK_ENABLED`, `Dali::Ui::InputField::Property::CURSOR_BLINK_INTERVAL`, `Dali::Ui::InputField::Property::CURSOR_POSITION`, `Dali::Ui::InputField::Property::CURSOR_HANDLE_IMAGE`, `Dali::Ui::InputField::Property::CURSOR_HANDLE_PRESSED_IMAGE` |
| 선택 | `Dali::Ui::InputField::Property::SELECTION_ENABLED`, `Dali::Ui::InputField::Property::SELECTION_COLOR`, `Dali::Ui::InputField::Property::SELECTED_TEXT`, `Dali::Ui::InputField::Property::SELECTED_TEXT_START`, `Dali::Ui::InputField::Property::SELECTED_TEXT_END` |
| 선택 및 텍스트 핸들 | `Dali::Ui::InputField::Property::TEXT_HANDLE_ENABLED`, `Dali::Ui::InputField::Property::TEXT_HANDLE_COLOR`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_IMAGE_LEFT`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_IMAGE_RIGHT`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_PRESSED_IMAGE_LEFT`, `Dali::Ui::InputField::Property::SELECTION_HANDLE_PRESSED_IMAGE_RIGHT` |
| 비밀번호 및 편집 | `Dali::Ui::InputField::Property::PASSWORD_MODE`, `Dali::Ui::InputField::Property::PASSWORD_MASK_CHARACTER`, `Dali::Ui::InputField::Property::PASSWORD_REVEAL_DURATION`, `Dali::Ui::InputField::Property::EDITABLE`, `Dali::Ui::InputField::Property::MAXIMUM_LENGTH` |
| 레이아웃 및 파싱 | `Dali::Ui::InputField::Property::HORIZONTAL_ALIGNMENT`, `Dali::Ui::InputField::Property::VERTICAL_ALIGNMENT`, `Dali::Ui::InputField::Property::OVERFLOW_MODE`, `Dali::Ui::InputField::Property::LAYOUT_DIRECTION_MODE`, `Dali::Ui::InputField::Property::MARKUP_ENABLED` |
| 입력 스타일 | `Dali::Ui::InputField::Property::TYPING_TEXT_COLOR`, `Dali::Ui::InputField::Property::TYPING_FONT_FAMILY`, `Dali::Ui::InputField::Property::TYPING_FONT_SIZE`, `Dali::Ui::InputField::Property::TYPING_FONT_WEIGHT`, `Dali::Ui::InputField::Property::TYPING_FONT_WIDTH`, `Dali::Ui::InputField::Property::TYPING_FONT_SLANT` |
