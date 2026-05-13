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
| EMPTY_SECTION | 2 |
| ENUM_VALUE_OWNER_MISMATCH | 1 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 53
- Span: ``
- Reason: Section ## Loading and Resource Management is empty.

### 2. EMPTY_SECTION (warning)

- Location: line 128
- Span: ``
- Reason: Section ## Fitting and Scaling is empty.

### 3. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 233
- Span: `ImageView::Property::PIXEL_AREA`
- Reason: ImageView::Property::PIXEL_AREA is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image.md`
