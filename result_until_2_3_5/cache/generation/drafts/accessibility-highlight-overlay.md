---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: accessibility
---

# Accessibility Highlight Overlay

`Dali::Ui::AccessibilityHighlightOverlay` lets a dali-ui application show, move, customize, and hide the accessibility focus highlight overlay.

## Table of Contents

- [Create and Configure the Overlay](#create-and-configure-the-overlay)
- [Show a Highlight for the Active Accessible View](#show-a-highlight-for-the-active-accessible-view)
- [Use a Custom Highlight Rectangle](#use-a-custom-highlight-rectangle)
- [Hide and Reset the Overlay](#hide-and-reset-the-overlay)

## Create and Configure the Overlay

Use `Dali::Ui::AccessibilityHighlightOverlay` as the application-side overlay controller. The overlay mode is configured with `SetOverlayMode`, and the current mode can be read back with `GetOverlayMode`.

```cpp
#include <dali-ui/public-api/accessibility-highlight-overlay.h>

class AccessibilityOverlayController
{
public:
  AccessibilityOverlayController()
  : mOverlay()
  {
  }

  void SetMode(OverlayHighlightMode mode)
  {
    mOverlay.SetOverlayMode(mode);
  }

  OverlayHighlightMode GetMode() const
  {
    return mOverlay.GetOverlayMode();
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mOverlay;
};
```

`SetOverlayMode` is the typed way to configure how the overlay is drawn. Keep the selected `OverlayHighlightMode` in your application state when the mode is user-configurable, and pass it to `SetOverlayMode` whenever the highlight policy changes.

```cpp
void ApplyAccessibilityHighlightMode(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  OverlayHighlightMode selectedMode)
{
  if(overlay.GetOverlayMode() != selectedMode)
  {
    overlay.SetOverlayMode(selectedMode);
  }
}
```

## Show a Highlight for the Active Accessible View

In a dali-ui application, the accessibility focus target is normally represented in your app model as a `Dali::Ui::View`. When your focus handling code has the active highlight object available, pass it to `UpdateOverlay`.

`UpdateOverlay` takes the active highlight object by reference and refreshes the overlay for that object.

```cpp
void OnAccessibleViewFocused(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  Dali::Actor& activeHighlight)
{
  overlay.UpdateOverlay(activeHighlight);
}
```

A small controller can keep overlay updates close to the focus-change path. The application continues to organize UI with `Dali::Ui::View`, while the overlay update receives the active highlight object required by `UpdateOverlay`.

```cpp
class AccessibleFocusPresenter
{
public:
  void SetHighlightMode(OverlayHighlightMode mode)
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

Use `SetCustomHighlight` when the visual highlight should use an explicit rectangle instead of the current active object bounds. The rectangle is described by a `Vector2` position and a `Vector2` size.

```cpp
void ShowCustomAccessibilityHighlight(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  Vector2 position,
  Vector2 size)
{
  overlay.SetCustomHighlight(position, size);
}
```

This is useful when the accessible interaction area is different from the visible bounds of the `Dali::Ui::View`, such as when a composite control exposes a larger touch target.

```cpp
class CompositeControlAccessibility
{
public:
  void HighlightTouchTarget(Vector2 topLeft, Vector2 touchSize)
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

After a custom rectangle is no longer needed, call `ResetCustomHighlight` before returning to object-based overlay updates.

## Hide and Reset the Overlay

Call `HideOverlay` when there is no active accessible target, when the current screen is being dismissed, or when your app temporarily disables accessibility focus presentation.

```cpp
void OnAccessibilityFocusCleared(Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  overlay.HideOverlay();
}
```

If your app used `SetCustomHighlight`, call `ResetCustomHighlight` to clear the custom rectangle. You can then call `UpdateOverlay` again for the active object.

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
