# image-loader Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 2
- Needs review: 3
- Repairable issues: 2

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 2 |
| UNKNOWN_SCOPED_SYMBOL | 3 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 30
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 79
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 125
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 27
- Span: `Dali::SamplingMode::BOX_THEN_LINEAR`
- Reason: Backticked API-like token `Dali::SamplingMode::BOX_THEN_LINEAR` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 5. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 31
- Span: `Dali::Ui::AsyncImageLoader::ImageLoadedSignalType&`
- Reason: Backticked API-like token `Dali::Ui::AsyncImageLoader::ImageLoadedSignalType&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image-loader.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image-loader.md`
