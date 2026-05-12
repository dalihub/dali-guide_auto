---
title: Accessibility Highlight Overlay
sidebar_label: Accessibility Highlight Overlay
category: uncategorized
---

# Accessibility Highlight Overlay

The Accessibility Highlight Overlay provides a visual highlight indicator for accessibility focus in dali-ui applications. It supports both automatic positioning based on the highlighted actor's screen extents and manual positioning for custom overlay placement.

## Table of Contents

- [Overlay Modes](#overlay-modes)
- [Creating and Configuring the Overlay](#creating-and-configuring-the-overlay)
- [Updating and Hiding the Overlay](#updating-and-hiding-the-overlay)
- [Custom Highlight Position](#custom-highlight-position)

## Overlay Modes

The `Dali::Ui::AccessibilityHighlightOverlay` operates in two modes defined by the `Dali::Ui::OverlayHighlightMode` enum:

- `OverlayHighlightMode::AUTO` — The overlay position and size are calculated automatically from the highlighted actor's screen extents.
- `OverlayHighlightMode::MANUAL` — The overlay uses a custom position and size set by the application.

By default, the overlay is created in `AUTO` mode.

## Creating and Configuring the Overlay

Create an instance of `Dali::Ui::AccessibilityHighlightOverlay` and configure its mode before use.

```cpp
#include <dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>

using namespace Dali::Ui;

// Create the overlay instance
AccessibilityHighlightOverlay highlightOverlay;

// Set to automatic mode (default)
highlightOverlay.SetOverlayMode(OverlayHighlightMode::AUTO);

// Or set to manual mode
highlightOverlay.SetOverlayMode(OverlayHighlightMode::MANUAL);
```

To check the current mode:

```cpp
OverlayHighlightMode currentMode = highlightOverlay.GetOverlayMode();
if (currentMode == OverlayHighlightMode::AUTO)
{
  // Automatic positioning is active
}
```

## Updating and Hiding the Overlay

When an accessibility focus highlight is activated, call `UpdateOverlay()` with the highlight actor to position the overlay.

```cpp
// Update the overlay for the currently highlighted actor
Dali::Actor highlightActor = GetHighlightActor(); // Obtain from your accessibility logic
highlightOverlay.UpdateOverlay(highlightActor);
```

To hide the overlay when focus is cleared:

```cpp
highlightOverlay.HideOverlay();
```

## Custom Highlight Position

For scenarios where automatic positioning does not meet your needs, use `SetCustomHighlight()` to define a fixed position and size. This automatically switches the overlay to `MANUAL` mode.

```cpp
// Set a custom highlight area at position (100, 200) with size (300, 150)
highlightOverlay.SetCustomHighlight(Vector2(100.0f, 200.0f), Vector2(300.0f, 150.0f));

// The overlay is now in MANUAL mode
OverlayHighlightMode mode = highlightOverlay.GetOverlayMode(); // Returns OverlayHighlightMode::MANUAL
```

To return to automatic positioning, call `ResetCustomHighlight()`:

```cpp
highlightOverlay.ResetCustomHighlight();

// The overlay is now back to AUTO mode
OverlayHighlightMode mode = highlightOverlay.GetOverlayMode(); // Returns OverlayHighlightMode::AUTO
```

Note that `SetCustomHighlight()` internally sets the mode to `MANUAL`, and `ResetCustomHighlight()` sets it back to `AUTO`. You can also use `SetOverlayMode()` directly if you need to switch modes without changing the custom position values.
