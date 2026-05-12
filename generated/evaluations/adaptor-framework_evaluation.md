# adaptor-framework Evaluation - Trial 1

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

- Location: line 23
- Span: `void(Dali::Application`
- Reason: Backticked API-like token `void(Dali::Application` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 41
- Span: `void(Dali::Application`
- Reason: Backticked API-like token `void(Dali::Application` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/adaptor-framework.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/adaptor-framework.md`
