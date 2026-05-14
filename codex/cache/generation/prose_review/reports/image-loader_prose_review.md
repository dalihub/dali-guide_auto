# Image Loader Prose Review

Changed prose:
- Narrowed the overview wording for `Dali::Ui::ImageUrl` so it says the generated URL is for dali-ui image presentation paths, not a general `AsyncImageLoader` input.
- Reworded the signal timing sentence to match the public header's warning that `ImageLoadedSignal` should be connected before `Load` because the signal may be emitted immediately.
- Revised the `ImageUrl` section and example. The original example passed `ImageUrl::GetUrl()` to `AsyncImageLoader::Load`, but the public `AsyncImageLoader` API only exposes URL-string loading and the internal loading path does not resolve `ImageUrl` buffer URLs back to `EncodedImageBuffer` for this public method.
- Reworded `ImageUrl` lifetime prose to say the resource is requested for removal when the handle is released, while visual references can keep it alive.
- Simplified handle-transfer prose to normal copy/move handle semantics and added `<utility>` to the example that uses `std::move`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/async-image-loader.h` documents `AsyncImageLoader::New`, `Load`, `Cancel`, `CancelAll`, `ImageLoadedSignal`, task IDs, immediate signal timing, and empty `PixelData` failure detection.
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/async-image-loader.cpp` shows the public `Load(const Dali::String&, ImageDimensions)` path converts the string into an internal `VisualUrl`.
- `repos/dali-ui/dali-ui-foundation/internal/image-loader/loading-task.cpp` shows normal URL loading uses file or synchronous download paths, while encoded-buffer loading is a separate internal constructor path.
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/image-url.h` documents `ImageUrl::New(const EncodedImageBuffer&)`, `GetUrl`, copy/move constructors, assignment, and `DownCast`.
- `repos/dali-ui/dali-ui-foundation/internal/image-loader/image-url-impl.cpp` shows `ImageUrl` stores a generated URL and requests external resource removal in its destructor.

Remaining concerns:
- The guide intentionally avoids adding a full `Dali::Ui::View` visual setup example because the supplied public surface for this feature only included `AsyncImageLoader` and `ImageUrl`; adding a complete visual property example would require a broader feature surface review.
