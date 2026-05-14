# ui-config Evaluation - Trial 1

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
| THIN_SECTION | 6 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 52
- Span: `## Display Scaling and DPI Configuration`
- Reason: Section ## Display Scaling and DPI Configuration has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 108
- Span: `## Text and Label Defaults`
- Reason: Section ## Text and Label Defaults has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. THIN_SECTION (warning)

- Location: line 185
- Span: `## Marquee Animation Defaults`
- Reason: Section ## Marquee Animation Defaults has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. THIN_SECTION (warning)

- Location: line 254
- Span: `## Focus and Input Configuration`
- Reason: Section ## Focus and Input Configuration has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. THIN_SECTION (warning)

- Location: line 361
- Span: `## Image Loading Error Handling`
- Reason: Section ## Image Loading Error Handling has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 6. THIN_SECTION (warning)

- Location: line 393
- Span: `## Interaction Effects`
- Reason: Section ## Interaction Effects has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 7. SCOPED_VALUE_OWNER_MISMATCH (bug)

- Location: line 295
- Span: `KeyClickPolicy::ON_CLICK`
- Reason: KeyClickPolicy::ON_CLICK could not be resolved with that exact owner/value combination.
- Suggested direction: Use the exact public owner and value from the API index; do not infer enum/property owners from naming alone.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/ui-config.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/ui-config.md`
