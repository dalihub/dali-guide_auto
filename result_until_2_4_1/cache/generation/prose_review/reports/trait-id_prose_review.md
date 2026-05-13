# Trait Id Prose Review

Changed prose:
- Clarified the overview from "identifier for distinguishing trait types" to "lightweight handle that identifies a logical trait type" to match the public header wording.
- Added `()` to prose references to `Dali::Ui::TraitId::Alloc()` for API clarity.
- Changed "stable identifier" wording to "one identifier during the process lifetime" because `TraitId::Alloc()` is runtime allocation backed by an internal counter.
- Changed "Repeated calls ... intentionally create different identifiers" to "Each call ... returns a new identifier" to align directly with the public API contract.
- Changed "temporary values" to "newly allocated values" because the real pitfall is calling `Alloc()` again, not temporary object lifetime.
- Narrowed raw `value` guidance to diagnostics, logging, and in-memory lookup keys, and added that it should not be treated as a persistent file or configuration ID.
- Replaced "without using raw Actor property idioms" with "without introducing a separate application-level identifier" because the Actor property comparison was not directly evidenced by `TraitId` sources.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h`: `TraitId` is documented as a lightweight handle for unique trait identifiers; extension or application code should allocate IDs with `TraitId::Alloc()` and store them statically so each logical trait gets exactly one ID.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h`: `operator==` and `operator!=` compare `value`.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h`: `value` is a public `uint32_t`.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.cpp`: `TraitId::Alloc()` uses a static atomic counter and returns `counter.fetch_add(1u, std::memory_order_relaxed)`, so each allocation returns the next runtime value.
- `repos/dali-ui/dali-ui-foundation/internal/views/view/view-data-impl.cpp`: trait storage and lookup compare `TraitId` values when setting, getting, and removing traits for a `View`.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-View.cpp`: test trait IDs are allocated once as static constants and reused.

Remaining concerns:
- The guide necessarily stays high-level because `TraitId` itself is a small identifier type; most attach/get/remove behavior is exposed through integration APIs rather than a public application-level trait attachment API.
