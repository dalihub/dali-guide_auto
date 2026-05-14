# Lottie Animation View Prose Review

## Summary

Reviewed the Lottie Animation View guide against public headers (`lottie-animation-view.h`, `lottie-animation-enumerations.h`, `animated-image-enumerations.h`, `image-enumerations.h`), sample code (`lottie-animation-view-example.cpp`), and UTC tests (`utc-Dali-LottieAnimationView.cpp`). Made localized edits to improve accuracy while preserving all code blocks and document structure.

## Changes Made

### 1. Loop Count Section - Added Missing Behavior for Value 0
**Location:** Looping Configuration > Loop Count

**Original:**
> Set the number of times the animation repeats with `SetLoopCount()`. Use `-1` for infinite looping.

**Revised:**
> Set the number of times the animation repeats with `SetLoopCount()`. Use `-1` for infinite looping. A value of `0` means the animation will not play.

**Source Evidence:** Header `lottie-animation-view.h` line 156-158:
```cpp
* A value of -1 means infinite looping. A value of 0 means the animation
* will not play. A positive value specifies an exact loop count.
```

### 2. Markers Section - Corrected SetMinMaxFrameByMarker Behavior
**Location:** Frame Range and Markers > Markers

**Original:**
```cpp
// Play from "start" marker to end
lottieView.SetMinMaxFrameByMarker("start");
```

**Revised:**
```cpp
// Play the range of the "start" marker
lottieView.SetMinMaxFrameByMarker("start");
```

**Source Evidence:** Header `lottie-animation-view.h` line 183-186:
```cpp
* If only @p minMarker is given, the animation plays the range of that marker.
* If both are given, the animation plays from the start of @p minMarker to the
* end of @p maxMarker.
```

### 3. Pixel Area Section - Added Coordinate Format Clarification
**Location:** Visual Styling > Pixel Area

**Original:**
> Display a sub-region of the animation using `SetPixelArea()`:

**Revised:**
> Display a sub-region of the animation using `SetPixelArea()`. The area is specified as normalized coordinates `(x, y, width, height)` where each component is in the range `[0, 1]`.

**Source Evidence:** Header `lottie-animation-view.h` line 363-366:
```cpp
* The area is specified as normalized coordinates: (x, y, width, height)
* where each component is in the range [0, 1].
```

### 4. Properties Table - Updated LOOP_COUNT Description
**Location:** Properties table

**Original:**
| `LOOP_COUNT` | Integer | Number of loops (-1 for infinite) |

**Revised:**
| `LOOP_COUNT` | Integer | Number of loops (-1 for infinite, 0 to not play) |

**Source Evidence:** Same as change #1 above.

## Verified Accurate (No Changes Needed)

The following were verified against source and found accurate:

- **API names and namespaces**: All `LottieAnimationView`, `Ui::AnimatedImage::PlayState`, `Ui::AnimatedImage::StopBehavior`, `Ui::LottieAnimation::LoopingMode`, `Ui::Image::ReleasePolicy` references are correct per enumeration headers.
- **Signal signatures**: `AnimationFinishedSignal()` and `ResourceReadySignal()` return types and callback signatures match header declarations.
- **Method signatures**: All setter methods return `LottieAnimationView&` for fluent chaining as documented.
- **Default values**: Verified via UTC tests - `LoopCount` defaults to -1, `StopBehavior` defaults to `CURRENT_FRAME`, `LoopingMode` defaults to `RESTART`, `FrameSpeedFactor` defaults to 1.0f, `RenderScale` defaults to 1.0f, `RedrawOnScaleUp/Down` default to true, `EnableFrameCache` defaults to false.
- **Code examples**: All code blocks compile against the public API and follow dali-ui idioms.

## Remaining Concerns

None. The document accurately reflects the public API surface for `LottieAnimationView`.
