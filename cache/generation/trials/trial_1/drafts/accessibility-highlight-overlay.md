---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: accessibility
---

# Accessibility Highlight Overlay

`Dali::Ui::AccessibilityHighlightOverlay` controls the visible accessibility highlight for a dali-ui view flow.

## Table of Contents

- [Overlay Role in a dali-ui App](#overlay-role-in-a-dali-ui-app)
- [Choosing Automatic or Manual Highlight Bounds](#choosing-automatic-or-manual-highlight-bounds)
- [Updating the Overlay from the Active Highlight Target](#updating-the-overlay-from-the-active-highlight-target)
- [Hiding and Resetting the Overlay](#hiding-and-resetting-the-overlay)

## Overlay Role in a dali-ui App

Use `Dali::Ui::AccessibilityHighlightOverlay` as the object that owns accessibility highlight overlay state. Application UI should remain organized around `Dali::Ui::View`; the overlay is updated by the accessibility bridge or controller when it receives the currently active highlight target.

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

  Dali::Ui::OverlayHighlightMode CurrentMode() const
  {
    return mHighlightOverlay.GetOverlayMode();
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mHighlightOverlay;
};
```

`Dali::Ui::AccessibilityHighlightOverlay::GetOverlayMode` returns the current `Dali::Ui::OverlayHighlightMode`, so app-side logic can keep overlay handling consistent with the current accessibility flow.

## Choosing Automatic or Manual Highlight Bounds

`Dali::Ui::AccessibilityHighlightOverlay` supports two overlay modes:

- `Dali::Ui::OverlayHighlightMode::AUTO` uses the active highlight target when `Dali::Ui::AccessibilityHighlightOverlay::UpdateOverlay` is called.
- `Dali::Ui::OverlayHighlightMode::MANUAL` uses bounds supplied through `Dali::Ui::AccessibilityHighlightOverlay::SetCustomHighlight`.

Use `Dali::Ui::AccessibilityHighlightOverlay::SetOverlayMode` when you only need to switch the mode. Use `Dali::Ui::AccessibilityHighlightOverlay::SetCustomHighlight` when you already know the rectangle to show.

```cpp
void UseAutomaticHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  overlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::AUTO);
}

void UseManualHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay,
                        const Dali::Vector2& position,
                        const Dali::Vector2& size)
{
  overlay.SetCustomHighlight(position, size);
}
```

`Dali::Ui::AccessibilityHighlightOverlay::SetCustomHighlight` stores the supplied `Dali::Vector2` position and size and changes the overlay mode to `Dali::Ui::OverlayHighlightMode::MANUAL`.

```cpp
bool IsManualHighlightActive(const Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  return overlay.GetOverlayMode() == Dali::Ui::OverlayHighlightMode::MANUAL;
}
```

## Updating the Overlay from the Active Highlight Target

Call `Dali::Ui::AccessibilityHighlightOverlay::UpdateOverlay` when the accessibility focus target changes. The method takes the active highlight object as `Dali::Actor&`; keep that raw actor handling inside the accessibility integration layer, while the rest of the application continues to model UI with `Dali::Ui::View`.

```cpp
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

In automatic mode, `Dali::Ui::AccessibilityHighlightOverlay::UpdateOverlay` updates the currently active highlight overlay and creates an overlay when one does not already exist.

Manual bounds can also be applied before updating from the current highlight target.

```cpp
void ShowCustomHighlightForTarget(Dali::Ui::AccessibilityHighlightOverlay& overlay,
                                  Dali::Actor& activeHighlight,
                                  const Dali::Vector2& position,
                                  const Dali::Vector2& size)
{
  overlay.SetCustomHighlight(position, size);
  overlay.UpdateOverlay(activeHighlight);
}
```

## Hiding and Resetting the Overlay

Use `Dali::Ui::AccessibilityHighlightOverlay::HideOverlay` when the current accessibility highlight should no longer be visible.

```cpp
void ClearVisibleHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  overlay.HideOverlay();
}
```

Use `Dali::Ui::AccessibilityHighlightOverlay::ResetCustomHighlight` to clear manual bounds and return the overlay mode to `Dali::Ui::OverlayHighlightMode::AUTO`.

```cpp
void ReturnToAutomaticHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay,
                                Dali::Actor& nextActiveHighlight)
{
  overlay.ResetCustomHighlight();

  if(overlay.GetOverlayMode() == Dali::Ui::OverlayHighlightMode::AUTO)
  {
    overlay.UpdateOverlay(nextActiveHighlight);
  }
}
```

A typical manual highlight session ends by hiding the visible overlay and clearing the custom rectangle.

```cpp
void EndManualHighlightSession(Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  overlay.HideOverlay();
  overlay.ResetCustomHighlight();
}
```
