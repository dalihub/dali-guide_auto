# Render Effects Prose Review

Changed prose:
- Clarified that `Dali::Ui::RenderEffect` is an interface handle and that application code sets concrete effects with `Dali::Ui::View::SetRenderEffect`.
- Replaced the vague blur-radius optimization note with the documented fixed increment formula, `2 / downscaleFactor`, including the `0.25f` to 8-pixel example.
- Tightened `SetBlurOnce` wording to match the headers: `true` renders once, `false` updates every frame.
- Added the documented ownership constraints: each render effect has one owner `View`, and a view owns at most one render effect.
- Corrected `FinishedSignal` wording to state that it applies when `blurOnce` is true and the effect is activated, then notifies after the target actor has been rendered.
- Updated `SetSourceActor`/`SetStopperActor` guidance to mention the source-actor parent requirement and that the effect does not hold actor references.
- Simplified blur animation prose to match the public parameter documentation for strength and opacity values.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/render-effect.h`: `RenderEffect` is an interface for visual effects, each effect has a single owner `View`, and exposes `Activate`, `Deactivate`, `Refresh`, and `IsActivated`.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: `View::SetRenderEffect`, `GetRenderEffect`, and `ClearRenderEffect` are the public view render-effect APIs.
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/gaussian-blur-effect.h`: `GaussianBlurEffect` blurs the owner view and children; blur radius, downscale factor, one-shot rendering, `FinishedSignal`, and animation value ranges are documented.
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/background-blur-effect.h`: `BackgroundBlurEffect` blurs the owner view's background; tree hierarchy matters; the owner view owns at most one render effect; source and stopper actor constraints are documented.
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/mask-effect.h`: `MaskEffect` masks the owner `View`; `MaskMode::ALPHA` and `MaskMode::LUMINANCE`; source and target mask-once setters are documented.
- `repos/dali-ui/samples/text/text-cutout-mask-example.cpp`: sample usage sets `MaskEffect::New(maskSource)` through `target.SetRenderEffect(...)`.

Remaining concerns:
- The guide still uses compact helper functions that return effects rather than showing full `View::SetRenderEffect` call sites for every effect type. This preserves the draft structure, but a future example-focused pass could add one concise end-to-end view-tree snippet.
