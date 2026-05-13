---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: uncategorized
---

# Accessibility Highlight Overlay

The Accessibility Highlight Overlay provides a visual focus indicator for accessibility highlighting in dali-ui applications. It supports both automatic position calculation and manual positioning modes.

## Table of Contents

- [Overlay Modes](#overlay-modes)
- [Creating and Configuring the Overlay](#creating-and-configuring-the-overlay)
- [Updating and Hiding the Overlay](#updating-and-hiding-the-overlay)
- [Custom Highlight Positioning](#custom-highlight-positioning)

## Overlay Modes

The `Dali::Ui::AccessibilityHighlightOverlay` operates in two modes defined by the `Dali::Ui::OverlayHighlightMode` enum:

- `OverlayHighlightMode::AUTO` - The overlay position and size are calculated automatically based on the highlight actor's screen extents.
- `OverlayHighlightMode::MANUAL` - The overlay uses custom position and size values set by the application.

By default, the overlay is created in `AUTO` mode.

## Creating and Configuring the Overlay

Create an instance of `Dali::Ui::AccessibilityHighlightOverlay` and configure its mode before use:

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

// Create the overlay instance
Dali::Ui::AccessibilityHighlightOverlay highlightOverlay;

// Set to automatic mode (default)
highlightOverlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::AUTO);

// Or set to manual mode
highlightOverlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::MANUAL);
```

To check the current mode:

```cpp
Dali::Ui::OverlayHighlightMode currentMode = highlightOverlay.GetOverlayMode();
if (currentMode == Dali::Ui::OverlayHighlightMode::AUTO)
{
  // Operating in automatic mode
}
```

## Updating and Hiding the Overlay

Call `UpdateOverlay()` to display or refresh the highlight overlay for a specific actor. The overlay finds the parent scene view and positions the highlight accordingly:

```cpp
// Update the overlay for the currently focused actor
Dali::Actor activeHighlight = GetFocusedActor(); // Your focus management logic
highlightOverlay.UpdateOverlay(activeHighlight);
```

When the highlight should be removed, call `HideOverlay()`:

```cpp
// Hide the visible highlight overlay
highlightOverlay.HideOverlay();
```

The `UpdateOverlay()` method creates the overlay actor hierarchy if it does not exist, then updates the highlight position based on the current mode.

## Custom Highlight Positioning

For scenarios where automatic position calculation is not suitable, use `SetCustomHighlight()` to define a fixed position and size. This method also switches the overlay to `MANUAL` mode:

```cpp
// Set a custom highlight area at position (100, 150) with size (200, 80)
highlightOverlay.SetCustomHighlight(
  Dali::Vector2(100.0f, 150.0f),  // position
  Dali::Vector2(200.0f, 80.0f)    // size
);

// The overlay is now in MANUAL mode
```

To return to automatic mode and clear the custom values, call `ResetCustomHighlight()`:

```cpp
// Reset to automatic mode
highlightOverlay.ResetCustomHighlight();

// Now operating in AUTO mode with zeroed position/size
```

A typical workflow for custom highlighting:

```cpp
// 1. Set custom highlight area
highlightOverlay.SetCustomHighlight(
  Dali::Vector2(50.0f, 100.0f),
  Dali::Vector2(300.0f, 120.0f)
);

// 2. Update the overlay to display
Dali::Actor highlightActor = GetHighlightActor();
highlightOverlay.UpdateOverlay(highlightActor);

// 3. When done, hide and reset
highlightOverlay.HideOverlay();
highlightOverlay.ResetCustomHighlight();
