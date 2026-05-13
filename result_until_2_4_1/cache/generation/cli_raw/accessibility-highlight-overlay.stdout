---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: accessibility
---

# Accessibility Highlight Overlay

`Dali::Ui::AccessibilityHighlightOverlay` lets a dali-ui app manage a visible accessibility highlight overlay for the currently active highlight target.

## Table of Contents

- [Create an Overlay Controller](#create-an-overlay-controller)
- [Use Automatic Highlight Placement](#use-automatic-highlight-placement)
- [Use a Custom Highlight Rectangle](#use-a-custom-highlight-rectangle)
- [Reset or Hide the Overlay](#reset-or-hide-the-overlay)
- [Query and Preserve Overlay Mode](#query-and-preserve-overlay-mode)

## Create an Overlay Controller

Create one `Dali::Ui::AccessibilityHighlightOverlay` for the part of your dali-ui app that coordinates accessibility highlight rendering. The object stores the current overlay mode and any custom rectangle configured through `SetCustomHighlight`.

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

Keep your app UI structured around `Dali::Ui::View` objects. The overlay controller is a companion object: it does not replace your views, and it does not require application code to configure view properties directly.

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

## Use Automatic Highlight Placement

Call `UpdateOverlay` when the active accessibility highlight target changes. In automatic mode, `AccessibilityHighlightOverlay` calculates the overlay position from the supplied active highlight object.

`UpdateOverlay` takes the active highlight as `Dali::Actor&` because this integration API works with the highlight object passed by the accessibility layer. In application structure, keep the rest of the UI modeled as `Dali::Ui::View`.

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

You can explicitly return the controller to automatic placement by calling `ResetCustomHighlight` before the next `UpdateOverlay`.

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

## Use a Custom Highlight Rectangle

Use `SetCustomHighlight` when the visual highlight rectangle should be placed at a specific position and size. The method accepts a `Dali::Vector2` position and a `Dali::Vector2` size.

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

`SetCustomHighlight` also switches the overlay into manual placement mode. After setting the custom rectangle, call `UpdateOverlay` with the current active highlight so the visible overlay is refreshed.

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

## Reset or Hide the Overlay

Call `ResetCustomHighlight` when a manual rectangle should no longer be used. The next `UpdateOverlay` uses automatic placement again.

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

Call `HideOverlay` when the current highlight should no longer be displayed, such as when the accessibility focus leaves the relevant view area.

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

`HideOverlay` only hides the displayed overlay. It does not clear a custom rectangle. If you want the next highlight update to return to automatic placement, call `ResetCustomHighlight` as well.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

void ClearManualOverlayState(Dali::Ui::AccessibilityHighlightOverlay& highlightOverlay)
{
  highlightOverlay.HideOverlay();
  highlightOverlay.ResetCustomHighlight();
}
```

## Query and Preserve Overlay Mode

Use `GetOverlayMode` when your controller needs to inspect the current placement mode, for example before temporarily hiding the overlay.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

Dali::Ui::OverlayHighlightMode ReadCurrentMode(
  const Dali::Ui::AccessibilityHighlightOverlay& highlightOverlay)
{
  return highlightOverlay.GetOverlayMode();
}
```

Use `SetOverlayMode` when restoring a previously saved mode.

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

For normal app flows, prefer the higher-level state changes: `SetCustomHighlight` for manual placement and `ResetCustomHighlight` for automatic placement. Use `SetOverlayMode` when your code is intentionally preserving or restoring the mode value.
