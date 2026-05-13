# render-effects Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 4
- Needs review: 5
- Repairable issues: 4

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 4 |
| UNKNOWN_SCOPED_SYMBOL | 5 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 43
- Span: `Dali::Ui::MaskEffect::ALPHA`
- Reason: Dali::Ui::MaskEffect::ALPHA could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 130
- Span: `Dali::Ui::MaskEffect::ALPHA`
- Reason: Dali::Ui::MaskEffect::ALPHA could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 148
- Span: `Dali::Ui::MaskEffect::LUMINANCE`
- Reason: Dali::Ui::MaskEffect::LUMINANCE could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 203
- Span: `Dali::AlphaFunction::EASE_IN_OUT`
- Reason: Dali::AlphaFunction::EASE_IN_OUT could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 5. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 223
- Span: `Dali::AlphaFunction::EASE_IN_OUT`
- Reason: Dali::AlphaFunction::EASE_IN_OUT could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 6. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 57
- Span: `Dali::Ui::MaskEffect::ALPHA`
- Reason: Backticked API-like token `Dali::Ui::MaskEffect::ALPHA` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 7. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 57
- Span: `Dali::Ui::MaskEffect::LUMINANCE`
- Reason: Backticked API-like token `Dali::Ui::MaskEffect::LUMINANCE` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 8. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 77
- Span: `Dali::Ui::GaussianBlurEffect::SetBlurOnce(true`
- Reason: Backticked API-like token `Dali::Ui::GaussianBlurEffect::SetBlurOnce(true` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 9. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 77
- Span: `Dali::Ui::BackgroundBlurEffect::SetBlurOnce(true`
- Reason: Backticked API-like token `Dali::Ui::BackgroundBlurEffect::SetBlurOnce(true` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/render-effects.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/render-effects.md`
