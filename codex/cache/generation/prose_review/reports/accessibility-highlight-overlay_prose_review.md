# Accessibility Highlight Overlay Prose Review

Changed prose:
- Replaced broad “controls the visual highlight rectangle” wording with “manages the visual highlight overlay” to match `AccessibilityHighlightOverlay` behavior.
- Changed “application-level object that owns highlight overlay behavior” to “coordination object that stores highlight overlay state” because the implementation stores mode/manual rectangle state and manages an overlay actor, but the draft’s ownership wording was too broad.
- Clarified that `SetCustomHighlight()` stores manual position/size and switches the overlay to manual mode.
- Replaced “default target bounds” with “automatic overlay extents” to better match `UpdateOverlayPosition()` and `GetOverlayExtents()`.
- Clarified `UpdateOverlay()` behavior: it updates the active highlight overlay and creates the overlay actor when needed.
- Changed `HideOverlay()` prose from “remove” to “hide” because the implementation sets the highlight actor visibility to false rather than deleting the overlay.
- Clarified that `ResetCustomHighlight()` clears the manual rectangle and returns the mode to automatic calculation.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/integration-api/accessibility-highlight-overlay.h` declares `AccessibilityHighlightOverlay()`, `SetCustomHighlight(Vector2, Vector2)`, `ResetCustomHighlight()`, `UpdateOverlay(Dali::Actor&)`, `HideOverlay()`, `SetOverlayMode(OverlayHighlightMode)`, and `GetOverlayMode() const`.
- `repos/dali-ui/dali-ui-foundation/integration-api/accessibility-highlight-overlay.cpp` initializes mode to `OverlayHighlightMode::AUTO`, makes `SetCustomHighlight()` set manual position/size and `OverlayHighlightMode::MANUAL`, and makes `ResetCustomHighlight()` clear manual values and restore `OverlayHighlightMode::AUTO`.
- `UpdateOverlay()` creates an overlay if needed and then updates its position; `HideOverlay()` hides the highlight child by setting visibility false.

Remaining concerns:
- `UpdateOverlay()` necessarily uses `Dali::Actor&` because that is the public signature, so the guide keeps `Dali::Actor` in update examples despite the app-facing preference for view coordination.
- No repository sample or UTC candidate was provided for this feature, so examples remain small API-focused snippets verified against the public header.
