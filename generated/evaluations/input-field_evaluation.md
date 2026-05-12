# input-field Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 4
- Needs review: 2
- Repairable issues: 5

## Issue Counts

| Type | Count |
|---|---:|
| EMPTY_SECTION | 4 |
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_SCOPED_SYMBOL | 2 |

## Human Review

### 1. EMPTY_SECTION (warning)

- Location: line 62
- Span: ``
- Reason: Section ## Text and Placeholder is empty.

### 2. EMPTY_SECTION (warning)

- Location: line 114
- Span: ``
- Reason: Section ## Cursor Configuration is empty.

### 3. EMPTY_SECTION (warning)

- Location: line 172
- Span: ``
- Reason: Section ## Text Selection is empty.

### 4. EMPTY_SECTION (warning)

- Location: line 250
- Span: ``
- Reason: Section ## Font Styling is empty.

### 5. PROMPT_LEAKAGE (bug)

- Location: line 14
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 6. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 578
- Span: `Text::TypingStyle::TEXT_COLOR`
- Reason: Text::TypingStyle::TEXT_COLOR could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 7. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 581
- Span: `Text::TypingStyle::FONT_FAMILY`
- Reason: Text::TypingStyle::FONT_FAMILY could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/input-field.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/input-field.md`
