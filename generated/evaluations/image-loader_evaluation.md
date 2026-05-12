# image-loader Evaluation - Trial 1

## Summary

- Status: needs_review
- Publish ready: true
- Bugs: 0
- Warnings: 2
- Needs review: 4
- Repairable issues: 2

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 2 |
| UNKNOWN_SCOPED_SYMBOL | 4 |

## Human Review

### 1. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 34
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 73
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 74
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 4. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 120
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 5. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 27
- Span: `Dali::Ui::AsyncImageLoader::Load(const Dali::String& url, ImageDimensions dimensions`
- Reason: Backticked API-like token `Dali::Ui::AsyncImageLoader::Load(const Dali::String& url, ImageDimensions dimensions` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 6. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 47
- Span: `Dali::Ui::ImageUrl::New(const EncodedImageBuffer& encodedImageBuffer`
- Reason: Backticked API-like token `Dali::Ui::ImageUrl::New(const EncodedImageBuffer& encodedImageBuffer` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image-loader.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image-loader.md`
