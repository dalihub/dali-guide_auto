---
title: Trait Id
sidebar_label: Trait Id
category: utilities
---

# Trait Id

`Dali::Ui::TraitId` is a lightweight handle that identifies a logical trait type used with `Dali::Ui::View`.

## Table of Contents

- [Allocate a Stable Trait Identifier](#allocate-a-stable-trait-identifier)
- [Compare Trait Identifiers](#compare-trait-identifiers)
- [Use the Raw Value for Diagnostics](#use-the-raw-value-for-diagnostics)

## Allocate a Stable Trait Identifier

Use `Dali::Ui::TraitId::Alloc()` once for each logical trait type, then reuse that `Dali::Ui::TraitId` wherever your dali-ui application or extension code needs to recognize that trait. Store the result in static storage so the same trait role keeps one identifier during the process lifetime.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kSelectionStateTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kValidationStateTraitId = Dali::Ui::TraitId::Alloc();
}

Dali::Ui::TraitId GetSelectionStateTraitId()
{
  return kSelectionStateTraitId;
}

Dali::Ui::TraitId GetValidationStateTraitId()
{
  return kValidationStateTraitId;
}
```

Avoid allocating a new `Dali::Ui::TraitId` each time you inspect or attach behavior for a `Dali::Ui::View`. Each call to `Dali::Ui::TraitId::Alloc()` returns a new identifier.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kKeyboardFocusTraitId = Dali::Ui::TraitId::Alloc();
}

Dali::Ui::TraitId KeyboardFocusTraitId()
{
  return kKeyboardFocusTraitId;
}
```

## Compare Trait Identifiers

`Dali::Ui::TraitId` supports direct equality checks with `Dali::Ui::TraitId::operator==` and inequality checks with `Dali::Ui::TraitId::operator!=`. This is the usual way to route trait-specific logic in a View-oriented code path.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kSelectionTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kActivationTraitId = Dali::Ui::TraitId::Alloc();
}

bool IsSelectionTrait(Dali::Ui::TraitId traitId)
{
  return traitId == kSelectionTraitId;
}

bool IsDifferentTrait(Dali::Ui::TraitId lhs, Dali::Ui::TraitId rhs)
{
  return lhs != rhs;
}

const char* GetTraitName(Dali::Ui::TraitId traitId)
{
  if(traitId == kSelectionTraitId)
  {
    return "selection";
  }

  if(traitId == kActivationTraitId)
  {
    return "activation";
  }

  return "unknown";
}
```

When a callback or helper receives a `Dali::Ui::TraitId`, compare it against the identifiers owned by your feature instead of comparing against newly allocated values.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kEnabledStateTraitId = Dali::Ui::TraitId::Alloc();
}

bool ShouldUpdateEnabledState(Dali::Ui::TraitId changedTraitId)
{
  return changedTraitId == kEnabledStateTraitId;
}
```

## Use the Raw Value for Diagnostics

`Dali::Ui::TraitId::value` exposes the numeric identifier. Use it for diagnostics, logging, or in-memory lookup keys when you need a plain integer representation. Keep identity checks in normal application logic based on `Dali::Ui::TraitId::operator==` or `Dali::Ui::TraitId::operator!=`, and do not treat the numeric value as a persistent file or configuration ID.

```cpp
#include <cstdint>
#include <dali-ui-foundation/public-api/trait-id.h>

uint32_t GetTraitDebugValue(Dali::Ui::TraitId traitId)
{
  return traitId.value;
}
```

A compact diagnostic record can store both the `Dali::Ui::TraitId` and its `Dali::Ui::TraitId::value` without introducing a separate application-level identifier.

```cpp
#include <cstdint>
#include <dali-ui-foundation/public-api/trait-id.h>

struct TraitDebugRecord
{
  Dali::Ui::TraitId traitId;
  uint32_t numericValue;
};

TraitDebugRecord MakeTraitDebugRecord(Dali::Ui::TraitId traitId)
{
  return TraitDebugRecord{
    traitId,
    traitId.value
  };
}
```
