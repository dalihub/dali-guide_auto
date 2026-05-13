# image-view Evaluation - Trial 4

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
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 1 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 66
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 24
- Span: `Dali::Ui::ImageView&`
- Reason: Backticked API-like token `Dali::Ui::ImageView&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 3. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: view_animation
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_4/validation/image-view.validation.json`
- Source draft: `cache/generation/trials/trial_4/drafts/image-view.md`
