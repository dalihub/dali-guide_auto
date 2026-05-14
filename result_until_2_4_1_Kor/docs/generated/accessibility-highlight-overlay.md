---
title: 접근성 하이라이트 오버레이
sidebar_label: 접근성 하이라이트 오버레이
category: accessibility
---

# 접근성 하이라이트 오버레이 {#accessibility-highlight-overlay}

`Dali::Ui::AccessibilityHighlightOverlay`를 사용하면 dali-ui 앱에서 현재 하이라이트 액터에 표시되는 접근성 하이라이트 오버레이를 관리할 수 있습니다.

## 목차 {#table-of-contents}

- [오버레이 컨트롤러 생성](#create-an-overlay-controller)
- [자동 하이라이트 배치 사용](#use-automatic-highlight-placement)
- [사용자 지정 하이라이트 사각형 사용](#use-a-custom-highlight-rectangle)
- [오버레이 재설정 또는 숨기기](#reset-or-hide-the-overlay)
- [오버레이 모드 조회 및 보존](#query-and-preserve-overlay-mode)

## 오버레이 컨트롤러 생성 {#create-an-overlay-controller}

접근성 하이라이트 렌더링을 조정하는 dali-ui 앱 영역에 대해 `Dali::Ui::AccessibilityHighlightOverlay`를 하나 생성합니다. 이 객체는 현재 `Dali::Ui::OverlayHighlightMode`와 `SetCustomHighlight`를 통해 설정된 수동 사각형을 저장합니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

class AccessibilityOverlayController
{
public:
  AccessibilityOverlayController()
  : mHighlightOverlay()
  {
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mHighlightOverlay;
};
```

앱 UI는 `Dali::Ui::View` 객체를 중심으로 구성합니다. 오버레이 컨트롤러는 함께 사용하는 보조 객체입니다. 뷰를 대체하지 않으며, 애플리케이션 코드가 오버레이 액터를 직접 설정하도록 요구하지도 않습니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

class AccessiblePanel
{
public:
  AccessiblePanel()
  : mHighlightOverlay()
  {
  }

  Dali::Ui::AccessibilityHighlightOverlay& GetHighlightOverlay()
  {
    return mHighlightOverlay;
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mHighlightOverlay;
};
```

## 자동 하이라이트 배치 사용 {#use-automatic-highlight-placement}

활성 접근성 하이라이트가 변경되면 `UpdateOverlay`를 호출합니다. 자동 모드에서 `AccessibilityHighlightOverlay`는 전달된 하이라이트 액터의 부모 SceneView를 찾고, 하이라이트된 모델 경계를 기준으로 오버레이를 배치합니다.

`UpdateOverlay`는 활성 하이라이트를 `Dali::Actor&`로 받습니다. 이 통합 API가 접근성 계층에서 전달한 하이라이트 액터와 함께 동작하기 때문입니다. 애플리케이션 구조에서는 나머지 UI를 `Dali::Ui::View`로 모델링해 둡니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

class AccessibilityOverlayController
{
public:
  void OnActiveHighlightChanged(Dali::Actor& activeHighlight)
  {
    mHighlightOverlay.UpdateOverlay(activeHighlight);
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mHighlightOverlay;
};
```

다음 `UpdateOverlay`를 호출하기 전에 `ResetCustomHighlight`를 호출하면 컨트롤러를 명시적으로 자동 배치로 되돌릴 수 있습니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

void UseAutomaticHighlight(
  Dali::Ui::AccessibilityHighlightOverlay& highlightOverlay,
  Dali::Actor& activeHighlight)
{
  highlightOverlay.ResetCustomHighlight();
  highlightOverlay.UpdateOverlay(activeHighlight);
}
```

## 사용자 지정 하이라이트 사각형 사용 {#use-a-custom-highlight-rectangle}

시각적 하이라이트 사각형을 오버레이 안의 특정 위치와 크기로 배치해야 할 때 `SetCustomHighlight`를 사용합니다. 이 메서드는 `Dali::Vector2` 위치와 `Dali::Vector2` 크기를 받습니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

void HighlightCustomRegion(
  Dali::Ui::AccessibilityHighlightOverlay& highlightOverlay,
  Dali::Actor& activeHighlight)
{
  const Dali::Vector2 overlayPosition(24.0f, 32.0f);
  const Dali::Vector2 overlaySize(220.0f, 72.0f);

  highlightOverlay.SetCustomHighlight(overlayPosition, overlaySize);
  highlightOverlay.UpdateOverlay(activeHighlight);
}
```

`SetCustomHighlight`는 오버레이를 수동 배치 모드로도 전환합니다. 사용자 지정 사각형을 설정한 뒤에는 현재 활성 하이라이트로 `UpdateOverlay`를 호출하여 표시 중인 오버레이를 갱신합니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

class ManualAccessibilityHighlight
{
public:
  void Show(Dali::Actor& activeHighlight)
  {
    mHighlightOverlay.SetCustomHighlight(
      Dali::Vector2(8.0f, 8.0f),
      Dali::Vector2(180.0f, 56.0f));

    mHighlightOverlay.UpdateOverlay(activeHighlight);
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mHighlightOverlay;
};
```

## 오버레이 재설정 또는 숨기기 {#reset-or-hide-the-overlay}

수동 사각형을 더 이상 사용하지 않아야 할 때 `ResetCustomHighlight`를 호출합니다. 이 메서드는 저장된 수동 사각형을 지우고 모드를 자동 배치로 되돌립니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

void ReturnToAutomaticPlacement(
  Dali::Ui::AccessibilityHighlightOverlay& highlightOverlay,
  Dali::Actor& activeHighlight)
{
  highlightOverlay.ResetCustomHighlight();
  highlightOverlay.UpdateOverlay(activeHighlight);
}
```

접근성 포커스가 관련 뷰 영역을 벗어나는 경우처럼 현재 하이라이트를 더 이상 표시하지 않아야 할 때 `HideOverlay`를 호출합니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

class AccessibilityOverlayController
{
public:
  void ShowFor(Dali::Actor& activeHighlight)
  {
    mHighlightOverlay.UpdateOverlay(activeHighlight);
  }

  void Hide()
  {
    mHighlightOverlay.HideOverlay();
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mHighlightOverlay;
};
```

`HideOverlay`는 오버레이 하이라이트 액터가 있으면 이를 숨깁니다. 사용자 지정 사각형을 지우거나 오버레이 모드를 변경하지는 않습니다. 다음 하이라이트 업데이트에서 자동 배치로 돌아가야 한다면 `ResetCustomHighlight`도 함께 호출합니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

void ClearManualOverlayState(Dali::Ui::AccessibilityHighlightOverlay& highlightOverlay)
{
  highlightOverlay.HideOverlay();
  highlightOverlay.ResetCustomHighlight();
}
```

## 오버레이 모드 조회 및 보존 {#query-and-preserve-overlay-mode}

오버레이 업데이트 방식을 임시로 변경하기 전처럼 컨트롤러가 현재 배치 모드를 확인해야 할 때 `GetOverlayMode`를 사용합니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

Dali::Ui::OverlayHighlightMode ReadCurrentMode(
  const Dali::Ui::AccessibilityHighlightOverlay& highlightOverlay)
{
  return highlightOverlay.GetOverlayMode();
}
```

이전에 저장한 모드 값을 복원할 때는 `SetOverlayMode`를 사용합니다.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

class OverlayModeScope
{
public:
  explicit OverlayModeScope(Dali::Ui::AccessibilityHighlightOverlay& highlightOverlay)
  : mHighlightOverlay(highlightOverlay),
    mSavedMode(highlightOverlay.GetOverlayMode())
  {
  }

  ~OverlayModeScope()
  {
    mHighlightOverlay.SetOverlayMode(mSavedMode);
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay& mHighlightOverlay;
  Dali::Ui::OverlayHighlightMode mSavedMode;
};
```

일반적인 앱 흐름에서는 더 높은 수준의 상태 변경을 우선 사용합니다. 수동 배치에는 `SetCustomHighlight`를, 자동 배치에는 `ResetCustomHighlight`를 사용합니다. `SetOverlayMode`는 코드가 모드 값을 의도적으로 보존하거나 복원할 때만 사용합니다. 이 메서드는 이전 수동 위치나 크기를 복원하지 않습니다.
