# image-view Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 2
- Needs review: 0
- Repairable issues: 3

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 2 |
| PROMPT_LEAKAGE | 1 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 43
- Span: ``
- Reason: Section ## Image Fitting and Scaling is empty.

### 2. EMPTY_SECTION (warning)

- Location: line 100
- Span: ``
- Reason: Section ## Loading Behavior and Policies is empty.

### 3. PROMPT_LEAKAGE (bug)

- Location: line 27
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image-view.md`
