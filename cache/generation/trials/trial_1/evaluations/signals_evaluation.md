# signals Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 5
- Warnings: 5
- Needs review: 0
- Repairable issues: 10

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 1 |
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| THIN_SECTION | 3 |
| UNKNOWN_RECEIVER_METHOD | 5 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 18
- Span: `## Connecting to Signals`
- Reason: Section ## Connecting to Signals has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 99
- Span: `## Emitting Signals`
- Reason: Section ## Emitting Signals has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. EMPTY_SECTION (warning)

- Location: line 146
- Span: ``
- Reason: Section ## Connection Management is empty.

### 4. THIN_SECTION (warning)

- Location: line 202
- Span: `## Signal Variants`
- Reason: Section ## Signal Variants has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 110
- Span: `signal.Emit(`
- Reason: signal.Emit() does not resolve on inferred type VoidSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 6. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 115
- Span: `intSignal.Emit(`
- Reason: intSignal.Emit() does not resolve on inferred type IntSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 7. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 120
- Span: `floatSignal.Emit(`
- Reason: floatSignal.Emit() does not resolve on inferred type FloatPairSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 8. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 131
- Span: `boolSignal.Emit(`
- Reason: boolSignal.Emit() does not resolve on inferred type BoolSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 9. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 142
- Span: `signal.EmitOr(`
- Reason: signal.EmitOr() does not resolve on inferred type BoolResultSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 10. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: signals_basic_setup
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/signals.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/signals.md`
