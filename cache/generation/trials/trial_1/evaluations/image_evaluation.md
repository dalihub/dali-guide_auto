# image Evaluation - Trial 1

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
| ENUM_VALUE_OWNER_MISMATCH | 1 |
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| THIN_SECTION | 1 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 204
- Span: `## Loading Behavior`
- Reason: Section ## Loading Behavior has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 173
- Span: `ImageView::Property::PIXEL_AREA`
- Reason: ImageView::Property::PIXEL_AREA is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

### 3. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: image_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image.md`
