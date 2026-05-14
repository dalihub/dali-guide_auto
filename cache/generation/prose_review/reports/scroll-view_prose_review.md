# Scroll View Prose Review

## Summary

The draft was reviewed against the following source files:
- `repos/dali-ui/dali-ui-foundation/public-api/scroll-view.h` (public header)
- `repos/dali-ui/dali-ui-foundation/public-api/scrollable-enum.h` (enum definitions)
- `repos/dali-ui/samples/scrollview/scrollview-example.cpp` (sample code)
- `repos/dali-ui/automated-tests/src/dali-ui-foundation/utc-Dali-ScrollView.cpp` (UTC tests)

## Verification Results

### API Names and Signatures
All API names and signatures in the draft match the public header:
- `ScrollView::New()`, `ScrollView::DownCast()` - ✓ Correct
- `SetContent()` / `GetContent()` - ✓ Correct (returns `ScrollView&` and `View` respectively)
- `SetScrollPosition()` / `GetScrollPosition()` - ✓ Correct
- `SetScrollDirection()` / `GetScrollDirection()` - ✓ Correct
- `ScrollTo()`, `ScrollToX()`, `ScrollToY()` - ✓ Correct (void return, animation parameter defaults to true)
- `SetMaxFlingDistance()` / `GetMaxFlingDistance()` - ✓ Correct
- `SetMinimumFlingDuration()` / `GetMinimumFlingDuration()` - ✓ Correct
- `SetMaximumFlingDuration()` / `GetMaximumFlingDuration()` - ✓ Correct
- `SetFlingSensitivity()` / `GetFlingSensitivity()` - ✓ Correct
- `SetDecelerationRate()` / `GetDecelerationRate()` - ✓ Correct
- `SetOverScrollMode()` / `GetOverScrollMode()` - ✓ Correct
- `SetVerticalScrollBarVisibility()` / `GetVerticalScrollBarVisibility()` - ✓ Correct
- `SetHorizontalScrollBarVisibility()` / `GetHorizontalScrollBarVisibility()` - ✓ Correct
- `IsScrolling()` - ✓ Correct

### Enum Values
All enum values match `scrollable-enum.h`:
- `ScrollDirection::Vertical`, `Horizontal`, `Both` - ✓ Correct
- `ScrollBarVisibility::Auto`, `Always`, `Never` - ✓ Correct
- `OverScrollMode::Never`, `Always`, `ContentScrolls` - ✓ Correct

### Signal Callbacks
All signal types match the header definitions:
- `ScrollStartedSignalType` = `Signal<void(ScrollView)>` - ✓ Correct
- `ScrollingSignalType` = `Signal<void(ScrollView)>` - ✓ Correct
- `ScrollFinishedSignalType` = `Signal<void(ScrollView)>` - ✓ Correct
- `DragStartedSignalType` = `Signal<void(ScrollView)>` - ✓ Correct
- `DraggingSignalType` = `Signal<void(ScrollView, float, float)>` - ✓ Correct
- `DragFinishedSignalType` = `Signal<void(ScrollView)>` - ✓ Correct

### Code Examples
Code examples follow valid patterns from sample code and UTC tests:
- Fluent chaining pattern - ✓ Verified in UTC `UtcDaliScrollViewMethodChainingP`
- Signal connection pattern - ✓ Verified in sample and UTC tests
- Content setup with `StackLayout` - ✓ Verified in sample code
- `ConnectionTracker` inheritance for signal handling - ✓ Verified

### Prose Accuracy
- "ScrollView inherits from `View`" - ✓ Verified in header: `class ScrollView : public View`
- "All scroll-related signals pass the source `ScrollView` to the callback" - ✓ Verified in signal type definitions
- "`DraggingSignal()` callback receives the scroll delta values" - ✓ Verified: `Signal<void(ScrollView, float, float)>`

## Changes Made

No changes were required. The draft accurately reflects the public API surface.

## Remaining Concerns

None. The draft is source-grounded and accurate.
