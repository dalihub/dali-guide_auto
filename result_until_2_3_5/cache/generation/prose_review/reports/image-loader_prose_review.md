# Image Loader Prose Review

Changed prose:
- Clarified that `Dali::Ui::AsyncImageLoader` uses a worker thread, matching the public header.
- Changed “completion can be reported as soon as the load is ready” to “completion can be reported immediately,” matching the header note that the signal may be emitted immediately.
- Replaced “loader defaults for sampling and orientation correction” with the exact defaults: `Dali::SamplingMode::BOX_THEN_LINEAR` and orientation correction enabled.
- Standardized “task id” to “task ID” in prose.
- Tightened `Cancel` wording: `false` means the task is no longer queued, rather than only “already being processed.”
- Clarified `Dali::Ui::ImageUrl` lifetime: destruction of the last handle requests texture-manager removal, and visuals may keep the resource alive until released.
- Replaced the generic handle-destruction paragraph with specific final-handle behavior for `Dali::Ui::AsyncImageLoader` and `Dali::Ui::ImageUrl`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/async-image-loader.h`: `AsyncImageLoader` loads pixel data asynchronously on a worker thread, `Load` returns a task ID, `ImageLoadedSignal` should be connected before `Load`, invalid `PixelData` indicates load errors, `Cancel`, `CancelAll`, and `ImageLoadedSignal` signatures.
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/async-image-loader.cpp`: public `Load` overloads use `SamplingMode::BOX_THEN_LINEAR` and `orientationCorrection = true`; `Cancel`, `CancelAll`, and `ImageLoadedSignal` forward to the implementation.
- `repos/dali-ui/dali-ui-foundation/internal/image-loader/async-image-loader-impl.cpp`: tasks are queued through `AsyncTaskManager`, completed tasks are removed before signal emission, `CancelAll` clears queued tasks, and the internal destructor calls `CancelAll`.
- `repos/dali-ui/dali-ui-foundation/public-api/image-loader/image-url.h`: `ImageUrl` wraps an external buffer, exposes `New`, `GetUrl`, `DownCast`, copy, and move operations, and documents visual-retained buffer lifetime.
- `repos/dali-ui/dali-ui-foundation/internal/image-loader/image-url-impl.cpp`: `ImageUrl` stores a generated URL and requests external-resource removal from the texture manager in its destructor.

Remaining concerns:
- The examples inherit from `Dali::Ui::View` but only include the image-loader headers, so a standalone compilation unit would need the appropriate `View` header from the application’s existing includes.
- The guide intentionally keeps `ImageUrl::New(Texture&, bool)` out of the examples because the draft and feature profile focus on encoded image buffers for app-facing usage.
