# Interactive View Prose Review

Changed prose:
- Replaced "plain dali-ui `View`" with "plain interactive container" to match the public header wording that `Dali::Ui::InteractiveView` can be instantiated directly when a plain interactive container is needed.
- Tightened `SetClickable(false)` guidance to say it prevents tap and key-click activation, not all interaction or object behavior.
- Renamed the small helper example from `SetBusy` to `SetUnavailable` and changed the parameter name to avoid implying busy-state semantics not present in the API.
- Expanded the keyboard policy sentence to specify D-Pad, remote, or keyboard input, matching `Dali::Ui::KeyClickPolicy` documentation.
- Revised pseudo-disabled prose to clarify that it is a visual pseudo-disabled state and can still accept interaction.
- Kept all section structure intact and preserved all code blocks except the localized helper rename described above.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-view.h`: `Dali::Ui::InteractiveView::New`, typed setters, signal callback shapes, chain helpers, and `DownCast`.
- `repos/dali-ui/dali-ui-foundation/public-api/interactive-trait.h`: pseudo-disabled still accepts user interaction; long-press `true` return suppresses `ClickedSignal`.
- `repos/dali-ui/dali-ui-foundation/public-api/key-click-policy.h`: `ON_RELEASE`, `ON_PRESS`, and `DISABLED` meanings for D-Pad, remote, or keyboard input.
- `repos/dali-ui/dali-ui-foundation/integration-api/interactive-trait-impl.cpp`: default `mClickable(true)`, `SetClickable()` gating tap/key click paths, and `SetPseudoDisabled(true)` clearing pressed state when currently pressed.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-InteractiveView.cpp`: UTC coverage for default clickability, setters/getters, signal callbacks, chaining helpers, downcast behavior, pressed changes, and long-press click suppression.

Remaining concerns:
- The guide includes a short derived-handle macro example for `DALI_UI_CHAIN_INTERACTIVEVIEW_METHODS`; it is accurate to the generated header but remains a simplified illustration rather than a full custom handle implementation.
