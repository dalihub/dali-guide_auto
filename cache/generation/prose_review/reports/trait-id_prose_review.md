# Trait Id Prose Review

## Summary

Reviewed the Trait Id guide draft against source evidence from:
- `repos/dali-ui/dali-ui-foundation/public-api/trait-id.h` (public API)
- `repos/dali-ui/dali-ui-foundation/integration-api/reserved-trait-id.h` (reserved IDs)
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-Trait.cpp` (UTC usage patterns)
- `repos/dali-ui/dali-ui-foundation/public-api/trait.h` (Trait handle)
- `repos/dali-ui/dali-ui-foundation/integration-api/trait-impl.h` (TraitImpl base class)

## Changes Made

### 1. Removed Integration-Level Lifecycle Callback Example

**Original prose (removed):**
> When implementing a custom trait, pass the allocated `TraitId` to the trait system during attachment:
> ```cpp
> // Inside a trait implementation's lifecycle callback
> void OnAttached(TraitId id, View& view) override
> {
>   // Store the id for later use
>   mLastId = id;
> }
> ```

**Reason:** `TraitImpl` and its lifecycle callbacks (`OnAttached`, `OnDetached`, etc.) are in the `Dali::Ui::Integration` namespace, which is integration-level API not intended for application developers. The public `Trait` class is a handle that does not expose lifecycle callbacks. Application developers use pre-built traits like `InteractiveTrait` and `SelectableTrait` via View methods like `AsInteractive()` and `AsSelectable()`, or allocate `TraitId` values for custom trait identification.

**Source evidence:** 
- `trait-impl.h` shows `TraitImpl` is in `Dali::Ui::Integration` namespace
- `trait.h` shows public `Trait` class has no lifecycle callback methods
- UTC tests use `IntegrationView::SetTrait()` which requires `ViewImpl&` access

### 2. Corrected ReservedTraitId Namespace Qualification

**Original prose:**
> The framework reserves a set of predefined `TraitId` constants in the `ReservedTraitId` namespace for internal use.

**Revised prose:**
> The framework reserves a set of predefined `TraitId` constants in the `Dali::Ui::Integration::ReservedTraitId` namespace for internal use.

**Reason:** The full namespace path is `Dali::Ui::Integration::ReservedTraitId`, not just `ReservedTraitId`. Accuracy requires the fully-qualified name.

**Source evidence:** `reserved-trait-id.h` line 21: `namespace ReservedTraitId` is nested inside `namespace Dali { namespace Ui { namespace Integration {`

### 3. Updated Reserved ID List with Full Qualification

**Original:** Listed IDs as `ReservedTraitId::INTERACTION_TRAIT`, etc.

**Revised:** Listed IDs as `Dali::Ui::Integration::ReservedTraitId::INTERACTION_TRAIT`, etc.

**Reason:** Consistency with the corrected namespace and accuracy for developers who may need to reference these symbols.

### 4. Simplified Comparison Section Prose

**Original prose:**
> Use these to verify trait identity in lifecycle callbacks or when retrieving traits from a view.

**Revised prose:**
> Use these to verify trait identity when working with trait identifiers.

**Reason:** Removed reference to "lifecycle callbacks" since those are integration-level APIs. The simpler phrasing remains accurate for all use cases.

## Preserved Content

- Document structure (overview, TOC, three main sections)
- Code blocks for `TraitId::Alloc()` usage
- Code blocks for equality/inequality operators
- Code block for accessing `value` member
- Thread-safety description of `Alloc()`
- Recommendation to use `TraitId::Alloc()` for custom traits

## Remaining Concerns

None. The guide now accurately reflects the public API surface for application developers using dali-ui.

## Verification

- All API names verified against public headers
- Namespace qualifications verified against source
- Code examples compile against public API (conceptually)
- No non-English prose present in original or revised document
