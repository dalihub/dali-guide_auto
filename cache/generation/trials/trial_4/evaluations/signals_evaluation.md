# signals Evaluation - Trial 4

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 3
- Needs review: 0
- Repairable issues: 3

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 3 |

## Human Review

### 1. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 36
- Span: `Dali::Signal< void(int)>`
- Reason: Backticked API-like token `Dali::Signal< void(int)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 56
- Span: `Dali::Signal::Disconnect`
- Reason: Backticked API-like token `Dali::Signal::Disconnect` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 3. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 56
- Span: `Dali::Signal::GetConnectionCount`
- Reason: Backticked API-like token `Dali::Signal::GetConnectionCount` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_4/validation/signals.validation.json`
- Source draft: `cache/generation/trials/trial_4/drafts/signals.md`
