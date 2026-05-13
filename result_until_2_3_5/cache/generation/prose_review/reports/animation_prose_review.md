# Animation Prose Review

Changed prose:
- Clarified the opening sentence to make `Dali::Ui::View::Animate` the app-facing entry point with `Dali::Animation` as the playback object.
- Replaced "owned by the `dali-ui` animation surface" with a more precise description of `Dali::Ui::ViewAnimationBridge` as a lightweight bridge that applies typed entries to a supplied `Dali::Animation`.
- Narrowed the raw property mapping paragraph to source-backed property names used by the bridge implementation: `OPACITY`, `POSITION_X`, `POSITION_Y`, `SCALE_X`, and `SCALE_Y`.
- Updated loop-setting prose to note that `SetLooping` and `SetLoopCount` should not be combined, and that `SetLoopCount(0)` repeats forever.
- Slightly tightened label prose to say the label bridge/spec extend the view animation surface with label-specific methods.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` and `view.cpp` expose `Dali::Ui::View::Animate(Animation)` and `Dali::Ui::View::NewAnimationSpec()`.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-bridge.autogen.h` documents `ViewAnimationBridge` as lightweight, stack-allocated, and type-safe, with methods such as `Opacity`, `PositionX`, `PositionY`, `ScaleX`, and `ScaleY`.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-bridge.autogen.cpp` maps typed methods to `Dali::Animation::AnimateTo` / `AnimateBy` on actor and view properties.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-spec.autogen.cpp` shows `ViewAnimationSpec::ApplyTo(Animation, View)` applying stored entries.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/duration.h` confirms `Dali::Ui::Duration`, `InSeconds`, and `InMilliseconds`.
- `repos/dali-core/dali/public-api/animation/animation.h` confirms `Play`, `Pause`, `PlayAfter`, `PlayFrom`, `SetLooping`, `SetLoopCount`, and `SetLoopingMode`, including the reset behavior between `SetLooping` and `SetLoopCount`.
- `repos/dali-ui/dali-ui-foundation/public-api/label.h`, `label.cpp`, `label-animation-bridge.autogen.h`, and `label-animation-spec.autogen.h` confirm label animation bridge/spec APIs and label-specific extension behavior.
- `repos/dali-ui/samples/animation/animation-example.cpp` demonstrates `View::NewAnimationSpec()`, `view.Animate(anim)`, fluent chaining, and `anim.Play()`.

Remaining concerns:
- Code blocks were preserved as requested and checked against public signatures, but they were not compiled in this read-only review pass.
