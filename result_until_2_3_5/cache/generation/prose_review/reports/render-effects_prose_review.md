# Render Effects Prose Review

Changed prose:
- Changed "An effect is assigned to one owner" to "An effect has a single owner" to match `RenderEffect` header language.
- Clarified `Dali::Ui::GaussianBlurEffect::SetBlurRadius` prose to say the effective blur changes in fixed increments, matching the public `New(uint32_t)` and `SetBlurRadius` notes.
- Tightened `Dali::Ui::BackgroundBlurEffect::SetSourceActor` guidance: the source actor is used only when it is a parent of the owner view, and `SetStopperActor` sets where traversal stops.
- Added a localized warning that blur strength and opacity animations are ignored when `SetBlurOnce(true)` is enabled.
- Removed no section and preserved all code blocks.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/render-effect.h`: `RenderEffect` is the common interface; each effect has a single owner `View`; `Activate`, `Deactivate`, `Refresh`, and `IsActivated` are public.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` and `public-api/view-impl.cpp`: `View::SetRenderEffect`, `GetRenderEffect`, and `ClearRenderEffect` are public; setting a new effect clears the previous one.
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/gaussian-blur-effect.h`: Gaussian blur affects the owner view and children; radius/downscale notes describe fixed effective increments; finished signal is tied to `blurOnce`.
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/background-blur-effect.h`: background blur affects the owner view background; hierarchy matters; source/stopper actor setters do not hold actor references.
- `repos/dali-ui/dali-ui-foundation/internal/render-effects/background-blur-effect-impl.cpp`: user source actor is applied only if it is a parent of the owner/source view; stopper actor is passed to `RenderUntil`.
- `repos/dali-ui/dali-ui-foundation/public-api/render-effects/mask-effect.h` and `internal/render-effects/mask-effect-impl.cpp`: mask defaults are `ALPHA`, zero position, one scale; target/source one-shot flags control render task refresh rate.
- `repos/dali-ui/samples/text/text-cutout-mask-example.cpp`: sample uses `View::SetRenderEffect(MaskEffect::New(maskSource))` with dali-ui view-style objects.
- `repos/dali-ui/dali-ui-foundation/internal/render-effects/gaussian-blur-effect-impl.cpp` and `background-blur-effect-impl.cpp`: blur strength and opacity animation values are clamped to `0.0f` to `1.0f`, and animations are ignored when `mBlurOnce` is true.

Remaining concerns:
- The code examples were preserved as requested and reviewed for API shape, but they were not compiled in this read-only review pass.
