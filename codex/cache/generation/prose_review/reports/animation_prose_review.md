# Animation Prose Review

Changed prose:
- Clarified the default easing behavior for typed `Dali::Ui::ViewAnimationBridge` and `Dali::Ui::ViewAnimationSpec` entries. The draft said omitted entry easing would use `Dali::Animation::SetDefaultAlphaFunction`; source shows typed bridge/spec methods default the `AlphaFunction` parameter to `AlphaFunction()` and pass it explicitly to `Dali::Animation`.
- Replaced the `UseDefaultEasing` example with `UseExplicitEasing`, because the original code set `Dali::Animation::SetDefaultAlphaFunction` and then used typed bridge calls that pass an explicit default `Dali::AlphaFunction`.
- Added that the default disconnect action is `Dali::Animation::BAKE_FINAL`.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-bridge.autogen.h` declares typed methods such as `Opacity`, `PositionX`, `ScaleX`, and `ScaleY` with `AlphaFunction alpha = AlphaFunction()`.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-bridge.autogen.cpp` forwards that `alpha` argument to `Dali::Animation::AnimateTo` or `AnimateBy` with a `TimePeriod`.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-spec.autogen.h` and `repos/dali-ui/dali-ui-foundation/internal/animation/view-animation-spec-impl.cpp` show the same typed-entry alpha and delay model for reusable specs.
- `repos/dali-core/dali/public-api/animation/alpha-function.h` documents `Dali::AlphaFunction::DEFAULT` as linear and `AlphaFunction()` as the default built-in alpha function.
- `repos/dali-core/dali/public-api/animation/animation.h` documents `SetDefaultAlphaFunction` for individual property animations when no further alpha functions are supplied, `SetLoopCount(0)` as continuous looping, `SetPlayRange`, `SetSpeedFactor`, `SetEndAction`, `SetDisconnectAction`, and `Clear`.
- `repos/dali-core/dali/internal/event/animation/animation-impl.cpp` defines `DEFAULT_DISCONNECT_ACTION` as `Dali::Animation::BAKE_FINAL`.
- `repos/dali-ui/samples/animation/animation-example.cpp` confirms the app-facing bridge and spec patterns with `Dali::Ui::View::Animate`, `Dali::Ui::View::NewAnimationSpec`, `ApplyTo`, and `Play`.

Remaining concerns:
- Code examples were reviewed against the public headers and implementation behavior, but they were not compiled in this read-only review pass.
