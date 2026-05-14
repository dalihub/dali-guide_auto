---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: accessibility
---
# Accessibility Highlight Overlay

The Accessibility Highlight Overlay provides a visual highlight indicator for accessible views, supporting both automatic and manual positioning modes for screen readers and accessibility services.

## Table of Contents

- [Overlay Modes](#overlay-modes)
- [Custom Highlight Positioning](#custom-highlight-positioning)
- [Updating and Hiding the Overlay](#updating-and-hiding-the-overlay)

## Overlay Modes

The `AccessibilityHighlightOverlay` supports two modes controlled by the `OverlayHighlightMode` enum:

- `OverlayHighlightMode::AUTO` - The overlay position and size are calculated automatically based on the highlighted actor's parent within the scene. This is the default mode.
- `OverlayHighlightMode::MANUAL` - The overlay uses custom position and size values set by the application.

To check or change the current mode:

```cpp
Dali::Ui::AccessibilityHighlightOverlay overlay;

// Get the current mode
Dali::Ui::OverlayHighlightMode mode = overlay.GetOverlayMode();

// Set to manual mode for custom positioning
overlay.SetOverlayMode(Dali::Ui::OverlayHighlightMode::MANUAL);
```

## Custom Highlight Positioning

When you need precise control over the highlight overlay position, use `SetCustomHighlight()` to specify custom coordinates. Calling this method automatically switches the mode to `OverlayHighlightMode::MANUAL`.

```cpp
Dali::Ui::AccessibilityHighlightOverlay overlay;

// Set a custom highlight at position (100, 200) with size (300, 150)
overlay.SetCustomHighlight(Vector2(100.0f, 200.0f), Vector2(300.0f, 150.0f));
```

To return to automatic positioning, call `ResetCustomHighlight()`:

```cpp
// Reset to automatic mode with default position and size
overlay.ResetCustomHighlight();
```

## Updating and Hiding the Overlay

The overlay must be updated when the highlight state changes. Pass the current highlight actor to `UpdateOverlay()` to create or refresh the visual indicator:

```cpp
Dali::Ui::AccessibilityHighlightOverlay overlay;
Dali::Actor highlightActor;

// Create or update the overlay for the highlight actor
overlay.UpdateOverlay(highlightActor);
```

When the highlight should be removed, call `HideOverlay()` to hide the visual indicator:

```cpp
// Hide the currently displayed overlay
overlay.HideOverlay();
```

The typical usage pattern follows the highlight lifecycle:

```cpp
// When highlighting an accessible view
void OnGrabHighlight(Dali::Actor highlightActor)
{
  mHighlightOverlay.UpdateOverlay(highlightActor);
}

// When clearing the highlight
void OnClearHighlight()
{
  mHighlightOverlay.HideOverlay();
}
```
