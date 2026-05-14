# View Prose Review

Changed prose:
- Clarified that `GetCurrentSize` reads the current rendered size after layout, matching `view.h`.
- Reworded `LayoutMode::STANDALONE` guidance to say the requested position is applied directly, matching `LayoutMode` docs and samples that use `SetRequestedPositionX` / `SetRequestedPositionY`.
- Reworded `AsInteractive` guidance from "attach" to "ensure" because `AsInteractive` creates or reuses the interaction trait through `EnsureInteractiveTrait`.
- Clarified long-press consumption: returning `true` prevents the following click signal from being emitted, matching `InteractiveTrait::LongPressedSignal` notes.
- Added the `SelectableTrait::EnableToggleByClick` behavior that creates an `InteractiveTrait` when needed, matching `selectable-trait.h`.
- Simplified the `Animate` prose to the verified public behavior: `View::Animate` returns a typed `ViewAnimationBridge` targeting a `Dali::Ui::View`.
- Updated attachment prose and example to show `SetAttachment` before `GetAttachment`, matching the public attachment API ownership model.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`: `View::New`, fluent setters, `Children`, `AsInteractive`, `AsSelectable`, `SetLayoutMode`, `GetCurrentSize`, `GetEffectiveLayoutDirection`, `SetAttachment`, `GetAttachment`, `Animate`, and `NewAnimationSpec`.
- `repos/dali-ui/dali-ui-foundation/public-api/layouts/layout-types.h`: `LayoutMode::STANDALONE` uses `SetRequestedPositionX` / `SetRequestedPositionY`.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-trait.h`: signal signatures and long-press click suppression note.
- `repos/dali-ui/dali-ui-foundation/public-api/selectable-trait.h`: selection state, toggle-by-click behavior, and automatic interactive trait creation.
- `repos/dali-ui/dali-ui-foundation/public-api/animation/view-animation-bridge.autogen.h` and `view-animation-spec.autogen.h`: typed animation bridge/spec APIs.
- `repos/dali-ui/samples/animation/animation-example.cpp`, `repos/dali-ui/samples/absolutelayout/absolutelayout-layout-direction-example.cpp`, and `repos/dali-ui/samples/attachment/attachment-example.cpp`: app-facing usage patterns.

Remaining concerns:
- The guide keeps concise examples and does not cover advanced focus, accessibility, visual containers, or custom measure/arrange APIs.
- Some upstream header comments around `ClickedSignal` callback return types and standalone positioning wording appear inconsistent with sample usage or enum comments; this guide follows the public signatures and the clearest source-backed usage.
