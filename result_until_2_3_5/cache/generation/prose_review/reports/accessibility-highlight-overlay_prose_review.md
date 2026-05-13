# Accessibility Highlight Overlay Prose Review

Changed prose:
- Narrowed the overview from "show, move, customize, and hide" to "update, customize, and hide" because the public API exposes `UpdateOverlay`, `SetCustomHighlight`, `ResetCustomHighlight`, `HideOverlay`, `SetOverlayMode`, and `GetOverlayMode`, not a direct public "move" API.
- Replaced "application-side overlay controller" with "overlay controller for accessibility highlight presentation" to avoid overstating ownership.
- Clarified that `SetOverlayMode` directly sets the mode used by later overlay updates.
- Changed the focus section to distinguish the app-facing `Dali::Ui::View` focus target from the `Dali::Actor&` highlight actor required by `UpdateOverlay`.
- Added the important `UpdateOverlay` fallback behavior: it creates an overlay when needed for a parent Scene3D view, and returns without updating the overlay when no Scene3D parent is found.
- Clarified that `SetCustomHighlight` switches mode to `Dali::Ui::OverlayHighlightMode::MANUAL`.
- Changed "current active object bounds" to "automatic bounds calculated from the active highlight actor" to match implementation behavior.
- Clarified that `ResetCustomHighlight` returns mode to `Dali::Ui::OverlayHighlightMode::AUTO`.

Code/example adjustments:
- Corrected the include from `<dali-ui/public-api/accessibility-highlight-overlay.h>` to `<dali-ui-foundation/integration-api/accessibility-highlight-overlay.h>`, matching the repository header path.
- Qualified `Dali::Ui::OverlayHighlightMode` and `Dali::Vector2` in examples so snippets do not rely on an unstated `using namespace`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/integration-api/accessibility-highlight-overlay.h` declares `Dali::Ui::AccessibilityHighlightOverlay`, `Dali::Ui::OverlayHighlightMode`, `SetCustomHighlight`, `ResetCustomHighlight`, `UpdateOverlay`, `HideOverlay`, `SetOverlayMode`, and `GetOverlayMode`.
- `repos/dali-ui/dali-ui-foundation/integration-api/accessibility-highlight-overlay.cpp` initializes the mode to `AUTO`, sets mode to `MANUAL` in `SetCustomHighlight`, and resets mode to `AUTO` in `ResetCustomHighlight`.
- `UpdateOverlay` finds a parent Scene3D view, creates the overlay if needed, and updates overlay position only when an overlay actor exists.
- `HideOverlay` hides the overlay's highlight child rather than deleting the overlay actor.
- `repos/dali-ui/dali-ui-foundation/integration-api/view-accessible.cpp` shows the normal accessibility path creating or reusing a highlight actor, adding it to the focused view, calling `mHighlightOverlay.UpdateOverlay(highlight)`, and calling `HideOverlay` from `ClearHighlight`.

Remaining concerns:
- This API is under `integration-api`, not `public-api`; the draft target says "application developers using dali-ui public APIs", so the feature may need classification review if integration APIs should not be promoted in app-facing guides.
- No dedicated UTC for `AccessibilityHighlightOverlay` was found in the visible `repos/dali-ui/automated-tests` search results; evidence is from public header, implementation, generated Doxygen XML, and `ViewAccessible` integration usage.
