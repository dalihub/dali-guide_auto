# Interactive View Prose Review

Changed prose:
- Refined the overview to say `Dali::Ui::InteractiveView` has built-in interactive behavior, matching the public header description.
- Changed click prose from “when the view is clicked” to “when the view emits a click” to avoid implying only pointer/tap input.
- Narrowed pressed-state prose from “pointer, touch, key, or gesture interaction” to “touch or key interaction,” matching public `InteractiveTrait` docs and implementation behavior.
- Changed clickability prose from “allow or block click handling” to “allow or block click emission,” because `SetClickable` gates click and key long-press emission but does not fully disable all touch state handling.
- Rewrote pseudo-disabled prose to state that it is a visually disabled state that still allows user interaction, and that `PseudoDisabledChangedSignal` emits only when `SetPseudoDisabled` changes the state.
- Changed key activation prose from broad “key input” wording to “D-Pad, remote, or keyboard input,” matching `KeyClickPolicy` documentation.
- Changed “derived child class” to “derived class” in the generated chaining section for clearer flow.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-view.h`: `InteractiveView` is a `View` subclass, `New`, `DownCast`, typed setters, signal signatures, and chaining helpers are public.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-trait.h`: documents pressed changes for touch or keyboard input, pseudo-disabled as visually disabled while still accepting interaction, clicked and long-pressed signal behavior, and callback shapes.
- `repos/dali-ui/dali-ui-foundation/public-api/key-click-policy.h`: documents `ON_RELEASE`, `ON_PRESS`, and `DISABLED` for D-Pad, remote, or keyboard interactions.
- `repos/dali-ui/dali-ui-foundation/integration-api/interactive-trait-impl.cpp`: confirms default clickable state, pseudo-disabled change emission, pressed clearing on pseudo-disable, click gating by `mClickable`, key click policy handling, and long-press click suppression.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-InteractiveView.cpp`: confirms setters/getters, signal emissions, chaining helpers, multiple click handlers, downcast behavior, and long-press suppression.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-view.autogen.h`: confirms generated fluent methods for derived classes.

Remaining concerns:
- Code blocks were preserved because they use public `dali-ui` APIs and remain consistent with the headers. The examples do not show adding the view to a scene or setting geometry, so they demonstrate API usage rather than complete runnable input tests.
