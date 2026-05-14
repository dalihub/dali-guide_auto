# signals Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 7
- Warnings: 5
- Needs review: 0
- Repairable issues: 12

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 2 |
| THIN_SECTION | 3 |
| UNKNOWN_RECEIVER_METHOD | 7 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 19
- Span: `## Connecting to Signals`
- Reason: Section ## Connecting to Signals has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 108
- Span: `## Emitting Signals`
- Reason: Section ## Emitting Signals has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. EMPTY_SECTION (warning)

- Location: line 162
- Span: ``
- Reason: Section ## Connection Lifecycle Management is empty.

### 4. THIN_SECTION (warning)

- Location: line 227
- Span: `## Signal Variants`
- Reason: Section ## Signal Variants has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. EMPTY_SECTION (warning)

- Location: line 283
- Span: ``
- Reason: Section ## Advanced Connection Patterns is empty.

### 6. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 122
- Span: `signal.Emit(`
- Reason: signal.Emit() does not resolve on inferred type VoidSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 7. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 126
- Span: `intSignal.Emit(`
- Reason: intSignal.Emit() does not resolve on inferred type IntParamSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 8. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 138
- Span: `boolSignal.Emit(`
- Reason: boolSignal.Emit() does not resolve on inferred type BoolSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 9. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 141
- Span: `mixedSignal.Emit(`
- Reason: mixedSignal.Emit() does not resolve on inferred type MixedReturnSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 10. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 152
- Span: `signal.Connect(`
- Reason: signal.Connect() does not resolve on inferred type BoolReturnSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 11. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 153
- Span: `signal.Connect(`
- Reason: signal.Connect() does not resolve on inferred type BoolReturnSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 12. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 156
- Span: `signal.EmitOr(`
- Reason: signal.EmitOr() does not resolve on inferred type BoolReturnSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/signals.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/signals.md`
