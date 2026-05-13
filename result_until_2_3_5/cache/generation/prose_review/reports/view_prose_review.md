# View Prose Review

Changed prose:
- Tightened the opening description from “base app-facing UI object” to “base app-facing UI component handle” to match `View` as a handle class for a UI component.
- Clarified that `Dali::Ui::View` is the app-facing model for UI component handles, while lower-level DALi object concepts are background or integration concerns.
- Changed “visual shape and outline state” to “corner and borderline state” to match the specific `View` getters and property names.
- Changed the long-press note from “consumes the long press” to “suppresses the subsequent click,” matching the public `LongPressedSignal()` and `ConnectLongPressedSignal()` documentation.
- Narrowed the property-identifier guidance by replacing “renderer and accessibility infrastructure” with “accessibility metadata,” keeping the paragraph app-facing.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: `View::New()`, copy handle semantics, `GetChildCount()`, `GetChildAt()`, typed getters, `AsInteractive()`, `AsSelectable()`, `Animate()`, `NewAnimationSpec()`, and `View::Property`.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-trait.h`: signal signatures, `SetClickable()`, key click policy accessors, pseudo-disabled behavior, and long-press suppression semantics.
- `repos/dali-ui/dali-ui-foundation/public-api/selectable-trait.h`: selected state, toggle-by-click behavior, `SelectionChangedSignal()` signature, and coexistence with `InteractiveTrait`.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-spec.autogen.h`: reusable animation spec methods and `ApplyTo(Animation, View)`.
- `repos/dali-ui/samples/animation/animation-example.cpp`: app-facing use of `View::Animate()`, `View::NewAnimationSpec()`, `InteractiveView`, and fluent view setters.

Remaining concerns:
- The draft examples were preserved as requested. I did not compile the snippets in isolation.
- Some public header comments contain minor wording issues, but the guide prose was normalized to clear English without copying those issues.
