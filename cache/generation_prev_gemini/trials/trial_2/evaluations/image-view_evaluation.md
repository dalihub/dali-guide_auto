# image-view Evaluation - Trial 2

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 0
- Needs review: 1
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_SCOPED_SYMBOL | 1 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 68
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 74
- Span: `Dali::Ui::Visual::MaskingMode::MASKING_ON_RENDERING`
- Reason: Dali::Ui::Visual::MaskingMode::MASKING_ON_RENDERING could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_2/validation/image-view.validation.json`
- Source draft: `cache/generation/trials/trial_2/drafts/image-view.md`
