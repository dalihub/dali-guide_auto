# layouts Evaluation - Trial 2

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 6
- Needs review: 0
- Repairable issues: 6

## Issue Counts

| Type | Count |
|---|---:|
| THIN_SECTION | 4 |
| UNKNOWN_BACKTICK_API_TOKEN | 2 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 43
- Span: `## AbsoluteLayout`
- Reason: Section ## AbsoluteLayout has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 94
- Span: `## StackLayout`
- Reason: Section ## StackLayout has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. THIN_SECTION (warning)

- Location: line 138
- Span: `## FlexLayout`
- Reason: Section ## FlexLayout has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. THIN_SECTION (warning)

- Location: line 211
- Span: `## GridLayout`
- Reason: Section ## GridLayout has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 136
- Span: `GridLength::Absolute(pixels`
- Reason: Backticked API-like token `GridLength::Absolute(pixels` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 6. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 137
- Span: `GridLength::Star(factor`
- Reason: Backticked API-like token `GridLength::Star(factor` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_2/validation/layouts.validation.json`
- Source draft: `cache/generation/trials/trial_2/drafts/layouts.md`
