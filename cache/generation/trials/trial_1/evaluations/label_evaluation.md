# label Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 8
- Needs review: 0
- Repairable issues: 9

## Issue Counts

| Type | Count |
|---|---:|
| LITERAL_NEWLINE_ESCAPE_IN_CODE | 1 |
| THIN_SECTION | 8 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 54
- Span: `## Text and Font Configuration`
- Reason: Section ## Text and Font Configuration has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 139
- Span: `## Line Height Control`
- Reason: Section ## Line Height Control has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. THIN_SECTION (warning)

- Location: line 189
- Span: `## Marquee Animation`
- Reason: Section ## Marquee Animation has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 4. THIN_SECTION (warning)

- Location: line 238
- Span: `## Font Size Scaling`
- Reason: Section ## Font Size Scaling has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 5. THIN_SECTION (warning)

- Location: line 282
- Span: `## Asynchronous Rendering`
- Reason: Section ## Asynchronous Rendering has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 6. THIN_SECTION (warning)

- Location: line 340
- Span: `## Text Styling`
- Reason: Section ## Text Styling has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 7. THIN_SECTION (warning)

- Location: line 373
- Span: `## Markup and Anchors`
- Reason: Section ## Markup and Anchors has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 8. THIN_SECTION (warning)

- Location: line 407
- Span: `## Cutout and Mask Effects`
- Reason: Section ## Cutout and Mask Effects has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 9. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 147
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/label.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/label.md`
