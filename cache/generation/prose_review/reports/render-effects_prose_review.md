# Render Effects Prose Review

## Summary

The draft was reviewed against public API headers in `repos/dali-ui/dali-ui-foundation/public-api/render-effects/` and the sample file `repos/dali-ui/samples/text/text-cutout-mask-example.cpp`. The draft was accurate overall with only one minor correction needed.

## Changes Made

### 1. Gaussian Blur Default Radius Unit (Line 42)

**Original:** "Create with default radius (10 pixels)"
**Revised:** "Create with default radius (10 units)"

**Source Evidence:** `gaussian-blur-effect.h` line 47: "Creates an initialized GaussianBlurEffect, with default blur radius 10u."

**Reason:** The API specifies the radius in units (10u), not pixels. While the distinction is minor, accuracy requires matching the header documentation.

## Verified Accurate Prose

The following sections were verified against source files and required no changes:

- **Applying Render Effects**: `SetRenderEffect()`, `GetRenderEffect()`, `ClearRenderEffect()` methods confirmed in `view.h` lines 919-935.
- **GaussianBlurEffect description**: Header confirms it "blurs owner view and its children" (line 38).
- **BackgroundBlurEffect description**: Header confirms it "blurs owner view's background" (line 35).
- **SetSourceActor/SetStopperActor**: Methods confirmed in `background-blur-effect.h` lines 152-163. Header note confirms source actor must be a parent.
- **MaskEffect API**: `New()` overloads, `MaskMode` enum, `SetTargetMaskOnce()`, `SetSourceMaskOnce()` all confirmed in `mask-effect.h`.
- **MaskEffect usage pattern**: Sample file `text-cutout-mask-example.cpp` lines 107-109 confirms the pattern of adding maskView as child before applying effect.
- **Animation methods**: `AddBlurStrengthAnimation()` and `AddBlurOpacityAnimation()` signatures confirmed in both blur effect headers.
- **Lifecycle methods**: `Activate()`, `Deactivate()`, `IsActivated()`, `Refresh()` confirmed in `render-effect.h` lines 55-70.
- **FinishedSignal**: Confirmed in both `GaussianBlurEffect` and `BackgroundBlurEffect` headers.

## Code Blocks

All code blocks were preserved as requested. Code examples use correct dali-ui API patterns:
- Fluent chaining with View setters
- `Dali::Ui::View` as the primary object model
- Proper use of `Dali::Animation`, `Dali::AlphaFunction`, `Dali::TimePeriod` from dali-core (acceptable as context APIs)

## Remaining Concerns

None. The draft accurately reflects the public API surface for render effects.
