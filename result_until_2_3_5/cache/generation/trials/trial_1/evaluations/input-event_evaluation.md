# input-event Evaluation - Trial 1

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

- Location: line 32
- Span: `Dali::Ui::InputEvent::New(const Dali::WheelEvent&`
- Reason: Backticked API-like token `Dali::Ui::InputEvent::New(const Dali::WheelEvent&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 78
- Span: `const Dali::Ui::InputEvent&`
- Reason: Backticked API-like token `const Dali::Ui::InputEvent&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 3. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 88
- Span: `const Dali::Ui::InputEvent&`
- Reason: Backticked API-like token `const Dali::Ui::InputEvent&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/input-event.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/input-event.md`
