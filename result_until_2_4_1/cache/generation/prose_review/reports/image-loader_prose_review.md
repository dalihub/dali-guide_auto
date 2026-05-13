# Image Loader Prose Review

Changed prose:
- Clarified the overview so `Dali::Ui::AsyncImageLoader` is described as loading pixel data from URLs asynchronously, and `Dali::Ui::ImageUrl` as exposing externally supplied image resources through URL strings.
- Replaced "owns the asynchronous request flow" with a more source-grounded statement that the loader starts image loading work on a worker thread.
- Changed the signal description from "uses the task ID" to "emits the task ID" to match `ImageLoadedSignalType`.
- Changed "The old request may complete" to "An older request may complete" for clearer guide flow.
- Narrowed `CancelAll()` prose from "all pending work owned by the loader" to "all queued work tracked by the loader".
- Revised `ImageUrl` lifetime prose to say destruction requests removal of the external resource URL, while visuals can keep their own resource references.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/async-image-loader.h`: `AsyncImageLoader` loads pixel data from a URL asynchronously, uses a worker thread, returns task IDs from `Load()`, and recommends connecting `ImageLoadedSignal()` before `Load()`.
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/async-image-loader.h`: `ImageLoadedSignalType` is `Signal<void(uint32_t, PixelData)>`, and invalid `PixelData` indicates load failure.
- `repos/dali-ui/dali-ui-foundation/internal/image-loader/async-image-loader-impl.cpp`: `Load()` adds a loading task to `AsyncTaskManager`, stores tracked tasks, `Cancel()` removes tracked tasks, `CancelAll()` removes tracked tasks and clears the list, and the destructor calls `CancelAll()`.
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/image-url.h`: `ImageUrl::New(const EncodedImageBuffer&)`, `ImageUrl::GetUrl()`, copy/move operations, and `DownCast()` are public APIs.
- `repos/dali-ui/dali-ui-foundation/internal/image-loader/image-url-impl.cpp`: `ImageUrl` adds an encoded image buffer to the texture manager and requests external resource removal for its URL in the destructor.
- `repos/dali-ui/dali-ui-foundation/internal/visuals/visual-url.cpp` and `repos/dali-ui/dali-ui-foundation/internal/texture-manager/texture-cache-manager.cpp`: visual resource references can increase and decrease encoded-buffer reference counts.

Remaining concerns:
- The code blocks were preserved as requested and were not compile-tested in this read-only review pass.
- The examples intentionally leave image presentation updates as comments because the allowed image-loader API surface does not include a specific public visual/property binding path for applying `PixelData` to a `Dali::Ui::View`.
