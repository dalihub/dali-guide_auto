# input-field Evaluation - Trial 4

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 0
- Needs review: 0
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| PROMPT_LEAKAGE | 1 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 91
- Span: `PLACEHOLDER`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_4/validation/input-field.validation.json`
- Source draft: `cache/generation/trials/trial_4/drafts/input-field.md`
