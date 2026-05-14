---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: accessibility
---

# Accessibility Highlight Overlay

`Dali::Ui::AccessibilityHighlightOverlay` controls the visual highlight rectangle used to show the current accessibility focus in a dali-ui application.

## Table of Contents

- [Create and Configure the Overlay](#create-and-configure-the-overlay)
- [Use a Custom Highlight Rectangle](#use-a-custom-highlight-rectangle)
- [Update and Hide the Highlight](#update-and-hide-the-highlight)
- [Restore Default Highlight Behavior](#restore-default-highlight-behavior)

## Create and Configure the Overlay

Create `Dali::Ui::AccessibilityHighlightOverlay` as the application-level object that owns highlight overlay behavior. The overlay mode is configured with `SetOverlayMode()`, and the current mode can be read back with `GetOverlayMode()`.

When your application stores the preferred mode elsewhere, pass that mode into the overlay rather than hard-coding accessibility behavior in individual views.

```cpp
void ApplyAccessibilityHighlightMode(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  decltype(overlay.GetOverlayMode()) mode)
{
  overlay.SetOverlayMode(mode);
}

void PreserveCurrentAccessibilityHighlightMode(
  Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  auto currentMode = overlay.GetOverlayMode();
  overlay.SetOverlayMode(currentMode);
}
```

For simple ownership, construct the overlay with `AccessibilityHighlightOverlay()` and keep it near the dali-ui view coordination code that handles accessibility focus changes.

```cpp
class AccessibilityHighlightController
{
public:
  AccessibilityHighlightController() = default;

  void SetMode(decltype(mOverlay.GetOverlayMode()) mode)
  {
    mOverlay.SetOverlayMode(mode);
  }

  decltype(mOverlay.GetOverlayMode()) GetMode() const
  {
    return mOverlay.GetOverlayMode();
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mOverlay;
};
```

## Use a Custom Highlight Rectangle

Use `SetCustomHighlight()` when the visible highlight should be drawn around a specific rectangle instead of the overlay's default target bounds. The rectangle is described by a position and size.

This is useful when a dali-ui view presents a composite control and the accessible focus should cover only the meaningful interactive region.

```cpp
void HighlightRegion(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  Dali::Vector2 position,
  Dali::Vector2 size)
{
  overlay.SetCustomHighlight(position, size);
}
```

A controller can keep this operation close to the app-facing dali-ui view logic, while still letting `Dali::Ui::AccessibilityHighlightOverlay` own the overlay state.

```cpp
class FocusHighlightPresenter
{
public:
  void ShowCustomFocusBounds(Dali::Vector2 topLeft, Dali::Vector2 dimensions)
  {
    mOverlay.SetCustomHighlight(topLeft, dimensions);
  }

  void UseConfiguredMode(decltype(mOverlay.GetOverlayMode()) mode)
  {
    mOverlay.SetOverlayMode(mode);
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mOverlay;
};
```

## Update and Hide the Highlight

`UpdateOverlay()` updates the overlay from the current accessibility highlight target. In a dali-ui application, treat this as the bridge point from your focused `Dali::Ui::View` coordination code into the overlay update path. The public signature accepts the active highlight target by reference.

Use `HideOverlay()` when the app should remove the accessibility highlight, such as when focus leaves the active view area or when the accessibility focus state is cleared.

```cpp
void RefreshAccessibilityHighlight(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  Dali::Actor& activeHighlight)
{
  overlay.UpdateOverlay(activeHighlight);
}

void ClearAccessibilityHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  overlay.HideOverlay();
}
```

A small wrapper keeps the update and hide decisions centralized.

```cpp
class AccessibilityFocusOverlay
{
public:
  void Update(Dali::Actor& activeHighlight)
  {
    mOverlay.UpdateOverlay(activeHighlight);
  }

  void Hide()
  {
    mOverlay.HideOverlay();
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay mOverlay;
};
```

## Restore Default Highlight Behavior

After using a custom rectangle, call `ResetCustomHighlight()` to return the overlay to its normal highlight calculation. This keeps temporary custom focus visuals from leaking into later accessibility focus changes.

```cpp
void ShowTemporaryCustomHighlight(
  Dali::Ui::AccessibilityHighlightOverlay& overlay,
  Dali::Vector2 position,
  Dali::Vector2 size)
{
  overlay.SetCustomHighlight(position, size);
}

void ReturnToDefaultHighlight(Dali::Ui::AccessibilityHighlightOverlay& overlay)
{
  overlay.ResetCustomHighlight();
}
```

A typical interaction flow is to set a custom rectangle for a specialized view state, then reset it when the view returns to ordinary accessibility focus behavior.

```cpp
class CustomHighlightScope
{
public:
  explicit CustomHighlightScope(Dali::Ui::AccessibilityHighlightOverlay& overlay)
  : mOverlay(overlay)
  {
  }

  void Enter(Dali::Vector2 position, Dali::Vector2 size)
  {
    mOverlay.SetCustomHighlight(position, size);
  }

  void Leave()
  {
    mOverlay.ResetCustomHighlight();
    mOverlay.HideOverlay();
  }

private:
  Dali::Ui::AccessibilityHighlightOverlay& mOverlay;
};
```