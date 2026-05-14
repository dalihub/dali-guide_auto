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
| EMPTY_SECTION | 2 |
| UNKNOWN_BACKTICK_API_TOKEN | 1 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 62
- Span: ``
- Reason: Section ## Creating InputEvent Objects is empty.

### 2. EMPTY_SECTION (warning)

- Location: line 105
- Span: ``
- Reason: Section ## Receiving InputEvent in Signal Handlers is empty.

### 3. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 60
- Span: `GetInputEventType() == InputEventType::NONE`
- Reason: Backticked API-like token `GetInputEventType() == InputEventType::NONE` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/input-event.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/input-event.md`
