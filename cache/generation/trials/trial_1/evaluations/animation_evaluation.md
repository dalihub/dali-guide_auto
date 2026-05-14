# animation Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 3
- Needs review: 0
- Repairable issues: 4

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 2 |
| ENUM_VALUE_OWNER_MISMATCH | 1 |
| THIN_SECTION | 1 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 88
- Span: `## Alpha Functions`
- Reason: Section ## Alpha Functions has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. EMPTY_SECTION (warning)

- Location: line 164
- Span: ``
- Reason: Section ## Animation Control is empty.

### 3. EMPTY_SECTION (warning)

- Location: line 229
- Span: ``
- Reason: Section ## Looping and Playback Range is empty.

### 4. ENUM_VALUE_OWNER_MISMATCH (bug)

- Location: line 150
- Span: `View::Property::CORNER_RADIUS`
- Reason: View::Property::CORNER_RADIUS is not defined by that Property owner.
- Suggested direction: Use the property enum owner that actually defines this value.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/animation.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/animation.md`
