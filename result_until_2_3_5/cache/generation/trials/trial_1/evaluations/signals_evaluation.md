# signals Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 10
- Warnings: 18
- Needs review: 0
- Repairable issues: 28

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 18 |
| UNKNOWN_RECEIVER_METHOD | 10 |

## Human Review

### 1. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 162
- Span: `mSelectedSignal.Emit(`
- Reason: mSelectedSignal.Emit() does not resolve on inferred type SelectedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 218
- Span: `mOpenedSignal.Emit(`
- Reason: mOpenedSignal.Emit() does not resolve on inferred type OpenedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 3. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 291
- Span: `mResponseSignal.Emit(`
- Reason: mResponseSignal.Emit() does not resolve on inferred type ResponseSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 4. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 336
- Span: `mReadySignal.Emit(`
- Reason: mReadySignal.Emit() does not resolve on inferred type ReadySignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 5. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 382
- Span: `mHoverSignal.Emit(`
- Reason: mHoverSignal.Emit() does not resolve on inferred type HoverSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 6. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 430
- Span: `mChangedSignal.Empty(`
- Reason: mChangedSignal.Empty() does not resolve on inferred type ChangedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 7. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 435
- Span: `mChangedSignal.GetConnectionCount(`
- Reason: mChangedSignal.GetConnectionCount() does not resolve on inferred type ChangedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 8. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 440
- Span: `mChangedSignal.Empty(`
- Reason: mChangedSignal.Empty() does not resolve on inferred type ChangedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 9. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 442
- Span: `mChangedSignal.Emit(`
- Reason: mChangedSignal.Emit() does not resolve on inferred type ChangedSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 10. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 474
- Span: `mRefreshSignal.Emit(`
- Reason: mRefreshSignal.Emit() does not resolve on inferred type RefreshSignalType.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 11. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 26
- Span: `Dali::Signal<void(Dali::Ui::View, Dali::Ui::InputEvent)>`
- Reason: Backticked API-like token `Dali::Signal<void(Dali::Ui::View, Dali::Ui::InputEvent)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 12. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 26
- Span: `Dali::Signal<void(Dali::Ui::View, bool, Dali::Ui::InputEvent)>`
- Reason: Backticked API-like token `Dali::Signal<void(Dali::Ui::View, bool, Dali::Ui::InputEvent)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 13. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 30
- Span: `Dali::Signal<void(Dali::Ui::WebView, const Dali::String&)>`
- Reason: Backticked API-like token `Dali::Signal<void(Dali::Ui::WebView, const Dali::String&)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 14. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 30
- Span: `Dali::Signal<void(Dali::Ui::WebView)>`
- Reason: Backticked API-like token `Dali::Signal<void(Dali::Ui::WebView)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 15. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 46
- Span: `Dali::Signal<void()>`
- Reason: Backticked API-like token `Dali::Signal<void()>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 16. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 46
- Span: `Dali::Signal<void(Arg0)>`
- Reason: Backticked API-like token `Dali::Signal<void(Arg0)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 17. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 46
- Span: `Dali::Signal<void(Arg0, Arg1)>`
- Reason: Backticked API-like token `Dali::Signal<void(Arg0, Arg1)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 18. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 46
- Span: `Dali::Signal<void(Arg0, Arg1, Arg2)>`
- Reason: Backticked API-like token `Dali::Signal<void(Arg0, Arg1, Arg2)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 19. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 46
- Span: `Dali::Signal<Ret()>`
- Reason: Backticked API-like token `Dali::Signal<Ret()>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 20. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 46
- Span: `Dali::Signal<Ret(Arg0)>`
- Reason: Backticked API-like token `Dali::Signal<Ret(Arg0)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 21. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 46
- Span: `Dali::Signal<Ret(Arg0, Arg1)>`
- Reason: Backticked API-like token `Dali::Signal<Ret(Arg0, Arg1)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 22. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 46
- Span: `Dali::Signal<Ret(Arg0, Arg1, Arg2)>`
- Reason: Backticked API-like token `Dali::Signal<Ret(Arg0, Arg1, Arg2)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 23. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 50
- Span: `Dali::Signal<void(Dali::Ui::View)>`
- Reason: Backticked API-like token `Dali::Signal<void(Dali::Ui::View)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 24. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 50
- Span: `void Callback(Dali::Ui::View`
- Reason: Backticked API-like token `void Callback(Dali::Ui::View` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 25. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 50
- Span: `Dali::Signal<void(Dali::Ui::View, bool, Dali::Ui::InputEvent)>`
- Reason: Backticked API-like token `Dali::Signal<void(Dali::Ui::View, bool, Dali::Ui::InputEvent)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 26. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 50
- Span: `void Callback(Dali::Ui::View, bool, Dali::Ui::InputEvent`
- Reason: Backticked API-like token `void Callback(Dali::Ui::View, bool, Dali::Ui::InputEvent` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 27. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 62
- Span: `Dali::Signal<bool(Dali::Ui::View, Dali::Ui::InputEvent)>`
- Reason: Backticked API-like token `Dali::Signal<bool(Dali::Ui::View, Dali::Ui::InputEvent)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 28. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 66
- Span: `Dali::Signal<Ret(Args...)>`
- Reason: Backticked API-like token `Dali::Signal<Ret(Args...)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/signals.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/signals.md`
