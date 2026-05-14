# Scroll Bar Prose Review

## Summary
The draft was well-structured and accurate. One prose correction was needed.

## Changes Made

### 1. HideBar() behavior clarification (Section: Hiding the Bar)
**Original prose:**
> Programmatically hide the scroll bar:

**Revised prose:**
> Programmatically hide the scroll bar. This only affects bars with `ScrollBarVisibility::Auto`; always-visible bars remain visible:

**Source evidence:** `repos/dali-ui/dali-ui-foundation/public-api/i-scroll-bar.h` lines 79-83:
```cpp
/**
 * @brief Hides the scroll bar (fade out).
 *
 * Called when scrolling ends. Only affects bars with Auto visibility.
 */
virtual void HideBar() = 0;
```

**Rationale:** The original prose omitted important behavioral detail from the IScrollBar interface documentation. The method only affects auto-visibility bars, not always-visible ones.

## Verified Accurate (No Changes Needed)

- **Inheritance claim**: `ScrollBar` inherits from `AbsoluteLayout` — verified in `scroll-bar.h` line 43
- **API signatures**: All getter/setter signatures match public header
- **ScrollBarVisibility enum values**: Auto, Always, Never — verified in `scrollable-enum.h` (via header includes)
- **SetBarCornerRadius() overloads**: Both `float` and `Vector4` variants exist — verified in `scroll-bar.h` lines 139-147
- **ScrollView integration**: ScrollView has `SetVerticalScrollBarVisibility()` and `SetHorizontalScrollBarVisibility()` methods — verified in `scroll-view.h` lines 175-195
- **Code examples**: All code examples compile against public API surface

## Remaining Concerns
None. The document is accurate and follows dali-ui application developer guidelines.
