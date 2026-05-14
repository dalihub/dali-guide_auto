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

## Comparing TraitId Values

`TraitId` provides equality and inequality operators for comparing identifier values. Use these to verify trait identity when working with trait identifiers.

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

The framework reserves a set of predefined `TraitId` constants in the `Dali::Ui::Integration::ReservedTraitId` namespace for internal use. These identifiers support built-in framework features such as layout parameters and interaction handling.

Framework-reserved identifiers include:

- `Dali::Ui::Integration::ReservedTraitId::INTERACTION_TRAIT` — Interaction behavior trait
- `Dali::Ui::Integration::ReservedTraitId::SELECTABLE_TRAIT` — Selection state trait
- `Dali::Ui::Integration::ReservedTraitId::STATE_HANDLER_TRAIT` — State handling trait
- `Dali::Ui::Integration::ReservedTraitId::ABSOLUTE_LAYOUT_PARAMS` — Absolute layout parameters
- `Dali::Ui::Integration::ReservedTraitId::STACK_LAYOUT_PARAMS` — Stack layout parameters
- `Dali::Ui::Integration::ReservedTraitId::GRID_LAYOUT_PARAMS` — Grid layout parameters
- `Dali::Ui::Integration::ReservedTraitId::FLEX_LAYOUT_PARAMS` — Flex layout parameters
- `Dali::Ui::Integration::ReservedTraitId::LAYOUT_MANAGER` — Layout manager trait
- `Dali::Ui::Integration::ReservedTraitId::LAYOUT_SIGNALS` — Layout signal trait
- `Dali::Ui::Integration::ReservedTraitId::INTERACTION_EFFECT` — Interaction effect trait
- `Dali::Ui::Integration::ReservedTraitId::INTERACTION_EFFECT_DATA` — Interaction effect data

Application code should allocate custom `TraitId` values via `TraitId::Alloc()` rather than using reserved identifiers.
