# focus-manager Evaluation - Trial 1

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 4
- Needs review: 0
- Repairable issues: 4

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 3 |
| THIN_SECTION | 1 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 35
- Span: ``
- Reason: Section ## Setting and Requesting Focus is empty.

### 2. EMPTY_SECTION (warning)

- Location: line 97
- Span: ``
- Reason: Section ## Moving Focus is empty.

### 3. THIN_SECTION (warning)

- Location: line 145
- Span: `## Focus Groups`
- Reason: Section ## Focus Groups has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. EMPTY_SECTION (warning)

- Location: line 270
- Span: ``
- Reason: Section ## Focus Change Signals is empty.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/focus-manager.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/focus-manager.md`
