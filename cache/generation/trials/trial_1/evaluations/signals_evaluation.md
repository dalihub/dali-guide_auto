# signals Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 7
- Warnings: 3
- Needs review: 0
- Repairable issues: 10

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 3 |
| UNKNOWN_RECEIVER_METHOD | 7 |

## Human Review

### 1. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 44
- Span: `mReadySignal.Emit(`
- Reason: mReadySignal.Emit() does not resolve on inferred type ReadySignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 49
- Span: `mCurrentPageChangedSignal.Emit(`
- Reason: mCurrentPageChangedSignal.Emit() does not resolve on inferred type PageSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 3. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 63
- Span: `panel.ReadySignal(`
- Reason: panel.ReadySignal() does not resolve on inferred type DocumentPanel.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 4. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 64
- Span: `panel.CurrentPageChangedSignal(`
- Reason: panel.CurrentPageChangedSignal() does not resolve on inferred type DocumentPanel.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 5. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 229
- Span: `mCanCloseSignal.Emit(`
- Reason: mCanCloseSignal.Emit() does not resolve on inferred type CanCloseSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 6. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 279
- Span: `mProgressSignal.Emit(`
- Reason: mProgressSignal.Emit() does not resolve on inferred type ProgressSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 7. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 284
- Span: `mProgressSignal.Empty(`
- Reason: mProgressSignal.Empty() does not resolve on inferred type ProgressSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 8. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 36
- Span: `Dali::Signal< void(int)>`
- Reason: Backticked API-like token `Dali::Signal< void(int)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 9. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 56
- Span: `Dali::Signal::Disconnect`
- Reason: Backticked API-like token `Dali::Signal::Disconnect` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 10. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 56
- Span: `Dali::Signal::GetConnectionCount`
- Reason: Backticked API-like token `Dali::Signal::GetConnectionCount` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/signals.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/signals.md`
