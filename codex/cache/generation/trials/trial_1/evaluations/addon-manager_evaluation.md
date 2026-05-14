# addon-manager Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 3
- Warnings: 0
- Needs review: 0
- Repairable issues: 3

## Issue Counts

| Type | Count |
|---|---:|
| LITERAL_NEWLINE_ESCAPE_IN_CODE | 3 |

## Human Review

### 1. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 45
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

### 2. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 80
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

### 3. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 104
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/addon-manager.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/addon-manager.md`
