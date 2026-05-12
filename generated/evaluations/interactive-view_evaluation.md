# interactive-view Evaluation - Trial 1

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

- Location: line 33
- Span: `Dali::Signal<void(Dali::Ui::View, Dali::Ui::InputEvent)>`
- Reason: Backticked API-like token `Dali::Signal<void(Dali::Ui::View, Dali::Ui::InputEvent)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 85
- Span: `Dali::Signal<bool(Dali::Ui::View, Dali::Ui::InputEvent)>`
- Reason: Backticked API-like token `Dali::Signal<bool(Dali::Ui::View, Dali::Ui::InputEvent)>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/interactive-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/interactive-view.md`
