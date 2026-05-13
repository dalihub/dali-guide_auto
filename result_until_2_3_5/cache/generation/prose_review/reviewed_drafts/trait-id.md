---
title: Trait Id
sidebar_label: Trait Id
category: utilities
---

# Trait Id

`Dali::Ui::TraitId` is a lightweight handle that represents a unique identifier for a dali-ui trait.

## Table of Contents

- [Allocate One Identifier Per Trait](#allocate-one-identifier-per-trait)
- [Compare Trait Identifiers](#compare-trait-identifiers)
- [Use the Numeric Value Only for Diagnostics](#use-the-numeric-value-only-for-diagnostics)

## Allocate One Identifier Per Trait

Use `Dali::Ui::TraitId::Alloc()` once for each logical trait type that your dali-ui application or extension owns. Store the result in a static variable so every `Dali::Ui::View` that uses the same logical trait refers to the same identifier.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kCardSelectionTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kCardValidationTraitId = Dali::Ui::TraitId::Alloc();
} // namespace
```

`Dali::Ui::TraitId::Alloc()` returns a new `Dali::Ui::TraitId`. The public header documents that allocation uses an internal atomic counter, is thread-safe, and returns the next unique identifier.

Prefer one static `Dali::Ui::TraitId` per trait role:

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace MyApp
{
namespace
{
const Dali::Ui::TraitId kNavigationStateTraitId = Dali::Ui::TraitId::Alloc();
}

Dali::Ui::TraitId GetNavigationStateTraitId()
{
  return kNavigationStateTraitId;
}
} // namespace MyApp
```

Avoid allocating a new `Dali::Ui::TraitId` each time you build a view. Repeated allocation creates different identifiers for the same logical trait, which prevents later code from matching that trait by ID.

## Compare Trait Identifiers

Use `==` (`Dali::Ui::TraitId::operator==`) and `!=` (`Dali::Ui::TraitId::operator!=`) to test whether two IDs refer to the same trait role.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kEditableTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kFocusableTraitId = Dali::Ui::TraitId::Alloc();
}

bool IsEditableTrait(Dali::Ui::TraitId id)
{
  return id == kEditableTraitId;
}

bool IsNotFocusTrait(Dali::Ui::TraitId id)
{
  return id != kFocusableTraitId;
}
```

Comparisons are value comparisons between `Dali::Ui::TraitId` instances. This makes them suitable for lightweight routing code that receives, stores, or looks up trait IDs for `Dali::Ui::View`-based behavior.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kPrimaryActionTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kSecondaryActionTraitId = Dali::Ui::TraitId::Alloc();
}

const char* GetActionTraitName(Dali::Ui::TraitId id)
{
  if(id == kPrimaryActionTraitId)
  {
    return "primary-action";
  }

  if(id == kSecondaryActionTraitId)
  {
    return "secondary-action";
  }

  return "unknown";
}
```

## Use the Numeric Value Only for Diagnostics

`Dali::Ui::TraitId::value` exposes the numeric value stored by a `Dali::Ui::TraitId`. Application code should normally compare IDs directly with `==` or `!=`. Use `Dali::Ui::TraitId::value` when you need a compact diagnostic value, such as logging or debug labels.

```cpp
#include <cstdint>
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kDebugOverlayTraitId = Dali::Ui::TraitId::Alloc();
}

uint32_t GetDebugOverlayTraitValue()
{
  return kDebugOverlayTraitId.value;
}
```

Keep the `Dali::Ui::TraitId` itself as the source of truth. The numeric `value` is useful to inspect, but app code should not depend on a specific number being assigned to a particular trait.

```cpp
#include <cstdint>
#include <dali-ui-foundation/public-api/trait-id.h>

struct TraitDebugRecord
{
  Dali::Ui::TraitId id;
  uint32_t value;
};

namespace
{
const Dali::Ui::TraitId kPanelMetricsTraitId = Dali::Ui::TraitId::Alloc();
}

TraitDebugRecord MakePanelMetricsDebugRecord()
{
  return TraitDebugRecord{kPanelMetricsTraitId, kPanelMetricsTraitId.value};
}
```
