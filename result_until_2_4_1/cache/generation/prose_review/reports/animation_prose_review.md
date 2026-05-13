# Animation Prose Review

Changed prose:
- Described `Dali::Ui::ViewAnimationBridge` as a "lightweight, stack-allocated proxy" instead of only "short-lived", matching the public header language.
- Clarified that `Dali::Ui::ViewAnimationSpec::ApplyTo` applies stored entries to the provided `Dali::Animation`.
- Added that `Dali::Ui::LabelAnimationBridge` and `Dali::Ui::LabelAnimationSpec` are created through `Dali::Ui::Label::Animate` and `Dali::Ui::Label::NewAnimationSpec`.
- Added timing behavior: bridge/spec entries use delay plus duration as required animation time and extend the supplied `Dali::Animation` if needed.
- Added `Stop` to the playback-control method list.
- Corrected property ownership examples from broad `POSITION`/`SCALE` wording to the actual axis properties used by typed view animation calls: `POSITION_X`, `POSITION_Y`, `SCALE_X`, and `SCALE_Y`, while keeping `OPACITY`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: `View::Animate(Animation)` returns `ViewAnimationBridge`; `View::NewAnimationSpec()` returns `ViewAnimationSpec`.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-bridge.autogen.h`: bridge is documented as lightweight, stack-allocated, and fluent; includes `Opacity`, `PositionX`, `PositionY`, `ScaleX`, `ScaleY`, and relative `By` methods.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-spec.autogen.h` and `.cpp`: `ViewAnimationSpec` stores reusable entries and `ApplyTo(Animation, View)` applies them.
- `repos/dali-ui/dali-ui-foundation/internal/animation/view-animation-spec-impl.cpp`: `ApplyEntries` and bridge helpers extend animation duration when `delay + duration` exceeds the current duration.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/duration.h`: `Duration` stores seconds and exposes `InSeconds` and `InMilliseconds`.
- `repos/dali-core/dali/public-api/animation/alpha-function.h`: default built-in alpha function is linear; easing constants include `EASE_IN`, `EASE_OUT`, and `EASE_IN_OUT`.
- `repos/dali-core/dali/public-api/animation/animation.h`: playback APIs include `Play`, `Pause`, `Stop`, `PlayFrom`, `PlayAfter`, loop controls, speed/play range/end action controls, `Clear`, getters, and `FinishedSignal`.
- `repos/dali-ui/samples/animation/animation-example.cpp`: demonstrates `View::Animate`, `View::NewAnimationSpec`, `ApplyTo`, `AlphaFunction`, and `Animation::Play`.

Remaining concerns:
- Code blocks were preserved except where no change was needed; they were reviewed against public headers and sample usage.
- The guide intentionally stays focused on app-facing `Dali::Ui::View` animation and does not cover lower-level `Dali::Animation::AnimateTo`, `AnimateBy`, `KeyFrames`, `Path`, or constraint APIs.
