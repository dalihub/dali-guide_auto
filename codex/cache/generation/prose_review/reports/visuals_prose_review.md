# Visuals Prose Review

Changed prose:
- Narrowed the overview from “animated image sequences” to “animated image content” because `Dali::Ui::AnimatedImageVisual` supports both single animated resources via `SetResourceUrl` and URL-list sequences via `SetResourceUrlList`.
- Updated setup examples so static `ImageVisual` and animated `AnimatedImageVisual` handles include resource URLs, and removed `SetFrameDelay` from single-resource GIF/WebP examples.
- Reworded the view attachment section to emphasize public `Dali::Ui::View::AddVisual`, `Visuals`, `RemoveVisual`, `GetVisualAt`, and `GetVisualCount` APIs instead of presenting `Dali::Ui::VisualsContainer` as the app-facing model.
- Expanded the `ImageVisual::Property` description to include properties present in the public header, including `PRE_MULTIPLIED_ALPHA`, `FAST_TRACK_UPLOADING`, and n-patch properties.
- Corrected `PIXEL_AREA` guidance to describe normalized `(x, y, width, height)` values and changed the cropped-image example from pixel-sized values to normalized values.
- Added `Stop` to the animated-image playback API list because it is a public `AnimatedImageVisual` method.
- Clarified that `SetFrameDelay` applies to URL-list image sequences.
- Reworded “keep the handle returned from `AddVisual` setup” because `AddVisual` returns `bool`; the app keeps the visual handle it created before adding it.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/image-visual.h` defines `ImageVisual::New`, typed setters including `SetResourceUrl` and `SetPixelArea`, and the `ImageVisual::Property` values.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/animated-image-visual.h` defines `SetResourceUrl`, `SetResourceUrlList`, `SetFrameDelay`, `Play`, `Pause`, `Stop`, `JumpTo`, and the playback getters.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/lottie-animation-visual.h` defines `LottieAnimationVisual::Property`, `SetPlayRange`, playback methods, and Lottie-specific properties.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` documents `View::AddVisual`, `View::Visuals`, `RemoveVisual`, `GetVisualCount`, and `GetVisualAt`; `AddVisual` returns `bool`.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/image-visual-properties.h` documents `PIXEL_AREA` as a rectangular `VECTOR4` with default `[0.0, 0.0, 1.0, 1.0]`.
- `repos/dali-ui/samples/image-view/image-pixel-area-example.cpp` describes `PixelArea` as normalized `(x, y, width, height)` in `[0, 1]`.
- `repos/dali-ui/samples/visual-base/animated-image-visual-example.cpp`, `image-visual-example.cpp`, and `color-visual-example.cpp` show view-based visual usage with `AddVisual` and `Visuals`.

Remaining concerns:
- The code blocks are preserved as guide snippets and were not compiled in this review.
- The guide intentionally avoids a full API inventory; it keeps feature-specific app guidance and cites representative public properties and typed setters.
