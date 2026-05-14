# Accessibility Highlight Overlay Prose Review

## Summary

The draft documentation was fundamentally accurate. One minor clarification was made to improve precision about the AUTO mode behavior.

## Changes Made

### 1. AUTO Mode Description (Section: Overlay Modes)

**Original:**
> The overlay position and size are calculated automatically based on the highlighted actor's screen extents.

**Revised:**
> The overlay position and size are calculated automatically based on the highlighted actor's parent within the scene.

**Source Evidence:**
From `accessibility-highlight-overlay.cpp`, lines 143-155:
```cpp
Bounds AccessibilityHighlightOverlay::GetOverlayExtents(Dali::Actor& sceneView, Actor& highlight)
{
  auto model = highlight.GetParent();
  // ...
  auto actualExtent = DevelActor::CalculateScreenExtents(model);
  // ...
  return Bounds(x, y, actualExtent.width, actualExtent.height);
}
```

The implementation calculates extents from `highlight.GetParent()` (the model), not directly from the highlight actor. The revision clarifies this while remaining app-developer friendly.

## Verified Accurate Content

### API Signatures
All API signatures in code examples were verified against `repos/dali-ui/dali-ui-foundation/integration-api/accessibility-highlight-overlay.h`:
- `AccessibilityHighlightOverlay()` constructor ✓
- `SetCustomHighlight(Vector2 position, Vector2 size)` ✓
- `ResetCustomHighlight()` ✓
- `SetOverlayMode(OverlayHighlightMode mode)` ✓
- `GetOverlayMode() const` ✓
- `UpdateOverlay(Dali::Actor& activeHighlight)` ✓
- `HideOverlay()` ✓

### Behavior Descriptions
- `SetCustomHighlight()` automatically sets mode to MANUAL - **Verified** (line 68 in .cpp: `mOverlayMode = OverlayHighlightMode::MANUAL;`)
- `ResetCustomHighlight()` resets to AUTO mode - **Verified** (line 74 in .cpp: `mOverlayMode = OverlayHighlightMode::AUTO;`)
- Default mode is AUTO - **Verified** (constructor initialization list)

### Code Examples
All code examples are syntactically correct and follow dali-ui conventions.

## Remaining Concerns

None. The documentation accurately describes the API and usage patterns.

## Non-English Content

No non-English prose was found in the draft. No removal was necessary.
