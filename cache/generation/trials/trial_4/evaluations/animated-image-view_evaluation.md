# animated-image-view Evaluation - Trial 4

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

- Location: line 145
- Span: `PLACEHOLDER`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 38
- Span: `Dali::Vector<Dali::String>`
- Reason: Backticked API-like token `Dali::Vector<Dali::String>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_4/validation/animated-image-view.validation.json`
- Source draft: `cache/generation/trials/trial_4/drafts/animated-image-view.md`
