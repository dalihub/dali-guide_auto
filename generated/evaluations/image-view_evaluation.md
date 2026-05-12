# image-view Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 2
- Needs review: 2
- Repairable issues: 3

## Issue Counts

| Type | Count |
|---|---:|
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_SCOPED_SYMBOL | 2 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 15
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 406
- Span: `Dali::Ui::ImageViewPropertyIndex::PROPERTY_START_INDEX`
- Reason: Dali::Ui::ImageViewPropertyIndex::PROPERTY_START_INDEX could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 411
- Span: `Dali::Ui::ImageViewPropertyIndex::PROPERTY_END_INDEX`
- Reason: Dali::Ui::ImageViewPropertyIndex::PROPERTY_END_INDEX could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 24
- Span: `Dali::Ui::ImageView&`
- Reason: Backticked API-like token `Dali::Ui::ImageView&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 5. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: view_animation
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image-view.md`
