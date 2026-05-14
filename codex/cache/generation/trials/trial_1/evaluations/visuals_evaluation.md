# visuals Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 0
- Needs review: 0
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| LITERAL_NEWLINE_ESCAPE_IN_CODE | 1 |

## Human Review

### 1. LITERAL_NEWLINE_ESCAPE_IN_CODE (bug)

- Location: line 300
- Span: `\n`
- Reason: Fenced C++ code contains literal \n escape text, which usually indicates broken formatting.
- Suggested direction: Replace literal \n escape text with real line breaks inside the fenced code block.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/visuals.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/visuals.md`
