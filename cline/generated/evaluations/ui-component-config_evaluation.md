# ui-component-config Evaluation - Trial 1

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 1
- Needs review: 0
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 1 |

## Human Review

### 1. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 38
- Span: `SetBrokenImageUrl(BrokenImageType, const Dali::String&`
- Reason: Backticked API-like token `SetBrokenImageUrl(BrokenImageType, const Dali::String&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/ui-component-config.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/ui-component-config.md`
