# signals Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 8
- Warnings: 0
- Needs review: 0
- Repairable issues: 8

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_RECEIVER_METHOD | 8 |

## Human Review

### 1. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 88
- Span: `mSelectedSignal.Emit(`
- Reason: mSelectedSignal.Emit() does not resolve on inferred type SelectedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 111
- Span: `mCanLeaveSignal.Emit(`
- Reason: mCanLeaveSignal.Emit() does not resolve on inferred type CanLeaveSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 3. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 134
- Span: `mInputSignal.EmitOr(`
- Reason: mInputSignal.EmitOr() does not resolve on inferred type InputSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 4. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 308
- Span: `mSelectionChangedSignal.Emit(`
- Reason: mSelectionChangedSignal.Emit() does not resolve on inferred type SelectionChangedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 5. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 340
- Span: `mSavedSignal.Empty(`
- Reason: mSavedSignal.Empty() does not resolve on inferred type SavedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 6. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 345
- Span: `mSavedSignal.GetConnectionCount(`
- Reason: mSavedSignal.GetConnectionCount() does not resolve on inferred type SavedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 7. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 350
- Span: `mSavedSignal.Empty(`
- Reason: mSavedSignal.Empty() does not resolve on inferred type SavedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 8. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 352
- Span: `mSavedSignal.Emit(`
- Reason: mSavedSignal.Emit() does not resolve on inferred type SavedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/signals.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/signals.md`
