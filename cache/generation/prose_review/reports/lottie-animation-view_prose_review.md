# Lottie Animation View Prose Review

## Summary

Reviewed the Lottie Animation View guide against public headers (`lottie-animation-view.h`, `animated-image-enumerations.h`, `lottie-animation-enumerations.h`, `lottie-animation-types.h`), sample code (`lottie-animation-view-example.cpp`), and UTC tests (`utc-Dali-LottieAnimationView.cpp`). Made localized edits to improve accuracy while preserving the document structure and code blocks.

## Changes Made

### 1. Play State Enum Order (Playback Control section)
**Original:** Listed play states as PLAYING, PAUSED, STOPPED
**Revised:** Listed play states as STOPPED, PLAYING, PAUSED
**Source Evidence:** `animated-image-enumerations.h` defines the enum as:
```cpp
enum class PlayState : uint8_t
{
  STOPPED, ///< Animation has stopped
  PLAYING, ///< The animation is playing
  PAUSED   ///< The animation is paused
};
```
**Reason:** Corrected the order to match the actual enum definition.

### 2. Loop Count Value 0 Behavior (Looping Configuration section)
**Original:** Did not mention behavior for loop count of 0.
**Revised:** Added sentence: "A value of `0` means the animation will not play."
**Source Evidence:** `lottie-animation-view.h` docstring states: "A value of 0 means the animation will not play."
**Reason:** Added missing documentation for the 0 value case.

### 3. Frame Speed Factor Description (Animation Speed and Scaling section)
**Original:** Only described the setter without mentioning the clamping behavior.
**Revised:** Added sentence: "The speed factor is clamped to the range [0.01, 100.0] by the underlying animation renderer."
**Source Evidence:** `lottie-animation-view.h` docstring: "The actual clamping to [0.01, 100.0] is handled by the underlying animation renderer."
**Reason:** Added important implementation detail for developer awareness.

### 4. Render Scale Description (Animation Speed and Scaling section)
**Original:** Brief description of quality tradeoffs.
**Revised:** Added: "A value of 2.0 rasterizes at twice the visual dimensions, producing sharper output on high-density displays. Negative values flip the image."
**Source Evidence:** `lottie-animation-view.h` docstring: "A value of 2.0 rasterizes at twice the visual dimensions... Negative values flip the image."
**Reason:** Added missing details about what specific values do.

### 5. Desired Size Description (Resource Loading and Placeholder section)
**Original:** "Set the desired dimensions for the animation"
**Revised:** "Set the desired dimensions for the animation as a hint for the renderer" and added "A value of 0 means use the natural size."
**Source Evidence:** `lottie-animation-view.h` docstrings: "Sets the desired rasterization width as a hint for the renderer" and "(0 to use natural size)"
**Reason:** Clarified that this is a hint and documented the 0 value behavior.

### 6. GetMarkerInfo/GetContentInfo Return Format (Frame Range and Markers section)
**Original:** Only showed the method call without describing the return format.
**Revised:** Added: "The returned map contains marker names as keys and two-element integer arrays `[startFrame, endFrame]` as values." and similar for GetContentInfo.
**Source Evidence:** `lottie-animation-view.h` docstrings: "The returned map contains marker names as keys and a two-element integer array [startFrame, endFrame] as values."
**Reason:** Added missing documentation about the return value structure.

### 7. Animation Finished Signal Description (Signals and State Monitoring section)
**Original:** "to be notified when the animation completes"
**Revised:** "to be notified when the animation completes all loops"
**Source Evidence:** `lottie-animation-view.h` typedef comment: "Animation finished signal type. Emitted when the animation completes all loops."
**Reason:** Clarified that the signal fires after all loops complete, not just at the end of a single play.

### 8. Frame Cache Description (Memory and Performance section)
**Original:** Brief description.
**Revised:** Added: "When enabled, all decoded frames are cached in memory to reduce CPU cost during looping, at the expense of higher memory usage"
**Source Evidence:** `lottie-animation-view.h` docstring: "When enabled, all decoded frames are cached in memory to reduce CPU cost during looping, at the expense of higher memory usage."
**Reason:** Added important performance tradeoff information.

### 9. Rasterization Notification Description (Memory and Performance section)
**Original:** Brief description.
**Revised:** Added: "This is useful for low-fps Lottie files to avoid unnecessary render thread wakeups"
**Source Evidence:** `lottie-animation-view.h` docstring: "Useful for low-fps Lottie files to avoid unnecessary render thread wakeups."
**Reason:** Added use case context.

### 10. Pixel Area Description (Memory and Performance section)
**Original:** Brief description.
**Revised:** Added: "The area is specified as normalized coordinates (x, y, width, height) where each component is in the range [0, 1]"
**Source Evidence:** `lottie-animation-view.h` docstring: "The area is specified as normalized coordinates: (x, y, width, height) where each component is in the range [0, 1]."
**Reason:** Clarified the coordinate format.

### 11. Dynamic Properties Section (New Content)
**Original:** Minimal placeholder example.
**Revised:** Expanded with concrete example showing DynamicPropertyInfo fields, keyPath usage, and important thread safety warning.
**Source Evidence:** `lottie-animation-types.h` struct definition and `lottie-animation-enumerations.h` VectorProperty enum.
**Reason:** The original section was too sparse. Added concrete usage information from the type definitions.

### 12. Image Tinting Section
**Original:** No mention of per-layer control.
**Revised:** Added: "For per-layer color control, use `SetDynamicProperty()` instead."
**Source Evidence:** `lottie-animation-view.h` docstring: "For per-layer color control, use SetDynamicProperty() instead."
**Reason:** Added cross-reference to related functionality.

## Remaining Concerns

None. All prose has been verified against public headers, samples, and UTCs. The document structure was preserved and all code blocks remain accurate.

## Verification Method

- Compared enum values against `animated-image-enumerations.h` and `lottie-animation-enumerations.h`
- Verified method signatures and docstrings against `lottie-animation-view.h`
- Cross-referenced usage patterns with `lottie-animation-view-example.cpp`
- Confirmed default values and behaviors with `utc-Dali-LottieAnimationView.cpp`
