---
title: 접근성 하이라이트 오버레이
sidebar_label: 접근성 하이라이트 오버레이
category: accessibility
---
# 접근성 하이라이트 오버레이

접근성 하이라이트 오버레이는 접근 가능한 뷰에 시각적 하이라이트 표시기를 제공하며, 화면 판독기 및 접근성 서비스를 위한 자동 및 수동 위치 지정 모드를 모두 지원합니다.

## 목차

- [오버레이 모드](#오버레이-모드)
- [커스텀 하이라이트 위치 지정](#커스텀-하이라이트-위치-지정)
- [오버레이 업데이트 및 숨기기](#오버레이-업데이트-및-숨기기)

## 오버레이 모드

`AccessibilityHighlightOverlay`는 `OverlayHighlightMode` 열거형으로 제어되는 두 가지 모드를 지원합니다:

- `OverlayHighlightMode::AUTO` - 오버레이의 위치와 크기가 장면 내 하이라이트된 액터의 부모를 기준으로 자동으로 계산됩니다. 기본 모드입니다.
- `OverlayHighlightMode::MANUAL` - 오버레이가 애플리케이션에서 설정한 커스텀 위치와 크기 값을 사용합니다.

현재 모드를 확인하거나 변경하려면:

```cpp
Dali::Ui::AccessibilityHighlightOverlay overlay;

// 현재 모드 가져오기
Dali::Ui::OverlayHighlightMode mode = overlay.GetOverlayMode();

// 커스텀 위치 지정을 위해 수동 모드로 설정
overlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::MANUAL);
```

## 커스텀 하이라이트 위치 지정

하이라이트 오버레이 위치를 정밀하게 제어해야 할 때, `SetCustomHighlight()`를 사용하여 커스텀 좌표를 지정합니다. 이 메서드를 호출하면 자동으로 모드가 `OverlayHighlightMode::MANUAL`로 전환됩니다.

```cpp
Dali::Ui::AccessibilityHighlightOverlay overlay;

// 위치 (100, 200)에 크기 (300, 150)으로 커스텀 하이라이트 설정
overlay.SetCustomHighlight(Vector2(100.0f, 200.0f), Vector2(300.0f, 150.0f));
```

자동 위치 지정으로 돌아가려면 `ResetCustomHighlight()`를 호출합니다:

```cpp
// 기본 위치와 크기로 자동 모드로 재설정
overlay.ResetCustomHighlight();
```

## 오버레이 업데이트 및 숨기기

하이라이트 상태가 변경되면 오버레이를 업데이트해야 합니다. 현재 하이라이트 액터를 `UpdateOverlay()`에 전달하여 시각적 표시기를 생성하거나 새로고침합니다:

```cpp
Dali::Ui::AccessibilityHighlightOverlay overlay;
Dali::Actor highlightActor;

// 하이라이트 액터에 대한 오버레이 생성 또는 업데이트
overlay.UpdateOverlay(highlightActor);
```

하이라이트를 제거해야 할 때, `HideOverlay()`를 호출하여 시각적 표시기를 숨깁니다:

```cpp
// 현재 표시된 오버레이 숨기기
overlay.HideOverlay();
```

일반적인 사용 패턴은 하이라이트 수명 주기를 따릅니다:

```cpp
// 접근 가능한 뷰를 하이라이트할 때
void OnGrabHighlight(Dali::Actor highlightActor)
{
  mHighlightOverlay.UpdateOverlay(highlightActor);
}

// 하이라이트를 해제할 때
void OnClearHighlight()
{
  mHighlightOverlay.HideOverlay();
}
```
