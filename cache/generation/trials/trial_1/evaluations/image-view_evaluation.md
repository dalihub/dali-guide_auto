# image-view Evaluation - Trial 1

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 3
- Needs review: 0
- Repairable issues: 3

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 2 |
| THIN_SECTION | 1 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 175
- Span: `## Loading Policies`
- Reason: Section ## Loading Policies has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. EMPTY_SECTION (warning)

- Location: line 252
- Span: ``
- Reason: Section ## Sampling Mode and Desired Size is empty.

### 3. EMPTY_SECTION (warning)

- Location: line 349
- Span: ``
- Reason: Section ## Additional Properties is empty.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/image-view.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/image-view.md`
