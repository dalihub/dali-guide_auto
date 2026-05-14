# Render Effects Prose Review

Changed prose:
- Clarified the overview so it distinguishes blurring a view, blurring the background behind a view, and masking a view.
- Reworded ownership behavior: `ViewImpl::SetRenderEffect()` calls `ClearRenderEffect()` before assigning the new effect.
- Added the `New()` default-constructor overloads for `Dali::Ui::GaussianBlurEffect` and `Dali::Ui::BackgroundBlurEffect`, both documented with default blur radius `10u`.
- Tightened blur downscale prose to match the header note that effective blur radius changes in fixed increments based on downscale factor.
- Clarified `FinishedSignal()` timing: it applies when `blurOnce` is true and the effect is active, and fires after the target actor has been rendered.
- Added background blur source/stopper constraints: `SetSourceActor()` and `SetStopperActor()` take `Dali::Actor`, the effect does not keep strong references, and a source actor outside the source-view parent chain has no effect.
- Corrected mask prose to say the target is “masked” rather than strictly “clipped,” and documented `MaskEffect::New(maskView)` default settings.
- Added the practical requirement from samples that the mask source should be kept in the view tree, commonly as a child of the target.
- Qualified `Activate()` to say it enables the effect when the owner view can be rendered, matching activation validity checks.
- Changed “blur-to-clear” wording to “blurred-to-clear” for clearer English.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/render-effect.h`: `RenderEffect` is the base interface, each effect has a single owner `View`, and exposes `Activate()`, `Deactivate()`, `Refresh()`, and `IsActivated()`.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` and `public-api/view-impl.cpp`: `View::SetRenderEffect()`, `GetRenderEffect()`, and `ClearRenderEffect()` are public; implementation clears the existing effect before assigning a new one.
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/gaussian-blur-effect.h`: Gaussian blur affects the owner view and children, has `New()`, `New(uint32_t)`, typed blur setters/getters, animation APIs, and `FinishedSignal()`.
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/background-blur-effect.h`: background blur affects the owner view background, hierarchy matters, has matching blur APIs, `SetSourceActor()`, `SetStopperActor()`, animation APIs, and `FinishedSignal()`.
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/mask-effect.h`: mask effect masks the owner view; `New(maskView)` defaults to `ALPHA`, zero position, and unit scale; overload accepts `MaskMode`, mask position, and mask scale.
- `repos/dali-ui/samples/text/text-cutout-mask-example.cpp` and `repos/dali-ui/samples/ui-scale/ui-scale-example.cpp`: samples apply effects through `View::SetRenderEffect()` and add mask source views into the target view tree.

Remaining concerns:
- The examples remain intentionally compact and do not show includes, application setup, or layout sizing needed for a complete runnable app.
- The guide mentions valid blur downscale range from public headers, but implementation details do not show runtime clamping in the reviewed snippets.
