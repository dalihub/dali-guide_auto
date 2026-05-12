---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: accessibility
---

# Accessibility Highlight Overlay

`Dali::Ui::AccessibilityHighlightOverlay` lets a dali-ui app display or hide the accessibility focus highlight overlay, either from the active highlight target or from an explicit rectangle.

## Table of Contents

- [Overlay Object and Default Mode](#overlay-object-and-default-mode)
- [Automatic Highlight Updates](#automatic-highlight-updates)
- [Manual Highlight Rectangles](#manual-highlight-rectangles)
- [Hiding and Resetting the Overlay](#hiding-and-resetting-the-overlay)

## Overlay Object and Default Mode

Create `Dali::Ui::AccessibilityHighlightOverlay` as the object that owns overlay state for the current accessibility highlight flow. In a dali-ui app, the app-facing UI should remain organized as `Dali::Ui::View` objects; the overlay API is used when the accessibility integration has an active highlight target to update.

A newly constructed `Dali::Ui::AccessibilityHighlightOverlay` starts in `Dali::Ui::OverlayHighlightMode::AUTO`.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

class AccessibilityHighlightController
{
public:
  AccessibilityHighlightController()
  : mHighlightOverlay()
  {
  }

  bool IsUsingAutomaticHighlight() const
  {
    return mHighlightOverlay.GetOverlayMode() == Dali::Ui::OverlayHighlightMode::AUTO;
  }

  void UseAutomaticHighlight()
  {
    mHighlightOverlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::AUTO);
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mHighlightOverlay;
};
```

Use `Dali::Ui::AccessibilityHighlightOverlay::GetOverlayMode` when local logic needs to branch on the current mode. Use `Dali::Ui::AccessibilityHighlightOverlay::SetOverlayMode` to switch directly between `Dali::Ui::OverlayHighlightMode::AUTO` and `Dali::Ui::OverlayHighlightMode::MANUAL`.

```cpp
void ConfigureHighlightMode(Dali::Ui::AccessibilityHighlightOverlay& overlay, bool customBounds)
{
  overlay.SetOverlayMode(customBounds ? Dali::Ui::OverlayHighlightMode::MANUAL
                                      : Dali::Ui::OverlayHighlightMode::AUTO);
}
```

## Automatic Highlight Updates

Use `Dali::Ui::AccessibilityHighlightOverlay::UpdateOverlay` when the accessibility focus target changes and the overlay should track the active highlight target. The method takes the active highlight object as `Dali::Actor&`; application code should still treat the dali-ui `Dali::Ui::View` tree as the app-facing model and keep direct actor handling inside the accessibility bridge or controller that receives the active highlight handle.

```cpp
#include <dali/public-api/actors/actor.h>
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

class AccessibilityHighlightController
{
public:
  void OnAccessibilityHighlightChanged(Dali::Actor& activeHighlight)
  {
    mHighlightOverlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::AUTO);
    mHighlightOverlay.UpdateOverlay(activeHighlight);
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mHighlightOverlay;
};
```

In automatic mode, `Dali::Ui::AccessibilityHighlightOverlay` calculates the overlay rectangle from the active highlight target during `UpdateOverlay`. Keep `UpdateOverlay` calls tied to actual focus or highlight changes rather than calling it as a general frame update.

```cpp
void RefreshAutomaticOverlay(Dali::Ui::AccessibilityHighlightOverlay& overlay,
                             Dali::Actor& activeHighlight)
{
  if(overlay.GetOverlayMode() != Dali::Ui::OverlayHighlightMode::AUTO)
  {
    overlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::AUTO);
  }

  overlay.UpdateOverlay(activeHighlight);
}
```

## Manual Highlight Rectangles

Use `Dali::Ui::AccessibilityHighlightOverlay::SetCustomHighlight` when the app or accessibility layer already knows the exact overlay rectangle. The position and size are passed as `Dali::Vector2` values. Calling `SetCustomHighlight` also switches the overlay to `Dali::Ui::OverlayHighlightMode::MANUAL`.

```cpp
#include <dali/public-api/math/vector2.h>
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

void ShowManualHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay,
                         Dali::Actor& activeHighlight)
{
  const Dali::Vector2 overlayPosition(24.0f, 32.0f);
  const Dali::Vector2 overlaySize(180.0f, 64.0f);

  overlay.SetCustomHighlight(overlayPosition, overlaySize);
  overlay.UpdateOverlay(activeHighlight);
}
```

Manual mode is useful when the visual highlight should cover a logical region rather than the active highlight target's calculated bounds.

```cpp
void MoveManualHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay,
                         Dali::Actor& activeHighlight,
                         const Dali::Vector2& position,
                         const Dali::Vector2& size)
{
  overlay.SetCustomHighlight(position, size);

  if(overlay.GetOverlayMode() == Dali::Ui::OverlayHighlightMode::MANUAL)
  {
    overlay.UpdateOverlay(activeHighlight);
  }
}
```

## Hiding and Resetting the Overlay

Use `Dali::Ui::AccessibilityHighlightOverlay::HideOverlay` when the current accessibility highlight should no longer be visible, for example when focus leaves the relevant content area.

```cpp
void ClearVisibleHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  overlay.HideOverlay();
}
```

Use `Dali::Ui::AccessibilityHighlightOverlay::ResetCustomHighlight` to clear the manual rectangle and return the overlay to automatic mode.

```cpp
void ReturnToAutomaticHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay,
                                Dali::Actor& activeHighlight)
{
  overlay.ResetCustomHighlight();

  if(overlay.GetOverlayMode() == Dali::Ui::OverlayHighlightMode::AUTO)
  {
    overlay.UpdateOverlay(activeHighlight);
  }
}
```

A common flow is to hide the current overlay, reset any custom rectangle, and then let the next focus update call `Dali::Ui::AccessibilityHighlightOverlay::UpdateOverlay` in automatic mode.

```cpp
void EndManualHighlightSession(Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  overlay.HideOverlay();
  overlay.ResetCustomHighlight();
}
```
