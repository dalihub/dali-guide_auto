# Ui Color Manager Prose Review

## Summary

Reviewed the draft against `repos/dali-ui/dali-ui-foundation/public-api/ui-color-manager.h`, `repos/dali-ui/dali-ui-foundation/public-api/ui-color.h`, and `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-UiColorManager.cpp`. The draft was largely accurate. Two localized prose corrections were made to improve accuracy.

## Changes Made

### 1. Registering a Binding (Section: Binding Colors to Views)

**Original prose:**
> The callback is invoked whenever the theme changes and the bound color needs to be reapplied.

**Revised prose:**
> The callback is invoked when the theme changes, or when a color override is set or cleared. If a binding with the same `bindingId` already exists, `RegisterBinding()` replaces it. The callback is not invoked immediately upon registration.
>
> The `UiColor` passed to `SetBindingColor()` must have a color ID (constructed with a string identifier). If no binding with the given `bindingId` exists, `SetBindingColor()` is a no-op.

**Source evidence:**
- `ui-color-manager.h` lines 108-116: `RegisterBinding()` docstring states "If a binding with the same bindingId already exists, it is replaced. Does not invoke the callback immediately."
- `ui-color-manager.h` lines 130-133: `SetBindingColor()` docstring states "The new UiColor to store (must have a color ID)" and "If no binding with bindingId exists, this call is a no-op."
- `utc-Dali-UiColorManager.cpp` test `UtcDaliUiColorManagerSetColorOverrideRefreshP`: Confirms callbacks are invoked when `SetColorOverride()` and `ClearColorOverride()` are called.

**Rationale:** The original prose was incomplete. It omitted important behavioral details: (1) bindings are replaced if the same ID exists, (2) callbacks are not invoked on registration, (3) `SetBindingColor()` requires a color ID and is a no-op without a prior binding, and (4) callbacks are also invoked on override changes, not just theme changes.

### 2. Cache Invalidation (Section: Cache Invalidation)

**Original prose:**
> This is useful when the override function's internal state has changed and previously resolved colors may now be stale.

**Revised prose:**
> This is useful when the override function's internal state has changed and previously resolved colors may now be stale. If the `UiColor` holds a direct RGBA value (not a color ID), `InvalidateCache()` is a no-op.

**Source evidence:**
- `ui-color-manager.h` lines 175-177: `InvalidateCache(const UiColor& color)` docstring states "No-op if the UiColor holds a direct RGBA value."

**Rationale:** Added missing behavioral detail that `InvalidateCache()` is a no-op for direct RGBA colors.

## Verified Accurate Prose (No Changes Needed)

- Singleton access pattern and `Get()` method description
- Copy/move semantics and `DownCast()` usage
- `GetColor()` return behavior (`Vector4::ZERO` when not found)
- Default theme color identifiers (`"Primary"`, `"Background"`, `"Outline"`)
- `HasBinding()` and `GetBindingColor()` usage examples
- `ClearBinding()` and `ClearBindings()` behavior
- `ColorOverrideFunc` signature and return value semantics
- Stateless lambda restriction for `SetColorOverride()`
- Immediate refresh of bindings when override is set/cleared

## Remaining Concerns

None. All prose now accurately reflects the public API headers and UTC evidence.
