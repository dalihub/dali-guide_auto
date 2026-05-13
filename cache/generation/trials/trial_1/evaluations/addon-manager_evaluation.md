# addon-manager Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 10
- Warnings: 3
- Needs review: 0
- Repairable issues: 13

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 1 |
| LITERAL_NEWLINE_ESCAPE_IN_CODE | 6 |
| THIN_SECTION | 2 |
| UNKNOWN_RECEIVER_METHOD | 4 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 43
- Span: ``
- Reason: Section ## Loading and Using AddOns is empty.

### 2. THIN_SECTION (warning)

- Location: line 164
- Span: `## Calling AddOn Functions`
- Reason: Section ## Calling AddOn Functions has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. THIN_SECTION (warning)

- Location: line 326
- Span: `## Creating Custom AddOns`
- Reason: Section ## Creating Custom AddOns has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 263
- Span: `addon.IsValid(`
- Reason: addon.IsValid() does not resolve on inferred type MyAddOnInterface.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 5. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 267
- Span: `addon.Initialize(`
- Reason: addon.Initialize() does not resolve on inferred type MyAddOnInterface.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 6. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 268
- Span: `addon.Process(`
- Reason: addon.Process() does not resolve on inferred type MyAddOnInterface.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 7. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 269
- Span: `addon.GetResult(`
- Reason: addon.GetResult() does not resolve on inferred type MyAddOnInterface.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 8. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 24
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

### 9. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 89
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

### 10. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 115
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

### 11. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 151
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

### 12. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 172
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

### 13. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 197
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/addon-manager.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/addon-manager.md`
