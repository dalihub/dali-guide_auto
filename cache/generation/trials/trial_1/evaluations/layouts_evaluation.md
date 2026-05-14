# layouts Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 6
- Needs review: 0
- Repairable issues: 7

## Issue Counts

| Type | Count |
|---|---:|
| SCOPED_VALUE_OWNER_MISMATCH | 1 |
| THIN_SECTION | 4 |
| UNKNOWN_BACKTICK_API_TOKEN | 2 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 49
- Span: `## AbsoluteLayout`
- Reason: Section ## AbsoluteLayout has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 143
- Span: `## StackLayout`
- Reason: Section ## StackLayout has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. THIN_SECTION (warning)

- Location: line 185
- Span: `## FlexLayout`
- Reason: Section ## FlexLayout has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. THIN_SECTION (warning)

- Location: line 317
- Span: `## GridLayout`
- Reason: Section ## GridLayout has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 238
- Span: `FlexAlign::SPACE_AROUND`
- Reason: FlexAlign::SPACE_AROUND could not be resolved with that exact owner/value combination.
- Suggestions: `Dali::Ui::FlexJustify::SPACE_AROUND`
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

### 6. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 189
- Span: `GridLength::Absolute(pixels`
- Reason: Backticked API-like token `GridLength::Absolute(pixels` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 7. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 190
- Span: `GridLength::Star(factor`
- Reason: Backticked API-like token `GridLength::Star(factor` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/layouts.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/layouts.md`
