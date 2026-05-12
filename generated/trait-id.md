---
title: Trait Id
sidebar_label: Trait Id
category: uncategorized
---

# Trait Id

`TraitId` is a lightweight handle representing a unique identifier for traits attached to `View` objects. Use `TraitId::Alloc()` to obtain unique IDs for custom traits.

## Table of Contents

- [Allocating Trait IDs](#allocating-trait-ids)
- [Comparing Trait IDs](#comparing-trait-ids)
- [Using Trait IDs with Views](#using-trait-ids-with-views)

## Allocating Trait IDs

Allocate a unique `TraitId` by calling the static `TraitId::Alloc()` method. Each call returns a new unique identifier backed by an internal atomic counter, making the operation thread-safe and lock-free.

Store allocated IDs in a static variable to ensure each logical trait type receives exactly one ID:

```cpp
// Allocate once per trait type (e.g., in an anonymous namespace or as a static)
static const Dali::Ui::TraitId kMyScrollStateTrait = Dali::Ui::TraitId::Alloc();
static const Dali::Ui::TraitId kMyAnimationTrait = Dali::Ui::TraitId::Alloc();
```

The `value` member provides access to the underlying `uint32_t` identifier:

```cpp
Dali::Ui::TraitId id = Dali::Ui::TraitId::Alloc();
uint32_t rawValue = id.value;
```

## Comparing Trait IDs

`TraitId` supports equality and inequality comparison for use in conditional logic and container lookups:

```cpp
Dali::Ui::TraitId id1 = Dali::Ui::TraitId::Alloc();
Dali::Ui::TraitId id2 = Dali::Ui::TraitId::Alloc();
Dali::Ui::TraitId id3 = id1;

if (id1 == id3) {
  // True: id3 is a copy of id1
}

if (id1 != id2) {
  // True: different allocations yield different IDs
}
```

## Using Trait IDs with Views

`TraitId` values serve as keys for attaching, retrieving, and removing traits from `View` instances. The dali-ui framework uses reserved IDs defined in the `ReservedTraitId` namespace for built-in traits. Application code should allocate custom IDs via `TraitId::Alloc()`.

When implementing custom traits, the `TraitId` is passed to lifecycle callbacks such as `OnBeforeAttached()`, `OnAttached()`, and `OnDetached()`:

```cpp
class MyTraitImpl : public Dali::Ui::Integration::TraitImpl
{
public:
  void OnBeforeAttached(Dali::Ui::TraitId id, Dali::Ui::View& view) override
  {
    // Called before the trait is attached to the view
    // 'id' identifies the trait slot being used
  }

  void OnAttached(Dali::Ui::TraitId id, Dali::Ui::View& view) override
  {
    // Called after the trait is attached to the view
  }

  void OnDetached(Dali::Ui::TraitId id, Dali::Ui::View& view) override
  {
    // Called when the trait is removed from the view
  }
};
```

Allocate static IDs for your custom traits and use them consistently when setting or retrieving traits from views:

```cpp
// In an anonymous namespace or as static file-scope variables
namespace
{
static const Dali::Ui::TraitId kMyCustomTrait = Dali::Ui::TraitId::Alloc();
} // namespace

// Later, when attaching a trait to a view:
Dali::Ui::View view = Dali::Ui::View::New();
MyTrait trait = MyTrait::New();
// Integration API usage for trait attachment
```
