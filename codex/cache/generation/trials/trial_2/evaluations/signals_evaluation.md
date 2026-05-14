# signals Evaluation - Trial 2

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 6
- Needs review: 0
- Repairable issues: 6

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 6 |

## Human Review

### 1. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 32
- Span: `Dali::Signal<void(Arg0)>`
- Reason: Backticked API-like token `Dali::Signal<void(Arg0)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 32
- Span: `Dali::Signal<void(Arg0, Arg1)>`
- Reason: Backticked API-like token `Dali::Signal<void(Arg0, Arg1)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 3. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 32
- Span: `Dali::Signal<Ret(Arg0, Arg1)>`
- Reason: Backticked API-like token `Dali::Signal<Ret(Arg0, Arg1)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 90
- Span: `Dali::Signal<void(Arg0)>`
- Reason: Backticked API-like token `Dali::Signal<void(Arg0)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 5. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 90
- Span: `Dali::Signal<void(Arg0, Arg1)>`
- Reason: Backticked API-like token `Dali::Signal<void(Arg0, Arg1)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 6. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 90
- Span: `Dali::Signal<Ret(Arg0, Arg1)>`
- Reason: Backticked API-like token `Dali::Signal<Ret(Arg0, Arg1)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_2/validation/signals.validation.json`
- Source draft: `cache/generation/trials/trial_2/drafts/signals.md`
