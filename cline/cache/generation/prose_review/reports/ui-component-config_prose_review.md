# Ui Component Config Prose Review

## Summary

The draft was well-structured and accurate overall. One minor inconsistency was identified and corrected.

## Changes Made

### 1. Inherited Configuration Methods Table (Section 2)

**Issue:** The code examples used `SetDefaultFontSize()` and `SetDefaultTextColor()`, but these methods were not listed in the table. This created an inconsistency between the table and the examples.

**Fix:** 
- Added `SetDefaultFontSize(float)` and `SetDefaultTextColor(const Vector4&)` to the table
- Changed "The following setters are available" to "The following key setters are available"
- Added a note: "Additional methods for marquee configuration, placeholder text styling, focus indicator behavior, and async rendering are also available. See the `UiConfig` API reference for the complete list."

**Source Evidence:**
- `ui-config.h` lines 263-272: `SetDefaultFontSize(float)` and `SetDefaultTextColor(const Vector4&)` are documented public API methods
- `ui-config.h` also contains additional methods: `SetDefaultPlaceholderTextColor`, `SetShowPlaceholderTextOnFocus`, `SetMarqueeSpeed`, `SetMarqueeLoopCount`, `SetMarqueeLoopDelay`, `SetMarqueeGap`, `SetMarqueeStopMode`, `SetMarqueeOrientation`, `SetLabelAsyncRendering`, `SetAlwaysShowFocus`

## Verified Accurate Prose

| Section | Prose | Source Evidence |
|---------|-------|-----------------|
| Overview | "extends `UiConfig`" | `ui-component-config.h` line 47: `class DALI_UI_API UiComponentConfig : public UiConfig` |
| Creating section | "after `Apply()` is called, the configuration is frozen" | `ui-config.h` line 68: "Setter methods may only be called before the config is applied via Apply(). After that point, any setter call will trigger an assertion failure." |
| Creating section | "All setter methods return a reference to the object" | `ui-config.h` all setters return `UiConfig&` |
| Downcasting section | "If the handle does not point to a `UiComponentConfig`, the returned handle is left uninitialized" | `ui-component-config.h` lines 89-91: "If handle points to a UiComponentConfig, the downcast produces a valid handle. If not, the returned handle is left uninitialized." |

## Code Blocks

All code blocks were verified against source and remain unchanged:
- Example 1 matches the pattern in `ui-component-config.h` docstring (lines 49-53)
- Example 2 uses valid inherited methods from `UiConfig`
- Downcast example matches the documented behavior in the header

## Remaining Concerns

None. The document is now accurate and complete.
