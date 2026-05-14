# Input Field Prose Review

## Summary

Reviewed the Input Field guide draft against the public header (`repos/dali-ui/dali-ui-foundation/public-api/input-field.h`), sample files (`text-input-field-example.cpp`), and UTC tests (`utc-Dali-InputField.cpp`). The draft was accurate overall with a few necessary corrections.

## Changes Made

### 1. Overview Description (Line 9)
- **Original**: "`InputField` is a text input control that allows users to enter and edit text."
- **Revised**: "`InputField` is a single-line editable text view that allows users to enter and edit text."
- **Source Evidence**: Header comment states "InputField is a single-line editable text view."
- **Reason**: Aligned with the official API documentation description.

### 2. Programmatic Selection Comment (Line 147)
- **Original**: `// Select a range (start inclusive, end exclusive)`
- **Revised**: `// Select a range by start and end index`
- **Source Evidence**: Header shows `SelectText(uint32_t startIndex, uint32_t endIndex)` without specifying inclusivity semantics. The sample code shows various selection patterns including reversed ranges.
- **Reason**: Removed unverified claim about inclusivity. The actual behavior is more nuanced and the original comment was not supported by the header documentation.

### 3. Added Input Filter Section (New Section)
- **Added**: New "Input Filter" section documenting `SetInputFilter()` and `ClearInputFilter()`.
- **Source Evidence**: Header lines 421-452 document these APIs. Sample file shows usage at lines 421-428.
- **Reason**: The draft was missing documentation for the `SetInputFilter()` and `ClearInputFilter()` APIs which are public and used in samples.

### 4. Typing Style Changed Signal Handler Signature (Line 382)
- **Original**: Handler shown without `Text::TypingStyle::Mask` parameter.
- **Revised**: Added the mask parameter to the callback signature.
- **Source Evidence**: Header line 610 shows `Signal<void(View, Text::TypingStyle::Mask)>& TypingStyleChangedSignal();`
- **Reason**: The signal callback includes a mask parameter indicating which typing style attributes changed.

### 5. Table of Contents Update
- **Added**: "Input Filter" entry to the table of contents.
- **Reason**: Reflects the new section added.

## Verified Accurate (No Changes Needed)

- All setter/getter method names match the public header exactly
- All property names in the Property enum are correct
- Signal names and signatures are accurate
- Code examples use correct fluent chaining syntax
- Font variation API supports both `Vector<FontVariationAxis>` and string format (verified in header lines 530-562)
- Password mode enum values are correct
- Cursor blink interval is in seconds (header line 265 confirms "interval in seconds")
- Password reveal duration is in milliseconds (header line 488 confirms "duration in milliseconds")

## Remaining Considerations

1. **Selection Text Range Semantics**: The exact behavior of `SelectText(start, end)` regarding inclusive/exclusive bounds is not explicitly documented in the header. The sample shows both normal and reversed ranges work. Consider adding more detail if platform documentation clarifies this.

2. **GetTextHandleColor() constness**: The header shows `GetTextHandleColor() const` (line 358) but `GetCursorColor()` and `GetSelectionColor()` are non-const. This inconsistency exists in the API but does not affect the guide.

3. **Default Values**: The UTC tests reveal some defaults (e.g., `IsSelectionEnabled()` defaults to `true`, `IsEditable()` defaults to `true`, `GetPasswordMode()` defaults to `NONE`). These could be documented if desired but are not required for the guide.
