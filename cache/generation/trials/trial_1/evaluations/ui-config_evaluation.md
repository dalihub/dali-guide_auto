# ui-config Evaluation - Trial 1

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 8
- Needs review: 0
- Repairable issues: 8

## Issue Counts

| Type | Count |
|---|---:|
| THIN_SECTION | 5 |
| UNKNOWN_BACKTICK_API_TOKEN | 3 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 50
- Span: `## Display Scaling and DPI Settings`
- Reason: Section ## Display Scaling and DPI Settings has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 99
- Span: `## Text and Label Defaults`
- Reason: Section ## Text and Label Defaults has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. THIN_SECTION (warning)

- Location: line 146
- Span: `## Marquee Animation Defaults`
- Reason: Section ## Marquee Animation Defaults has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. THIN_SECTION (warning)

- Location: line 205
- Span: `## Focus and Input Configuration`
- Reason: Section ## Focus and Input Configuration has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. THIN_SECTION (warning)

- Location: line 293
- Span: `## Image Loading Fallbacks`
- Reason: Section ## Image Loading Fallbacks has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 6. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 191
- Span: `BrokenImageType::SMALL`
- Reason: Backticked API-like token `BrokenImageType::SMALL` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 7. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 192
- Span: `BrokenImageType::NORMAL`
- Reason: Backticked API-like token `BrokenImageType::NORMAL` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 8. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 193
- Span: `BrokenImageType::LARGE`
- Reason: Backticked API-like token `BrokenImageType::LARGE` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/ui-config.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/ui-config.md`
