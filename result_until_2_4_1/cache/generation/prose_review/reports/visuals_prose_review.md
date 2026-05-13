# Visuals Prose Review

Changed prose:
- Reworded the overview from "rendering objects" to "content objects" to match `Dali::Ui::VisualBase` as the attachable public handle.
- Clarified `Dali::Ui::Visual::ContainerRangeType` using the exact public ranges: background effect, background, content, decoration, and foreground effect.
- Added the `Dali::Ui::View::Visuals` assertion/error-handling note while preserving the declarative example.
- Tightened `Dali::Ui::ImageVisual` loading prose without changing the examples.
- Corrected `Dali::Ui::CutoutPolicy::CUTOUT_OUTSIDE_WITH_CORNER_RADIUS`: it cuts out the outside area of the view including the corner radius; it does not "keep the effect inside" by itself.
- Clarified that `Dali::Ui::AnimatedImageVisual::SetResourceUrl` and `SetResourceUrlList` are mutually exclusive, and that `SetFrameDelay` is relevant to URL-list sequences.
- Replaced the broad "animation controls" claim for `Dali::Ui::LottieAnimationVisual` with named supported playback setters.
- Clarified that `CONTENT_INFO` and `MARKER_INFO` can be read through `GetContentInfo`, `GetMarkerInfo`, or indexed property reads.
- Added that moving a `Dali::Ui::VisualBase` to another container range also detaches it from the previous range, and that sibling-order APIs operate only within the same container range.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/visual-base.h`: documents `VisualBase` attachment to `Dali::Ui::View`, single-owner/container behavior, `GetOwner`, `GetContainerRangeType`, `Detach`, and same-container sibling ordering.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: documents `AddVisual`, `RemoveVisual`, `GetVisualCount`, `GetVisualAt`, and `View::Visuals` assertion behavior.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/visual-properties.h`: defines the public `ContainerRangeType` values.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/image-visual.h` and `image-visual-properties.h`: support `SetResourceUrl`, image properties, N-patch setters, and mandatory `URL`.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/color-visual-properties.h`: defines `CutoutPolicy` semantics.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/animated-image-visual.h`: documents `SetResourceUrl`, `SetResourceUrlList`, mutual exclusivity, `SetPlayRange`, and `SetFrameDelay`.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/lottie-animation-visual.h`: documents Lottie playback setters, `GetContentInfo`, `GetMarkerInfo`, and read-only metadata.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-VisualBase.cpp`, `utc-Dali-ImageVisual.cpp`, `utc-Dali-AnimatedImageVisual.cpp`, and `utc-Dali-LottieAnimationVisual.cpp`: confirm the public API usage patterns used by the examples.

Remaining concerns:
- Code blocks were preserved as requested. I did not compile the snippets in isolation.
- `UseLottieMarkers` assumes the named markers exist in the loaded Lottie file; the public `PLAY_RANGE` property supports marker strings, but runtime behavior depends on the resource metadata.
