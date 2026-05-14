# Label Prose Review Report

## Summary

The Label draft document was reviewed against the public API header (`repos/dali-ui/dali-ui-foundation/public-api/label.h`), text enumerations (`repos/dali-ui/dali-ui-foundation/public-api/text/text-enumerations.h`), and sample files (`text-example.cpp`, `text-marquee-example.cpp`, `text-scale-example.cpp`, `text-cutout-mask-example.cpp`).

## Changes Made

### 1. Font Weight Enum Value Correction

**Location:** Section "Font Weight, Width, and Slant"

**Original Prose:**
> Available font weight values include `THIN`, `LIGHT`, `NORMAL`, `MEDIUM`, `SEMIBOLD`, `BOLD`, and `BLACK`.

**Revised Prose:**
> Available font weight values include `THIN`, `LIGHT`, `NORMAL`, `MEDIUM`, `SEMI_BOLD`, `BOLD`, and `BLACK`.

**Source Evidence:** `Text::FontWeight` enum in `text-enumerations.h` defines `SEMI_BOLD = 7` (with underscore), not `SEMIBOLD`. The enum also includes aliases like `DEMIBOLD = SEMI_BOLD`, but the primary identifier uses an underscore.

**Rationale:** The original prose used an incorrect enum identifier. The actual public API uses `SEMI_BOLD` with an underscore separator.

## Verified Accurate Content

The following sections were verified against source files and found to be accurate:

- **Creating a Label:** `Label::New()` signatures match the header (default constructor and `New(const Dali::String& text)`).
- **Text and Font Configuration:** All setters and getters (`SetText`, `GetText`, `SetFontFamily`, `GetFontFamily`, `SetFontSize`, `GetFontSize`) match the header signatures.
- **Font Variation Axes:** Both overloads of `SetFontVariation()` are correctly documented (string and vector forms).
- **Text Alignment:** `Text::Alignment` enum values (`START`, `CENTER`, `END`) match `text-enumerations.h`.
- **Layout Direction Mode:** `Text::LayoutDirectionMode` values (`CONTENTS`, `INHERIT`, `LOCALE`) match the enum definition.
- **Line Wrap Mode:** `Text::LineWrapMode` values (`WORD`, `CHARACTER`, `HYPHENATION`, `MIXED`) match the enum definition.
- **Overflow Mode:** `Text::OverflowMode` values (`CLIP`, `ELLIPSIS`) match the enum definition.
- **Line Height Control:** `Text::LineHeightMode` values (`RELATIVE`, `ABSOLUTE`) and `LINE_HEIGHT_AUTO` constant match the header.
- **Marquee Animation:** All marquee-related enums and methods (`MarqueeTriggerPolicy`, `MarqueeOrientation`, `MarqueeStopMode`, `StartMarquee`, `StopMarquee`, `IsMarqueeRunning`) match the header.
- **Font Size Scaling:** All scaling methods (`SetFontSizeScale`, `GetFontSizeScale`, `SetMinimumFontSizeScale`, `SetMaximumFontSizeScale`, `SetSystemFontSizeScaleEnabled`, `GetAdjustedFontSizeScale`) match the header.
- **Asynchronous Rendering:** All async methods and signals match the header, including signal signatures.
- **Text Styling Effects:** All style methods (`SetUnderline`, `SetShadow`, `SetOutline`, `SetLineThrough`, `SetBevel`, `SetCutoutEnabled`, `SetMaskEffect`) match the header.
- **Anchor Links:** `SetMarkupEnabled`, `SetAnchorColor`, `SetAnchorClickedColor`, and `AnchorClickedSignal` match the header.

## Remaining Concerns

None. The document is accurate and well-structured after the single correction.
