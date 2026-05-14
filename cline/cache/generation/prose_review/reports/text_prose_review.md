# Text Prose Review

## Summary

Reviewed the Text feature draft against public headers in `repos/dali-ui/dali-ui-foundation/public-api/text/` and sample files in `repos/dali-ui/samples/text/`. The draft was substantially accurate. One clarification was needed for the Text Fit Configuration section.

## Changes Made

### Text Fit Configuration Section

**Original prose:**
> The text fit algorithm searches from the maximum font size downward by the specified step until finding a size that fits the layout space.
>
> The `Dali::Ui::Text::FitCandidate` class represents a candidate font size during the fit process:

**Revised prose:**
> The range-based text fit algorithm searches from the maximum font size downward by the specified step until finding a size that fits the layout space. The line height follows the current text style configuration.
>
> The `Dali::Ui::Text::FitCandidate` class configures candidate-based text fit, where each candidate defines a specific font size and line height:
>
> In candidate-based text fit, the algorithm selects the largest candidate that fits into the available layout space. Unlike range-based fit, each candidate specifies an absolute line height value.

**Source evidence:**
- `text-fit-range.h`: "Describes a font size range for range-based text fit... The line height is not specified by this object. It follows the current text style configuration"
- `text-fit-candidate.h`: "Describes a text fit candidate for candidate-based text fit... The line height is specified as an absolute value."

**Reason:** The original prose incorrectly implied `FitCandidate` is part of the same algorithm as `FitRange`. The headers clarify these are two different text fit modes: range-based (using `FitRange`) and candidate-based (using `FitCandidate`).

## Verified Accurate

All other sections were verified against headers:

- **Underline Style**: `SetColor()`, `SetThickness()`, `SetType()`, `SetDashLength()`, `SetDashGap()` and `Type` enum (`SOLID`, `DASHED`, `DOUBLE`) confirmed in `underline.h`
- **Shadow Style**: `SetColor()`, `SetOffset()`, `SetBlurRadius()` confirmed in `shadow.h`
- **Outline Style**: `SetColor()`, `SetOffset()`, `SetWidth()`, `SetBlurRadius()` confirmed in `outline.h`
- **LineThrough Style**: `SetColor()`, `SetThickness()` confirmed in `line-through.h`
- **Bevel Style**: `SetDirection()`, `SetIntensity()`, `SetLightColor()`, `SetShadowColor()` confirmed in `bevel.h`
- **Input Filtering**: `SetAllowPattern()`, `SetDenyPattern()`, `RejectReason` enum (`NOT_ALLOWED`, `DENIED`) confirmed in `input-filter.h`. `SetInputFilter()`, `ClearInputFilter()`, `InputRejectedSignal()` confirmed in `input-field.h`
- **Font Variation Axes**: `SetTag()`, `SetValue()` confirmed in `font-variation-axis.h`. `ToString()`, `FromString()` confirmed in `font-variation.h`

## Code Blocks

All code blocks were verified against sample files (`text-style-example.cpp`, `text-input-field-example.cpp`) and found to use correct API patterns. No code changes were required.

## Remaining Concerns

None. The document accurately reflects the public API surface.
