# text Evaluation - Trial 1

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

- Location: line 32
- Span: `Dali::Vector2(-1.0f, -1.0f`
- Reason: Backticked API-like token `Dali::Vector2(-1.0f, -1.0f` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 64
- Span: `std::regex`
- Reason: Backticked API-like token `std::regex` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/text.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/text.md`
