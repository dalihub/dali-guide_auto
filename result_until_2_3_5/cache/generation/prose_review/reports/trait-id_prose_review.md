# Trait Id Prose Review

Changed prose:
- Replaced "small identifier type" and "stable, unique key" with "lightweight handle that represents a unique identifier" to match the public header wording and avoid implying cross-run stability.
- Changed "same trait concept" to "same logical trait" to align with the header guidance.
- Added "thread-safe" to the `Dali::Ui::TraitId::Alloc()` description because the public header documents it.
- Reworded comparison guidance to show normal C++ usage as `==` and `!=` while preserving explicit `Dali::Ui::TraitId::operator==` and `Dali::Ui::TraitId::operator!=` names.
- Replaced "routing code around trait attachment, detachment" with "receives, stores, or looks up trait IDs" to avoid over-emphasizing integration-level attachment APIs in an app-facing guide.
- Reworded the `value` section to recommend direct `==` and `!=` comparisons without requiring users to think in operator function names.

Source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h` documents `Dali::Ui::TraitId` as a lightweight handle representing a unique identifier for traits.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h` says `TraitId` values are allocated sequentially with `Dali::Ui::TraitId::Alloc()`, and application or extension code should allocate once per logical trait and store the result statically.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h` documents `Dali::Ui::TraitId::Alloc()` as using an internal atomic counter, thread-safe and lock-free.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.cpp` implements allocation with a static `std::atomic<uint32_t>` and `fetch_add(1u, std::memory_order_relaxed)`.
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h` implements `Dali::Ui::TraitId::operator==` and `Dali::Ui::TraitId::operator!=` by comparing `value`.
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-View.cpp` allocates static test-only trait IDs once and reuses them.

Remaining concerns:
- The guide intentionally does not show trait attachment APIs because the available attachment, lookup, and removal functions are in integration/internal surfaces rather than the primary app-facing dali-ui public surface for this feature.
- Code blocks were preserved because they use only public `Dali::Ui::TraitId` APIs and remain consistent with the source.
