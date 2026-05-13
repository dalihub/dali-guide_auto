# Accessibility Highlight Overlay Prose Review

Changed prose:
- Clarified that the overlay manages the current highlight actor, matching `UpdateOverlay(Dali::Actor& activeHighlight)`.
- Replaced the broad claim that automatic mode calculates from the supplied object with a more precise statement: it finds a parent SceneView and positions from highlighted model bounds.
- Clarified that `SetCustomHighlight` uses a rectangle within the overlay and switches to manual mode.
- Clarified that `ResetCustomHighlight` clears manual position and size and sets automatic mode.
- Clarified that `HideOverlay` hides the overlay highlight actor when it exists, but does not clear manual state or change mode.
- Narrowed `SetOverlayMode` guidance to mode preservation only, because it does not restore manual position or size.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/integration-api/accessibility-highlight-overlay.h` exposes `AccessibilityHighlightOverlay`, `OverlayHighlightMode`, `SetCustomHighlight`, `ResetCustomHighlight`, `UpdateOverlay`, `HideOverlay`, `SetOverlayMode`, and `GetOverlayMode`.
- `repos/dali-ui/dali-ui-foundation/integration-api/accessibility-highlight-overlay.cpp` initializes mode to `AUTO`, sets manual position/size and mode in `SetCustomHighlight`, clears them in `ResetCustomHighlight`, and only updates `mOverlayMode` in `SetOverlayMode`.
- `UpdateOverlay` finds the parent SceneView, creates an overlay if needed, and updates overlay position; automatic placement uses `GetOverlayExtents`.
- `HideOverlay` sets the named highlight actor invisible and does not change `mOverlayMode`, `mManualPosition`, or `mManualSize`.
- `repos/dali-ui/dali-ui-foundation/integration-api/view-accessible.cpp` passes the accessibility highlight actor to `mHighlightOverlay.UpdateOverlay(highlight)` and calls `HideOverlay` during highlight clearing.

Remaining concerns:
- No sample or UTC paths were available in the context pack, so examples were checked against the public header and implementation behavior only.
- The class is under `integration-api`; the guide keeps it because the provided allowed symbol list marks it as the feature’s public dali-ui surface.
