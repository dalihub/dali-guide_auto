# Render Effects Prose Review

## Summary

The draft was reviewed against public headers in `repos/dali-ui/dali-ui-foundation/public-api/render-effects/` and sample code in `repos/dali-ui/samples/`. The document was largely accurate with correct API signatures and usage patterns.

## Changes Made

### 1. Background Blur Effect - Default Radius Documentation
**Location:** "Creating a Background Blur Effect" section

**Change:** Added explicit documentation that `BackgroundBlurEffect::New()` uses a default radius of 10 pixels.

**Before:**
```cpp
Ui::BackgroundBlurEffect bgBlur = Ui::BackgroundBlurEffect::New(60u);
```

**After:**
Added prose: "Create with a default radius of 10 pixels:" followed by the no-argument constructor example.

**Source Evidence:** `background-blur-effect.h` line 57: "Creates an initialized BackgroundBlurEffect, using default settings. As default, blur radius is set to 10u."

### 2. Background Blur Effect - SetSourceActor Requirements
**Location:** "Specifying Source and Stopper Actors" section

**Change:** Added important usage notes about `SetSourceActor()` requirements.

**Added:**
> **Note:** The source actor must be a parent of the view for `SetSourceActor()` to take effect. The `RenderEffect` does not hold references to source or stopper actors, so you must ensure they remain valid for the effect's lifetime.

**Source Evidence:** 
- `background-blur-effect.h` line 147: "If given source actor is not a parent of source view, it has no efforts."
- `background-blur-effect.h` line 149: "RenderEffect didn't hold source actor reference."
- `background-blur-effect.h` line 156: "RenderEffect didn't hold stopper actor reference."

## Verified Accurate (No Changes Needed)

### API Signatures
All API signatures in code blocks were verified against headers:
- `GaussianBlurEffect::New()` and `New(uint32_t)` ✓
- `BackgroundBlurEffect::New()` and `New(uint32_t)` ✓
- `MaskEffect::New(Ui::View)` and `New(Ui::View, MaskMode, Vector2, Vector2)` ✓
- `SetBlurRadius()`, `GetBlurRadius()` ✓
- `SetBlurDownscaleFactor()`, `GetBlurDownscaleFactor()` ✓
- `SetBlurOnce()`, `GetBlurOnce()` ✓
- `SetSourceActor()`, `SetStopperActor()` ✓
- `SetTargetMaskOnce()`, `SetSourceMaskOnce()` ✓
- `AddBlurStrengthAnimation()`, `AddBlurOpacityAnimation()` ✓
- `Activate()`, `Deactivate()`, `Refresh()`, `IsActivated()` ✓
- `FinishedSignal()` ✓

### Usage Patterns
- `View::SetRenderEffect()` and `ClearRenderEffect()` confirmed in `view.h` ✓
- Mask source must be added as child - confirmed in `text-cutout-mask-example.cpp` ✓
- One render effect per view - confirmed in `render-effect.h` comment ✓

### Code Blocks
All code blocks were verified against sample code patterns in:
- `repos/dali-ui/samples/ui-scale/ui-scale-example.cpp` (Zone F demonstrates all render effects)
- `repos/dali-ui/samples/text/text-cutout-mask-example.cpp` (MaskEffect usage)

## Remaining Considerations

None. The document accurately reflects the public API and follows the dali-ui application developer guide conventions.
