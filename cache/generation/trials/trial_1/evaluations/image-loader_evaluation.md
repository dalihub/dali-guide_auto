# image-loader Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 3
- Warnings: 4
- Needs review: 2
- Repairable issues: 7

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 2 |
| ENUM_VALUE_OWNER_MISMATCH | 2 |
| SCOPED_VALUE_OWNER_MISMATCH | 1 |
| THIN_SECTION | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 1 |
| UNKNOWN_SCOPED_SYMBOL | 2 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 40
- Span: ``
- Reason: Section ## Loading Images is empty.

### 2. EMPTY_SECTION (warning)

- Location: line 124
- Span: ``
- Reason: Section ## Canceling Load Operations is empty.

### 3. THIN_SECTION (warning)

- Location: line 158
- Span: `## Working with ImageUrl`
- Reason: Section ## Working with ImageUrl has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 58
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 5. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 71
- Span: `Dali::ImageDimensions`
- Reason: Dali::ImageDimensions could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 6. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 206
- Span: `Visual::Property::TYPE`
- Reason: Visual::Property::TYPE is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

### 7. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 207
- Span: `ImageVisual::Property::URL`
- Reason: ImageVisual::Property::URL is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

### 8. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 206
- Span: `Dali::Toolkit::Visual::IMAGE`
- Reason: Dali::Toolkit::Visual::IMAGE could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Accessibility::AtspiInterface::IMAGE`, `Dali::Accessibility::Role::IMAGE`, `Dali::Ui::AccessibilityRole::IMAGE`, `Dali::Ui::AnimatedImageView::Property::IMAGE`, `Dali::Ui::AnimatedImageViewPropertyIndex::IMAGE`, `Dali::Ui::DevelVisual::Type::IMAGE`, `Dali::Ui::ImageView::Property::IMAGE`, `Dali::Ui::ImageViewPropertyIndex::IMAGE`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 9. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 64
- Span: `Dali::PixelData pixelData`
- Reason: Backticked API-like token `Dali::PixelData pixelData` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image-loader.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image-loader.md`
