---
title: Trait Id
sidebar_label: Trait Id
category: utilities
---

# Trait Id

`Dali::Ui::TraitId` is the dali-ui identifier type used to distinguish trait data associated with a `Dali::Ui::View`.

## Table of Contents

- [Allocating One Id Per Trait Kind](#allocating-one-id-per-trait-kind)
- [Comparing Trait Ids](#comparing-trait-ids)
- [Using the Numeric Value for Diagnostics](#using-the-numeric-value-for-diagnostics)

## Allocating One Id Per Trait Kind

Allocate a `Dali::Ui::TraitId` once for each logical trait kind. `Dali::Ui::TraitId::Alloc()` returns a new identifier, and the public header recommends storing that result in a static variable so the same trait kind reuses the same id throughout the app or extension code.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kSelectionStateTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kScrollStateTraitId    = Dali::Ui::TraitId::Alloc();
}

Dali::Ui::TraitId GetSelectionStateTraitId()
{
  return kSelectionStateTraitId;
}

Dali::Ui::TraitId GetScrollStateTraitId()
{
  return kScrollStateTraitId;
}
```

In a dali-ui app, treat the id as the stable key for a `Dali::Ui::View` trait category. Do not call `Dali::Ui::TraitId::Alloc()` every time you work with an individual view instance; call it once for the trait kind and reuse that `Dali::Ui::TraitId`.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kCardMetadataTraitId = Dali::Ui::TraitId::Alloc();
}

Dali::Ui::TraitId CardMetadataTraitId()
{
  return kCardMetadataTraitId;
}
```

## Comparing Trait Ids

Use `Dali::Ui::TraitId::operator==` and `Dali::Ui::TraitId::operator!=` to branch on a known trait id. The comparison checks whether two `Dali::Ui::TraitId` values represent the same allocated identifier.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kPrimaryTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kAccentTraitId  = Dali::Ui::TraitId::Alloc();
}

bool IsPrimaryTrait(Dali::Ui::TraitId traitId)
{
  return traitId == kPrimaryTraitId;
}

bool IsNotAccentTrait(Dali::Ui::TraitId traitId)
{
  return traitId != kAccentTraitId;
}
```

This is useful when app code receives or stores a `Dali::Ui::TraitId` and needs to decide which `Dali::Ui::View`-related trait category it belongs to.

```cpp
#include <dali-ui-foundation/public-api/trait-id.h>

namespace
{
const Dali::Ui::TraitId kLayoutCacheTraitId = Dali::Ui::TraitId::Alloc();
const Dali::Ui::TraitId kInputStateTraitId  = Dali::Ui::TraitId::Alloc();
}

const char* GetTraitLabel(Dali::Ui::TraitId traitId)
{
  if(traitId == kLayoutCacheTraitId)
  {
    return "layout-cache";
  }

  if(traitId == kInputStateTraitId)
  {
    return "input-state";
  }

  return "unknown";
}
```

## Using the Numeric Value for Diagnostics

`Dali::Ui::TraitId::value` exposes the underlying `uint32_t` value. Use it for logging, diagnostics, or compact debug records. Keep equality decisions based on `Dali::Ui::TraitId::operator==` or `Dali::Ui::TraitId::operator!=` so the code stays tied to the identifier type.

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

For example, a diagnostic snapshot can preserve the numeric value while the app logic still passes around `Dali::Ui::TraitId`.

```cpp
#include <cstdint>
#include <dali-ui-foundation/public-api/trait-id.h>

struct TraitDebugRecord
{
  Dali::Ui::TraitId traitId;
  uint32_t          numericValue;
};

TraitDebugRecord MakeTraitDebugRecord(Dali::Ui::TraitId traitId)
{
  TraitDebugRecord record{
    traitId,
    traitId.value};

  return record;
}
```
