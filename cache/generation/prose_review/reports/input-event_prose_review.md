# Input Event Prose Review

## Summary

Reviewed the Input Event guide draft against public headers in `repos/dali-ui/dali-ui-foundation/public-api/`. The draft was largely accurate with one significant API usage error in the SelectableTrait section.

## Changed Prose

### 1. SelectableTrait Signals Section (API Usage Correction)

**Original:**
```cpp
View view = View::New();
SelectableTrait trait = SelectableTrait::New();
view.SetTrait(SelectableTrait::New());
trait.SelectionChangedSignal().Connect(this, [](View view, bool selected, InputEvent event) {
```

**Revised:**
```cpp
View view = View::New();
SelectableTrait trait = view.EnsureSelectableTrait();
trait.SelectionChangedSignal().Connect(this, [](View view, bool selected, InputEvent event) {
```

**Source Evidence:** `view.h` lines 895-904 shows `EnsureSelectableTrait()` method that creates and attaches a SelectableTrait to a View. There is no `SetTrait()` method in the View class. The correct pattern is to use `EnsureSelectableTrait()` which returns the attached trait.

### 2. InteractiveTrait Signals Section (Simplified API Pattern)

**Original:**
```cpp
View view = View::New();
view.EnsureInteractiveTrait().ClickedSignal().Connect(this, [](View view, InputEvent event) {
```

**Revised:**
```cpp
View view = View::New();
InteractiveTrait trait = view.EnsureInteractiveTrait();
trait.ClickedSignal().Connect(this, [](View view, InputEvent event) {
```

**Reason:** Minor clarification for readability - storing the trait in a variable makes the code clearer and follows the same pattern used in the SelectableTrait section.

## Verified Accurate Prose

The following sections were verified against source headers and required no changes:

1. **Event Types Table** - Verified against `input-event-type.h`. All enum values and descriptions match the header documentation.

2. **InputEvent Class Description** - Verified against `input-event.h`. Correctly states that `InputEvent` inherits from `BaseHandle` and supports copy/move semantics.

3. **Creating InputEvent Objects** - All `New()` overloads verified against `input-event.h` lines 70-100. The `None()` static method and its behavior are correctly documented.

4. **InteractiveView Signals** - Verified against `interactive-view.h`. Signal signatures and connection helper methods are correctly documented.

5. **StateEvent Section** - Verified against `state-event.h`. The `GetCause()` method and `GetInputEventType()` convenience method are correctly described.

6. **Code Examples** - All switch statement examples and event type checking patterns are accurate.

## Remaining Concerns

None. All API references have been verified against public headers and the guide accurately reflects the dali-ui API surface.
