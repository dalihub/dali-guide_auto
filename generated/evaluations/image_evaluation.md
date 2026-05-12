# image Evaluation - Trial 1

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 2
- Needs review: 0
- Repairable issues: 2

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 2 |

## Human Review

### 1. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 27
- Span: `StackLayoutParams::New().SetWeight`
- Reason: Backticked API-like token `StackLayoutParams::New().SetWeight` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 45
- Span: `Signal<void(Dali::Ui::View)>`
- Reason: Backticked API-like token `Signal<void(Dali::Ui::View)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image.md`
