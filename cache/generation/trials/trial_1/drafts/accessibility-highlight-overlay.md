---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: accessibility
---

# Accessibility Highlight Overlay

The Accessibility Highlight Overlay provides a visual focus indicator for accessibility highlighting in dali-ui applications. It supports both automatic position calculation and manual positioning modes for flexible highlight overlay control.

## Table of Contents

- [Overlay Modes](#overlay-modes)
- [Creating and Configuring the Overlay](#creating-and-configuring-the-overlay)
- [Manual Highlight Positioning](#manual-highlight-positioning)
- [Showing and Hiding the Overlay](#showing-and-hiding-the-overlay)

## Overlay Modes

The `AccessibilityHighlightOverlay` operates in two modes determined by the `OverlayHighlightMode` enum:

- `OverlayHighlightMode::AUTO` - The overlay position and size are automatically calculated based on the highlighted actor's screen extents.
- `OverlayHighlightMode::MANUAL` - The overlay uses custom position and size values set by the application.

By default, the overlay starts in `AUTO` mode. You can query the current mode using `GetOverlayMode()`:

```cpp
Dali::Ui::AccessibilityHighlightOverlay overlay;

if (overlay.GetOverlayMode() == Dali::Ui::OverlayHighlightMode::AUTO)
{
  // Automatic positioning is active
}
```

## Creating and Configuring the Overlay

Create an `AccessibilityHighlightOverlay` instance and configure its mode before use. The overlay integrates with the accessibility system to display a highlight border around the currently focused element.

```cpp
// Create the highlight overlay
Dali::Ui::AccessibilityHighlightOverlay highlightOverlay;

// Set to automatic mode (default)
highlightOverlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::AUTO);
```

To switch between modes at runtime:

```cpp
// Switch to manual mode
highlightOverlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::MANUAL);

// Switch back to automatic mode
highlightOverlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::AUTO);
```

## Manual Highlight Positioning

When you need precise control over the highlight position and size, use `SetCustomHighlight()` to specify custom coordinates. This method automatically switches the overlay to `MANUAL` mode.

```cpp
Dali::Ui::AccessibilityHighlightOverlay overlay;

// Set a custom highlight at position (100, 150) with size (200, 80)
Dali::Vector2 position(100.0f, 150.0f);
Dali::Vector2 size(200.0f, 80.0f);
overlay.SetCustomHighlight(position, size);
```

To return to automatic positioning after using a custom highlight, call `ResetCustomHighlight()`:

```cpp
// Reset to automatic mode
overlay.ResetCustomHighlight();
```

The `ResetCustomHighlight()` method clears the manual position and size values and switches the mode back to `AUTO`.

## Showing and Hiding the Overlay

The `UpdateOverlay()` method updates or creates the highlight overlay for a specified actor. Pass the currently highlighted actor to this method.

```cpp
Dali::Ui::AccessibilityHighlightOverlay overlay;
Dali::Actor highlightActor; // The actor receiving accessibility focus

// Update or create the overlay for the highlight actor
overlay.UpdateOverlay(highlightActor);
```

When the highlight should no longer be visible, call `HideOverlay()`:

```cpp
// Hide the currently displayed highlight
overlay.HideOverlay();
```

A typical workflow for managing the highlight overlay during accessibility focus changes:

```cpp
void OnAccessibilityFocusGained(Dali::Actor focusedActor)
{
  mHighlightOverlay.UpdateOverlay(focusedActor);
}

void OnAccessibilityFocusLost()
{
  mHighlightOverlay.HideOverlay();
}
```

When using `ViewAccessible` for accessibility support, the highlight overlay is managed internally. `ViewAccessible` provides `SetCustomHighlightOverlay()` and `ResetCustomHighlightOverlay()` methods that delegate to the internal `AccessibilityHighlightOverlay` instance.
