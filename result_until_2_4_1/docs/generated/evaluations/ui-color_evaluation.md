# ui-color Evaluation - Trial 1

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

- Location: line 36
- Span: `Dali::Ui::UiColor(float r, float g, float b, float a = 1.0f`
- Reason: Backticked API-like token `Dali::Ui::UiColor(float r, float g, float b, float a = 1.0f` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 40
- Span: `Dali::Ui::UiColor(1.0f, 0.0f, 0.0f`
- Reason: Backticked API-like token `Dali::Ui::UiColor(1.0f, 0.0f, 0.0f` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/ui-color.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/ui-color.md`
