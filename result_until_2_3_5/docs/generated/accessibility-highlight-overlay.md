---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: accessibility
---

# Accessibility Highlight Overlay

`Dali::Ui::AccessibilityHighlightOverlay` lets a dali-ui application update, customize, and hide the visual overlay used for an accessibility highlight actor.

## Table of Contents

- [Create and Configure the Overlay](#create-and-configure-the-overlay)
- [Show a Highlight for the Active Accessible View](#show-a-highlight-for-the-active-accessible-view)
- [Use a Custom Highlight Rectangle](#use-a-custom-highlight-rectangle)
- [Hide and Reset the Overlay](#hide-and-reset-the-overlay)

## Create and Configure the Overlay

Use `Dali::Ui::AccessibilityHighlightOverlay` as the overlay controller for accessibility highlight presentation. The overlay mode is configured with `SetOverlayMode`, and the current mode can be read back with `GetOverlayMode`.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

class AccessibilityOverlayController
{
public:
  AccessibilityOverlayController()
  : mOverlay()
  {
  }

  void SetMode(Dali::Ui::OverlayHighlightMode mode)
  {
    mOverlay.SetOverlayMode(mode);
  }

  Dali::Ui::OverlayHighlightMode GetMode() const
  {
    return mOverlay.GetOverlayMode();
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mOverlay;
};
```

`SetOverlayMode` directly sets the `OverlayHighlightMode` used by later overlay updates. Keep the selected `OverlayHighlightMode` in your application state when the mode is user-configurable, and pass it to `SetOverlayMode` whenever the highlight policy changes.

```cpp
void ApplyAccessibilityHighlightMode(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  Dali::Ui::OverlayHighlightMode selectedMode)
{
  if(overlay.GetOverlayMode() != selectedMode)
  {
    overlay.SetOverlayMode(selectedMode);
  }
}
```

## Show a Highlight for the Active Accessible View

In a dali-ui application, the accessibility focus target is normally a `Dali::Ui::View`, while `UpdateOverlay` receives the active highlight actor associated with that focus target.

`UpdateOverlay` takes the active highlight actor by reference and updates the overlay for that actor. If no overlay actor exists yet, the implementation creates one for the parent Scene3D view; if no Scene3D parent is found, the passed highlight actor remains visible and the overlay is not updated.

```cpp
void OnAccessibleViewFocused(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  Dali::Actor& activeHighlight)
{
  overlay.UpdateOverlay(activeHighlight);
}
```

A small controller can keep overlay updates close to the focus-change path. The application continues to organize UI with `Dali::Ui::View`, while the overlay update receives the active highlight actor required by `UpdateOverlay`.

```cpp
class AccessibleFocusPresenter
{
public:
  void SetHighlightMode(Dali::Ui::OverlayHighlightMode mode)
  {
    mOverlay.SetOverlayMode(mode);
  }

  void PresentActiveHighlight(Dali::Actor& activeHighlight)
  {
    mOverlay.UpdateOverlay(activeHighlight);
  }

  void ClearActiveHighlight()
  {
    mOverlay.HideOverlay();
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mOverlay;
};
```

## Use a Custom Highlight Rectangle

Use `SetCustomHighlight` when the overlay should use an explicit rectangle instead of the automatic bounds calculated from the active highlight actor. The rectangle is described by a `Dali::Vector2` position and a `Dali::Vector2` size, and `SetCustomHighlight` switches the overlay mode to `Dali::Ui::OverlayHighlightMode::MANUAL`.

```cpp
void ShowCustomAccessibilityHighlight(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  Dali::Vector2 position,
  Dali::Vector2 size)
{
  overlay.SetCustomHighlight(position, size);
}
```

This is useful when the accessible interaction area is different from the bounds that the automatic overlay would calculate, such as when a composite control exposes a larger touch target.

```cpp
class CompositeControlAccessibility
{
public:
  void HighlightTouchTarget(Dali::Vector2 topLeft, Dali::Vector2 touchSize)
  {
    mOverlay.SetCustomHighlight(topLeft, touchSize);
  }

  void RestoreObjectHighlight(Dali::Actor& activeHighlight)
  {
    mOverlay.ResetCustomHighlight();
    mOverlay.UpdateOverlay(activeHighlight);
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mOverlay;
};
```

After a custom rectangle is no longer needed, call `ResetCustomHighlight` before returning to automatic overlay updates.

## Hide and Reset the Overlay

Call `HideOverlay` when there is no active accessible target, when the current screen is being dismissed, or when your app temporarily disables accessibility focus presentation.

```cpp
void OnAccessibilityFocusCleared(Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  overlay.HideOverlay();
}
```

If your app used `SetCustomHighlight`, call `ResetCustomHighlight` to clear the manual rectangle and return the mode to `Dali::Ui::OverlayHighlightMode::AUTO`. You can then call `UpdateOverlay` again for the active highlight actor.

```cpp
void ReturnToDefaultHighlight(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  Dali::Actor& activeHighlight)
{
  overlay.ResetCustomHighlight();
  overlay.UpdateOverlay(activeHighlight);
}
```

For screen transitions, hide the overlay first so stale highlight visuals are not left visible while your `Dali::Ui::View` tree is changing.

```cpp
class ScreenAccessibilityState
{
public:
  void OnLeavingScreen()
  {
    mOverlay.HideOverlay();
    mOverlay.ResetCustomHighlight();
  }

  void OnEnteringScreen(Dali::Actor& initialHighlight)
  {
    mOverlay.UpdateOverlay(initialHighlight);
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mOverlay;
};
```
