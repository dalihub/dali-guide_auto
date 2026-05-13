# Image View Prose Review

## Summary

Reviewed the Image View guide draft against public headers (`image-view.h`, `image-enumerations.h`), implementation context, and sample code. The draft was largely accurate with a few corrections needed.

## Changes Made

### 1. Overview - Removed Unsubstantiated Claim
**Location:** First paragraph  
**Original:** "ImageView displays static images from URLs, resource paths, or in-memory pixel data."  
**Revised:** "ImageView displays images from URLs or resource paths."  
**Source Evidence:** The public header (`image-view.h`) only exposes URL-based APIs: `New()`, `New(const Dali::String& url)`, and `SetResourceUrl()`. No API exists for in-memory pixel data.  
**Reason:** Removed claim not supported by the public API surface.

### 2. Fitting Mode FILL - Corrected Description
**Location:** Fitting Modes section  
**Original:** "Fill the entire view, may crop the image"  
**Revised:** "Stretch to fill the view, does not preserve aspect ratio"  
**Source Evidence:** `image-enumerations.h` line 31: `FILL ///< The visual should be stretched to fill, not preserving aspect ratio`  
**Reason:** FILL stretches without preserving aspect ratio; it does not crop. The original description was misleading.

### 3. Fitting Mode OVER_FIT_KEEP_ASPECT_RATIO - Clarified Description
**Location:** Fitting Modes section  
**Original:** "Keep aspect ratio, cover the view (may overflow)"  
**Revised:** "Keep aspect ratio, cover the view (excess is cropped)"  
**Source Evidence:** `image-enumerations.h` line 32: `OVER_FIT_KEEP_ASPECT_RATIO ///< The visual should be scaled to fit, preserving aspect ratio, outside is cropped away`  
**Reason:** Clarified that excess is cropped away, not that the image overflows the view.

### 4. Sampling Mode LINEAR - Removed Unverified Default Claim
**Location:** Sampling Mode section  
**Original:** "Linear filtering (smooth, default)"  
**Revised:** "Linear filtering (smooth)"  
**Source Evidence:** `image-enumerations.h` shows `DEFAULT` as a separate enum value. The header does not document which mode is the default.  
**Reason:** Removed unverified claim about default value.

### 5. Load Policy ATTACHED - Removed Unverified Default Claim
**Location:** Load Policy section  
**Original:** "Load when the view is added to the scene (default)"  
**Revised:** "Load when the view is added to the scene"  
**Source Evidence:** `image-enumerations.h` shows `IMMEDIATE = 0` as the first enum value, but the header does not document which is the default.  
**Reason:** Removed unverified claim about default value.

## Verified Accurate Sections

The following sections were verified against headers and samples with no changes needed:

- **Creating an ImageView:** `New()` and `New(url)` signatures match header.
- **Setting the Image Source:** `SetResourceUrl()` and `GetResourceUrl()` match header.
- **Pixel Area and Cropping:** `SetPixelArea()`, `GetPixelArea()` signatures and normalized coordinate description match header documentation.
- **Image Color and Tinting:** `SetImageColor()` and `GetImageColor()` match header.
- **Alpha Masking:** `SetAlphaMaskUrl()`, `SetCropToMask()`, `SetMaskingMode()`, and `Reload()` usage verified in `image-alpha-mask-example.cpp`.
- **Loading Policies:** Enum values and descriptions match `image-enumerations.h`.
- **Placeholder Images:** `SetPlaceholderUrl()` and `GetPlaceholderUrl()` match header; usage verified in `image-placeholder-example.cpp`.
- **Resource Ready Signal:** `ResourceReadySignal()`, `GetLoadingStatus()`, and `DownCast()` usage verified in samples.
- **N-Patch Images:** `SetNPatchBorder()`, `SetNPatchBorderOnly()` match header.
- **Additional Properties:** All setter/getter pairs match header signatures.
- **Property Access:** Property indices match `ImageView::Property` enum in header.
- **Reloading Images:** `Reload()` signature matches header.

## Remaining Concerns

None. All prose now accurately reflects the public API surface documented in the headers and demonstrated in the sample code.
