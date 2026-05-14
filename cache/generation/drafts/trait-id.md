---
title: Trait Id
sidebar_label: Trait Id
category: views-components
---

# Trait Id

TraitId is a lightweight identifier used to uniquely distinguish trait types attached to a View. Each trait type receives a unique ID via `TraitId::Alloc()`, enabling the framework to manage trait lifecycle and attachment.

## Table of Contents

- [Allocating Trait IDs](#allocating-trait-ids)
- [Comparing Trait IDs](#comparing-trait-ids)
- [Using Trait IDs with Traits](#using-trait-ids-with-traits)

## Allocating Trait IDs

The `TraitId::Alloc()` static method allocates a new unique identifier. This method is thread-safe and lock-free, using an internal atomic counter. Allocate IDs once per logical trait type and store them statically.

```cpp
// Allocate a unique ID for a custom trait (typically in an anonymous namespace or as a static)
static const TraitId kMyScrollStateTrait = TraitId::Alloc();
```

Each call to `TraitId::Alloc()` returns a new unique `TraitId`. The framework guarantees that allocated IDs do not conflict with each other.

```cpp
// Each trait type gets its own unique ID
static const TraitId kInputValidationTrait = TraitId::Alloc();
static const TraitId kAnimationStateTrait = TraitId::Alloc();

// These will have different values
DALI_ASSERT_ALWAYS(kInputValidationTrait != kAnimationStateTrait);
```

## Comparing Trait IDs

`TraitId` provides equality and inequality operators for comparing identifiers. Use these to check if a given ID matches an expected trait type.

```cpp
TraitId id1 = TraitId::Alloc();
TraitId id2 = TraitId::Alloc();
TraitId id1Copy = id1;

// Equality comparison
if (id1 == id1Copy)
{
  // Same ID value
}

// Inequality comparison
if (id1 != id2)
{
  // Different IDs
}
```

The `value` member provides direct access to the underlying `uint32_t` value, primarily for debugging or logging purposes.

```cpp
TraitId id = TraitId::Alloc();
uint32_t rawValue = id.value;  // Access the raw numeric value
```

## Using Trait IDs with Traits

`TraitId` is passed to trait lifecycle callbacks when a `Trait` is attached to or detached from a `View`. This allows trait implementations to identify which trait type triggered the callback.

```cpp
class MyTraitImpl : public Dali::Ui::Integration::TraitImpl
{
public:
  void OnAttached(Dali::Ui::TraitId id, Dali::Ui::View& view) override
  {
    // The id parameter identifies this trait's unique identifier
    if (id == kMyCustomTrait)
    {
      // Perform attachment logic
    }
  }

  void OnDetached(Dali::Ui::TraitId id, Dali::Ui::View& view) override
  {
    // Handle detachment
  }
};
```

Framework-reserved trait IDs are defined in the `Dali::Ui::Integration::ReservedTraitId` namespace. Application code should allocate custom IDs using `TraitId::Alloc()` rather than using reserved IDs.

```cpp
// Example: Framework-reserved IDs defined in ReservedTraitId namespace
// (Do not use these for custom traits; allocate your own with TraitId::Alloc())
namespace Dali::Ui::Integration::ReservedTraitId
{
  extern const TraitId INTERACTION_TRAIT;
  extern const TraitId SELECTABLE_TRAIT;
  extern const TraitId STATE_HANDLER_TRAIT;
  extern const TraitId LAYOUT_MANAGER;
  // ... and others
}