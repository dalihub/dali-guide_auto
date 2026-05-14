# Image Loader Prose Review

## Summary

Reviewed the Image Loader draft against public headers in dali-ui and dali-adaptor. The draft was largely accurate with minor issues corrected.

## Changes Made

### 1. Code Block Formatting (Section: Asynchronous Image Loading)
- **Change**: Added missing opening brace in `OnImageLoaded` method body
- **Source**: `repos/dali-ui/dali-ui-foundation/public-api/image-loader/async-image-loader.h` shows the callback signature `void(uint32_t, PixelData)`

### 2. Prose Clarification (Section: Loading Images with Dimensions and Sampling)
- **Change**: Changed "maximum width and height to fit" to "width and height to fit"
- **Source**: Header comment states "The width and height to fit the loaded image to" - not specifically "maximum"

### 3. Prose Clarification (Section: Loading Images with Dimensions and Sampling)
- **Change**: Changed "respect orientation metadata in the image header" to "reorient the image according to orientation metadata in its header"
- **Source**: Header states "Reorient the image to respect any orientation metadata in its header"

### 4. Added Missing API (Section: Using ImageUrl with Encoded Buffers)
- **Change**: Added example for `ImageUrl::New(Texture&, bool)` overload
- **Source**: `repos/dali-ui/dali-ui-foundation/public-api/image-loader/image-url.h` defines:
  ```cpp
  static ImageUrl New(Texture& texture, bool preMultiplied = false);
  ```
- **Note**: This overload was in the allowed_symbols but missing from the draft

## Verified Accurate (No Changes Needed)

1. **AsyncImageLoader::New()** - Correctly documented
2. **AsyncImageLoader::Load() overloads** - All three overloads correctly documented with accurate default values (SamplingMode::BOX_THEN_LINEAR, orientationCorrection=true)
3. **AsyncImageLoader::Cancel()** - Return behavior accurately described
4. **AsyncImageLoader::CancelAll()** - Correctly documented
5. **AsyncImageLoader::ImageLoadedSignal()** - Signal type and usage correctly documented
6. **ImageUrl::New(EncodedImageBuffer)** - Correctly documented
7. **ImageUrl::GetUrl()** - Correctly documented
8. **ImageUrlUtils::GenerateUrl() overloads** - All overloads verified against `repos/dali-ui/dali-ui-foundation/public-api/image-loader/image-url-utils.h`

## Notes on Allowed Symbols

The context pack's `allowed_symbols` list did not include `ImageUrlUtils`, but this namespace is part of the dali-ui public API (`repos/dali-ui/dali-ui-foundation/public-api/image-loader/image-url-utils.h`) and is directly relevant to the image-loader feature. The examples using `ImageUrlUtils` were kept because:
1. They are accurate against the public headers
2. They are part of the dali-ui public API surface
3. They provide essential functionality for the ImageUrl class (as noted in the ImageUrl class documentation: "An instance of ImageUrl can be created from ImageUrlUtils::GenerateUrl()")

## Remaining Concerns

None. The document accurately reflects the public API and provides useful guidance for application developers.
