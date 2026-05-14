# Scroll View Prose Review

## Summary

The draft was reviewed against the public API header (`repos/dali-ui/dali-ui-foundation/public-api/scroll-view.h`), enum definitions (`scrollable-enum.h`), sample code (`scrollview-example.cpp`), and UTC tests (`utc-Dali-ScrollView.cpp`).

## Changes Made

### 1. Added Missing `ScrollTo` Overload (Programmatic Scrolling Section)

**Change:** Added documentation for `ScrollTo(View child, bool animation, ScrollToPosition)` overload.

**Source Evidence:**
- Header signature: `void ScrollTo(View child, bool animation = true, ScrollToPosition scrollToPosition = ScrollToPosition::MakeVisible)`
- Enum values from `scrollable-enum.h`: `Start`, `Center`, `End`, `MakeVisible`

**Rationale:** The original draft documented only `ScrollTo(Vector2, bool)`, `ScrollToX()`, and `ScrollToY()`, but omitted the child-view overload which is a useful API for scrolling to specific content elements.

## Verified Accurate (No Changes Needed)

### API Names and Signatures
- All setter methods return `ScrollView&` for method chaining (verified in header)
- All getter methods return correct types (verified in header)
- Signal type definitions match header:
  - `ScrollStartedSignalType = Signal<void(ScrollView)>`
  - `ScrollFinishedSignalType = Signal<void(ScrollView)>`
  - `ScrollingSignalType = Signal<void(ScrollView)>`
  - `DragStartedSignalType = Signal<void(ScrollView)>`
  - `DragFinishedSignalType = Signal<void(ScrollView)>`
  - `DraggingSignalType = Signal<void(ScrollView, float, float)>`

### Enum Values
- `ScrollDirection`: `Vertical`, `Horizontal`, `Both` — matches `scrollable-enum.h`
- `ScrollBarVisibility`: `Auto`, `Always`, `Never` — matches `scrollable-enum.h`
- `OverScrollMode`: `Never`, `Always`, `ContentScrolls` — matches `scrollable-enum.h`

### Code Examples
- All code examples use correct syntax and API names
- Signal callback signatures match UTC test patterns (e.g., `OnDragging(ScrollView, float, float)`)
- Method chaining patterns match sample code style

### Prose Descriptions
- Overview description matches header docstring
- All setter/getter descriptions accurately reflect header comments
- Signal descriptions accurately reflect header documentation

## Remaining Concerns

None. The draft is now complete and accurate against the public API surface.
