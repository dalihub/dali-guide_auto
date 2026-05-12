# layouts Evaluation - Trial 1

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

- Location: line 130
- Span: `GridLength::Absolute(pixels`
- Reason: Backticked API-like token `GridLength::Absolute(pixels` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 131
- Span: `GridLength::Star(factor`
- Reason: Backticked API-like token `GridLength::Star(factor` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/layouts.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/layouts.md`
