# image Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 0
- Needs review: 0
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| ENUM_VALUE_OWNER_MISMATCH | 1 |

## Human Review

### 1. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 142
- Span: `ImageView::Property::PIXEL_AREA`
- Reason: ImageView::Property::PIXEL_AREA is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image.md`
