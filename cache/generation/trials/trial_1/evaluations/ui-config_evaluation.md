# ui-config Evaluation - Trial 1

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
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_BACKTICK_API_TOKEN | 3 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 69
- Span: `placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 217
- Span: `BrokenImageType::SMALL`
- Reason: Backticked API-like token `BrokenImageType::SMALL` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 3. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 218
- Span: `BrokenImageType::NORMAL`
- Reason: Backticked API-like token `BrokenImageType::NORMAL` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 219
- Span: `BrokenImageType::LARGE`
- Reason: Backticked API-like token `BrokenImageType::LARGE` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/ui-config.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/ui-config.md`
