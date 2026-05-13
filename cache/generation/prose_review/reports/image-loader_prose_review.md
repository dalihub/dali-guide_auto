# Image Loader Prose Review

## Summary

The draft was reviewed against the following source files:
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/async-image-loader.h`
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/image-url.h`
- `repos/dali-adaptor/dali/public-api/adaptor-framework/image-options.h`

The draft was found to be **substantially accurate**. Only minor formatting and clarity improvements were applied.

## Changes Made

| Section | Change | Source Evidence |
|---------|--------|-----------------|
| Loading Images > Basic Image Loading | Added note about default values for sampling mode and orientation correction | Header comment: "Note: When using this method, the following defaults will be used: samplingMode = SamplingMode::BOX_THEN_LINEAR, orientationCorrection = true" |
| Loading Images > Loading with Dimensions | Added note that this overload also uses defaults | Header shows same defaults for `Load(url, dimensions)` overload |
| Handling Load Completion | Minor formatting adjustment (indentation in code example) | N/A - style only |
| Working with ImageUrl | Minor formatting adjustment (indentation in code example) | N/A - style only |

## Verified Accurate Prose

The following key statements were verified against source:

1. **AsyncImageLoader creation**: `New()` static method confirmed in header.
2. **Load overloads**: Three overloads confirmed with correct signatures.
3. **Signal type**: `Signal<void(uint32_t, PixelData)>` confirmed as `ImageLoadedSignalType`.
4. **Cancel behavior**: Returns `true` if task removed from queue, `false` if already processing - confirmed in header comment.
5. **ImageUrl::New(Texture&, bool)**: Signature confirmed with `preMultiplied` default parameter.
6. **ImageUrl::New(const EncodedImageBuffer&)**: Signature confirmed.
7. **ImageUrl lifecycle**: Buffer removal on destruction confirmed in header comment.

## Remaining Concerns

None. The draft accurately reflects the public API surface for the image-loader feature.

## Symbols Used

All symbols in the draft are from the allowed dali-ui surface:
- `Dali::Ui::AsyncImageLoader`
- `Dali::Ui::AsyncImageLoader::New()`
- `Dali::Ui::AsyncImageLoader::Load()`
- `Dali::Ui::AsyncImageLoader::Cancel()`
- `Dali::Ui::AsyncImageLoader::CancelAll()`
- `Dali::Ui::AsyncImageLoader::ImageLoadedSignal()`
- `Dali::Ui::ImageUrl`
- `Dali::Ui::ImageUrl::New()`
- `Dali::Ui::ImageUrl::GetUrl()`

Contextual symbols from dali-core/dali-adaptor (e.g., `Dali::Texture`, `Dali::PixelData`, `Dali::ImageDimensions`, `Dali::SamplingMode`) are used appropriately as supporting types.
