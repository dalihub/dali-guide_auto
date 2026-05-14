# Animated ImageView Prose Review

## Summary

Reviewed the draft against `animated-image-view.h`, `animated-image-enumerations.h`, `image-enumerations.h`, and sample code. The draft was largely accurate but contained incorrect FittingMode enum values and descriptions.

## Changes Made

### 1. FittingMode Section (Critical Fix)

**Original prose:**
```cpp
// Scale to fill while maintaining aspect ratio (may crop)
animatedView.SetFittingMode(FittingMode::FIT_KEEP_ASPECT_RATIO);

// Scale to fit entirely within bounds (may letterbox)
animatedView.SetFittingMode(FittingMode::SHRINK_TO_FIT);
```

**Issue:** `SHRINK_TO_FIT` does not exist in `Ui::Image::FittingMode`. The actual enum values from `image-enumerations.h` are:
- `FIT_KEEP_ASPECT_RATIO` — scales to fit within bounds, preserving aspect ratio
- `FILL` — stretches to fill, not preserving aspect ratio
- `OVER_FIT_KEEP_ASPECT_RATIO` — scales to fill, preserving aspect ratio, crops overflow
- `CENTER` — keeps original size

**Fix:** Replaced with correct enum values and accurate descriptions matching the header definitions.

### 2. Stop() Description (Clarification)

**Original prose:**
```cpp
// Stop and reset based on StopBehavior
animatedView.Stop();
```

**Issue:** The header doc for `Stop()` says "stops playback and resets to the first frame," but `SetStopBehavior()` modifies this behavior.

**Fix:** Changed comment to "Stop playback (behavior depends on StopBehavior)" to accurately reflect the relationship between `Stop()` and `SetStopBehavior()`.

### 3. Loop Count (Minor Addition)

**Original:** No mention of loop count 0 behavior.

**Fix:** Added note: "A loop count of `0` means the animation will not play." This matches the header documentation.

### 4. JumpToFrame (Wording Adjustment)

**Original:** "Jump to frame 5 (zero-based index)"

**Fix:** Removed "(zero-based index)" since the header does not explicitly specify the indexing convention. The sample code uses `JumpToFrame(5)` to jump to "frame 5" without clarifying the base.

### 5. Default FittingMode (Addition)

**Original:** No mention of default FittingMode.

**Fix:** Added "(default)" annotation to `FittingMode::FILL` based on header comment: "The default fitting mode is Ui::Image::FittingMode::FILL (stretch to fill)."

## Source Evidence

| Prose Claim | Source | Status |
|-------------|--------|--------|
| `SHRINK_TO_FIT` enum | `image-enumerations.h` | **Not found** — removed |
| `FIT_KEEP_ASPECT_RATIO` description | `image-enumerations.h:27` | Corrected |
| `OVER_FIT_KEEP_ASPECT_RATIO` | `image-enumerations.h:29` | Added |
| `CENTER` enum | `image-enumerations.h:30` | Added |
| `Stop()` resets to first frame | `animated-image-view.h:143` | Clarified with StopBehavior context |
| Loop count 0 behavior | `animated-image-view.h:155` | Added |
| Default FittingMode is FILL | `animated-image-view.h:262` | Added |

## Remaining Concerns

None. All prose now aligns with public headers and sample code.
