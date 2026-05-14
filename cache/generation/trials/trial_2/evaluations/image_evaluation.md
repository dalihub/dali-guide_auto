# image Evaluation - Trial 2

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 4
- Warnings: 1
- Needs review: 0
- Repairable issues: 5

## Issue Counts

| Type | Count |
|---|---:|
| ENUM_VALUE_OWNER_MISMATCH | 1 |
| MISSING_TABLE_OF_CONTENTS | 1 |
| MISSING_TITLE_HEADING | 1 |
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| TOO_FEW_TOPIC_SECTIONS | 1 |

## Human Review

### 1. MISSING_TITLE_HEADING (bug)

- Location: line n/a
- Span: ``
- Reason: Markdown draft has no top-level title heading.

### 2. MISSING_TABLE_OF_CONTENTS (bug)

- Location: line n/a
- Span: ``
- Reason: Markdown draft has no table of contents heading.

### 3. TOO_FEW_TOPIC_SECTIONS (bug)

- Location: line n/a
- Span: ``
- Reason: Only 0 topic sections found; expected at least 2.

### 4. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 7
- Span: `ImageView::Property::PIXEL_AREA`
- Reason: ImageView::Property::PIXEL_AREA is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

### 5. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: image_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_2/validation/image.validation.json`
- Source draft: `cache/generation/trials/trial_2/drafts/image.md`
