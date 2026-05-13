# Visuals Prose Review

Changed prose:
- Reworded the overview from "rendering objects" to "rendering handles" to match `VisualBase` and derived visual classes, which are `BaseHandle`-style public handles.
- Clarified that `Dali::Ui::AnimatedImageVisual` can use one animated resource via `SetResourceUrl` or an image sequence via `SetResourceUrlList`.
- Changed the view ownership paragraph to name `Dali::Ui::View::AddVisual` and fluent `Dali::Ui::View::Visuals`, matching the public view API and the samples.
- Narrowed the implementation-facing note so normal application guidance does not encourage direct use of `Dali::Ui::Internal::VisualBaseImpl` or `Dali::Ui::Internal::Visual::Transform`.
- Added `Dali::Ui::AnimatedImageVisual::Stop` to playback control because it is part of the same public playback API group as `Play`, `Pause`, and `JumpTo`.
- Corrected loop-count guidance: positive values are fixed loop counts, `-1` is infinite looping, and `0` means the animation will not play.
- Clarified that `GetTotalFrameNumber` may return `-1` before image decoding completes.
- Updated example code locally by adding resource URLs and replacing continuous-loop `SetLoopCount(0)` calls with `SetLoopCount(-1)`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/animated-image-visual.h` defines `AnimatedImageVisual::New`, `SetResourceUrl`, `SetResourceUrlList`, typed fluent setters, `Play`, `Pause`, `Stop`, `JumpTo`, getters, and the `Property` constants.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/visual-base.h` states that `VisualBase` attaches to `Dali::Ui::View`, manages attachment/detachment, sibling order, and property updates, and can belong to only one view container range at a time.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` defines `View::Visuals`, `View::AddVisual`, `View::RemoveVisual`, `View::GetVisualCount`, and `View::GetVisualAt`.
- `repos/dali-ui/samples/visual-base/animated-image-visual-example.cpp` shows app-facing use of `View::Visuals(...)` with `ColorVisual::New()` and `AnimatedImageVisual::New()` chained into a view tree.
- `repos/dali-ui/dali-ui-foundation/public-api/animated-image-view.h` and `animated-image-view-properties.h` document the shared loop-count semantics: `-1` for infinite, `0` means the animation will not play, and positive values specify exact loop counts.
- `repos/dali-ui/dali-ui-foundation/public-api/visuals/color-visual.h`, `image-visual.h`, and `lottie-animation-visual.h` confirm the listed property-owner groups.

Remaining concerns:
- The guide still focuses mostly on `AnimatedImageVisual`; it lists `ColorVisual`, `ImageVisual`, and `LottieAnimationVisual` accurately but does not provide equally rich examples for each visual type.
- `Dali::Ui::VisualsContainer` is available under `devel-api`; the revised wording avoids presenting it as the usual application attachment path.
