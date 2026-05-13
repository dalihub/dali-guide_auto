# input-field Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 1
- Needs review: 0
- Repairable issues: 2

## Issue Counts

| Type | Count |
|---|---:|
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 1 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 62
- Span: `PLACEHOLDER`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 32
- Span: `Dali::Ui::InputField&`
- Reason: Backticked API-like token `Dali::Ui::InputField&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/input-field.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/input-field.md`
