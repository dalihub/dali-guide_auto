# visuals Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 6
- Warnings: 1
- Needs review: 1
- Repairable issues: 7

## Issue Counts

| Type | Count |
|---|---:|
| ENUM_VALUE_MISSING | 4 |
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_RECEIVER_METHOD | 2 |
| UNKNOWN_SCOPED_SYMBOL | 1 |

## Human Review

### 1. ENUM_VALUE_MISSING (bug)

- Location: line 56
- Span: `Dali::Image::FittingMode::SCALE_TO_FILL`
- Reason: Dali::Image::FittingMode::SCALE_TO_FILL does not exist in the public API index.
- Suggested direction: Use a real enum value from the matching public enum.

### 2. ENUM_VALUE_MISSING (bug)

- Location: line 65
- Span: `Dali::Image::LoadPolicy::IMMEDIATE`
- Reason: Dali::Image::LoadPolicy::IMMEDIATE does not exist in the public API index.
- Suggested direction: Use a real enum value from the matching public enum.

### 3. ENUM_VALUE_MISSING (bug)

- Location: line 66
- Span: `Dali::Image::ReleasePolicy::DETACHED`
- Reason: Dali::Image::ReleasePolicy::DETACHED does not exist in the public API index.
- Suggested direction: Use a real enum value from the matching public enum.

### 4. ENUM_VALUE_MISSING (bug)

- Location: line 115
- Span: `Dali::Image::MaskingType::MASKING_ON_RENDERING`
- Reason: Dali::Image::MaskingType::MASKING_ON_RENDERING does not exist in the public API index.
- Suggested direction: Use a real enum value from the matching public enum.

### 5. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 154
- Span: `Dali::AnimatedImage::PlayState`
- Reason: Dali::AnimatedImage::PlayState could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 6. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 163
- Span: `playRange.PushBack(`
- Reason: playRange.PushBack() does not resolve on inferred type Dali::Property::Array.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 7. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 164
- Span: `playRange.PushBack(`
- Reason: playRange.PushBack() does not resolve on inferred type Dali::Property::Array.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 8. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 36
- Span: `Dali::Ui::AnimatedImageVisual&`
- Reason: Backticked API-like token `Dali::Ui::AnimatedImageVisual&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/visuals.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/visuals.md`
