# image-view Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 7
- Warnings: 0
- Needs review: 0
- Repairable issues: 7

## Issue Counts

| Type | Count |
|---|---:|
| ENUM_VALUE_MISSING | 5 |
| ENUM_VALUE_OWNER_MISMATCH | 1 |
| PROMPT_LEAKAGE | 1 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 68
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 30
- Span: `Dali::Actor::Property::ANCHOR_POINT`
- Reason: Dali::Actor::Property::ANCHOR_POINT is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

### 3. ENUM_VALUE_MISSING (bug)

- Location: line 45
- Span: `Dali::Image::FittingMode::FIT_WIDTH`
- Reason: Dali::Image::FittingMode::FIT_WIDTH does not exist in the public API index.
- Suggested direction: Use a real enum value from the matching public enum.

### 4. ENUM_VALUE_MISSING (bug)

- Location: line 54
- Span: `Dali::Image::SamplingMode::BOX_THEN_LINEAR`
- Reason: Dali::Image::SamplingMode::BOX_THEN_LINEAR does not exist in the public API index.
- Suggested direction: Use a real enum value from the matching public enum.

### 5. ENUM_VALUE_MISSING (bug)

- Location: line 74
- Span: `Dali::Image::MaskingType::MASKING_ON_RENDERING`
- Reason: Dali::Image::MaskingType::MASKING_ON_RENDERING does not exist in the public API index.
- Suggested direction: Use a real enum value from the matching public enum.

### 6. ENUM_VALUE_MISSING (bug)

- Location: line 94
- Span: `Dali::Image::LoadPolicy::IMMEDIATE`
- Reason: Dali::Image::LoadPolicy::IMMEDIATE does not exist in the public API index.
- Suggested direction: Use a real enum value from the matching public enum.

### 7. ENUM_VALUE_MISSING (bug)

- Location: line 95
- Span: `Dali::Image::ReleasePolicy::DETACHED`
- Reason: Dali::Image::ReleasePolicy::DETACHED does not exist in the public API index.
- Suggested direction: Use a real enum value from the matching public enum.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image-view.md`
