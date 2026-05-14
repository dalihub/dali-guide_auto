---
title: Trait Id
sidebar_label: Trait Id
category: views-components
---

# Trait Id

`TraitId` is a lightweight handle representing a unique identifier for traits attached to `View` objects. Each trait type requires a unique `TraitId` to distinguish it from other traits on the same view.

## Table of Contents

- [Allocating Trait Identifiers](#allocating-trait-identifiers)
- [Comparing TraitId Values](#comparing-traitid-values)
- [Reserved Trait IDs](#reserved-trait-ids)

## Allocating Trait Identifiers

Use `TraitId::Alloc()` to allocate a unique identifier for each custom trait type. Call this method once per trait type and store the result in a static variable to ensure consistent identification throughout the application lifetime.

```cpp
// Allocate a unique ID for a custom trait (typically in an anonymous namespace)
static const TraitId kMyCustomTrait = TraitId::Alloc();
```

The `TraitId::Alloc()` method is thread-safe and lock-free, using an internal atomic counter. Each call returns a new unique `TraitId` value.

When implementing a custom trait, pass the allocated `TraitId` to the trait system during attachment:

```cpp
// Inside a trait implementation's lifecycle callback
void OnAttached(TraitId id, View& view) override
{
  // Store the id for later use
  mLastId = id;
}
```

## Comparing TraitId Values

`TraitId` provides equality and inequality operators for comparing identifier values. Use these to verify trait identity in lifecycle callbacks or when retrieving traits from a view.

```cpp
TraitId id1 = TraitId::Alloc();
TraitId id2 = TraitId::Alloc();
TraitId id3 = id1;

// Compare for equality
if (id1 == id3)
{
  // id1 and id3 have the same value
}

// Compare for inequality
if (id1 != id2)
{
  // id1 and id2 have different values (each Alloc() returns a unique ID)
}
```

Access the underlying numeric value through the `value` member when logging or debugging:

```cpp
TraitId id = TraitId::Alloc();
uint32_t numericValue = id.value;
```

## Reserved Trait IDs

The framework reserves a set of predefined `TraitId` constants in the `ReservedTraitId` namespace for internal use. These identifiers support built-in framework features such as layout parameters and interaction handling.

Framework-reserved identifiers include:

- `ReservedTraitId::INTERACTION_TRAIT` — Interaction behavior trait
- `ReservedTraitId::SELECTABLE_TRAIT` — Selection state trait
- `ReservedTraitId::STATE_HANDLER_TRAIT` — State handling trait
- `ReservedTraitId::ABSOLUTE_LAYOUT_PARAMS` — Absolute layout parameters
- `ReservedTraitId::STACK_LAYOUT_PARAMS` — Stack layout parameters
- `ReservedTraitId::GRID_LAYOUT_PARAMS` — Grid layout parameters
- `ReservedTraitId::FLEX_LAYOUT_PARAMS` — Flex layout parameters
- `ReservedTraitId::LAYOUT_MANAGER` — Layout manager trait
- `ReservedTraitId::LAYOUT_SIGNALS` — Layout signal trait
- `ReservedTraitId::INTERACTION_EFFECT` — Interaction effect trait
- `ReservedTraitId::INTERACTION_EFFECT_DATA` — Interaction effect data

Application code should allocate custom `TraitId` values via `TraitId::Alloc()` rather than using reserved identifiers.