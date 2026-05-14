# Ui Scale Manager Prose Review

## Summary

The draft was reviewed against the following source evidence:
- `repos/dali-ui/dali-ui-foundation/public-api/ui-scale-manager.h`
- `repos/dali-ui/dali-ui-foundation/public-api/ui-scale-policy.h`
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`
- `repos/dali-ui/samples/ui-scale/ui-scale-example.cpp`

## Findings

**No changes required.** The draft is accurate and well-grounded in the source code.

### Verified Prose

1. **"UiScaleManager provides access to the system-driven adaptive UI scale"** - Matches the class documentation in `ui-scale-manager.h`: "Provides access to the system-driven adaptive UI scale."

2. **"Use the static `Get()` method to retrieve the singleton instance"** - Matches `static UiScaleManager Get()` with documentation "Returns the singleton UiScaleManager instance."

3. **"The default value is 1.0"** - Matches `GetScale()` documentation: "The current scale value (1.0 by default)"

4. **"This triggers re-layout of all registered layout roots"** - Matches `SetScale()` documentation: "Invalidates all registered layout roots and triggers re-layout."

5. **Policy table descriptions** - All three policy descriptions match the enum documentation in `ui-scale-policy.h` exactly:
   - `INHERIT`: "Inherit effective scale from parent; root inherits from UiScaleManager (default)"
   - `ENABLED`: "Always apply the current UiScaleManager scale, ignoring parent policy"
   - `DISABLED`: "Opt out of scaling (effectiveScale = 1.0); descendant INHERIT views also get 1.0"

6. **"Use `SetUiScalePolicy()` on any `View`"** - Matches `View& SetUiScalePolicy(UiScalePolicy policy)` in `view.h`

### Verified Code Examples

All code examples follow valid dali-ui patterns observed in the sample code:
- `UiScaleManager::Get()` singleton access pattern
- `GetScale()` / `SetScale()` usage
- `View::New()` and `SetUiScalePolicy()` with fluent chaining
- `StackLayout`, `Label`, `InteractiveView` usage patterns
- Policy inheritance examples match Zone A, B, C patterns in `ui-scale-example.cpp`

## Remaining Concerns

None. The draft accurately describes the API surface and provides practical examples grounded in actual sample code.
