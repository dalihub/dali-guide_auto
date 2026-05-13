# View Prose Review

Changed prose:
- Clarified `Dali::Ui::View::New` as creating an initialized general-purpose UI object, matching the public header.
- Changed “underlying UI object” to “underlying view object” for handle-copy prose, matching the `View` copy-constructor documentation.
- Tightened the inspection sentence from “current view identity” to “view identity” because `GetName()` is not a current animated value.
- Changed the `DownCast` paragraph to refer to a generic `Dali::BaseHandle` and preserved the condition that a valid `Dali::Ui::View` is returned only for view handles.
- Changed `InteractiveTrait` prose from “owns” to “provides” interaction behavior, matching the trait header’s behavior-oriented description.
- Shortened long-press prose from “consumes the long press and suppresses the following click” to “suppresses the following click,” which is the public header’s documented effect.
- Changed `SelectableTrait` prose from “owns selected state” to “manages selected state,” matching the public header wording.
- Added that enabling toggle-by-click creates an `InteractiveTrait` if the owner view does not already have one.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: `View::New`, handle copy behavior, `DownCast`, child lookup, layout/current state getters, `AsInteractive`, `AsSelectable`, `Animate`, and `NewAnimationSpec`.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-trait.h`: click, pressed, long-press, pseudo-disabled, key-click APIs and signal signatures.
- `repos/dali-ui/dali-ui-foundation/public-api/selectable-trait.h`: selected state, toggle-by-click behavior, coexistence with `InteractiveTrait`, and automatic interactive trait creation.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-bridge.autogen.h`: fluent animation bridge methods for opacity, position, background color, corner radius, corner squareness, and borderline properties.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-spec.autogen.h`: reusable `ViewAnimationSpec` and `ApplyTo(Animation, View)`.
- `repos/dali-ui/samples/animation/animation-example.cpp`: app-facing use of `View::Animate`, `View::NewAnimationSpec`, and `ApplyTo`.

Remaining concerns:
- The draft keeps the original code block structure as requested. Some examples omit application setup and `ConnectionTracker` inheritance used by repository samples, but the shown API calls match public headers and remain guide-local snippets rather than full applications.
- I did not add focus, accessibility, or layout parameter examples because that would expand the guide beyond localized prose review.
