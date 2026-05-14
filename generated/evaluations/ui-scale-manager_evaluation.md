# ui-scale-manager Evaluation - Trial 1

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

### 1. EMPTY_SECTION (warning)

- Location: line 34
- Span: ``
- Reason: Section ## Reading and Setting the System Scale is empty.

### 2. THIN_SECTION (warning)

- Location: line 71
- Span: `## Scale Policies for Views`
- Reason: Section ## Scale Policies for Views has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. EMPTY_SECTION (warning)

- Location: line 126
- Span: ``
- Reason: Section ## Practical Examples is empty.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/ui-scale-manager.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/ui-scale-manager.md`
