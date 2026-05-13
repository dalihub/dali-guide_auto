# view-state Evaluation - Trial 1

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

- Location: line 44
- Span: `Contains(Dali::Ui::ViewState::NORMAL`
- Reason: Backticked API-like token `Contains(Dali::Ui::ViewState::NORMAL` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 62
- Span: `Dali::Ui::ViewState::Create("Normal"`
- Reason: Backticked API-like token `Dali::Ui::ViewState::Create("Normal"` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 3. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 62
- Span: `Dali::Ui::ViewState::Create("All"`
- Reason: Backticked API-like token `Dali::Ui::ViewState::Create("All"` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/view-state.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/view-state.md`
