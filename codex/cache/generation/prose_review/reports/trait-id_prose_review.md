# Trait Id Prose Review

Changed prose:
- Clarified the overview from "identifier for naming one logical trait" to "lightweight handle that represents a unique identifier for a trait" to match the public header wording.
- Changed "stable name" to "shared identifier" to avoid implying persistence beyond the allocated `TraitId` value.
- Changed "callback or utility" to "helper" because the guide is app-facing and public `TraitId` evidence directly supports comparison in helpers without implying a specific callback API.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h` documents `Dali::Ui::TraitId` as a lightweight handle representing a unique identifier for traits.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h` states that extension or application code should allocate IDs with `TraitId::Alloc()` and store them in a static variable so each logical trait gets exactly one ID.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h` defines `operator==`, `operator!=`, and the public `uint32_t value` member.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.cpp` shows `TraitId::Alloc()` returning values from an internal atomic counter.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-View.cpp` uses test-only trait IDs allocated once and reused across tests.

Remaining concerns:
- The examples intentionally keep `Dali::Ui::View` usage minimal because `TraitId` itself is the public app-facing API in scope; trait storage and reserved trait IDs are mainly integration/internal surfaces.
