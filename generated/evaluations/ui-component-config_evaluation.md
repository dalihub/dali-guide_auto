# ui-component-config Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 1
- Warnings: 0
- Needs review: 2
- Repairable issues: 1

## Issue Counts

| Type | Count |
|---|---:|
| PROMPT_LEAKAGE | 1 |
| UNKNOWN_SCOPED_SYMBOL | 2 |

## Human Review

### 1. PROMPT_LEAKAGE (bug)

- Location: line 119
- Span: `Placeholder`
- Reason: Prompt/internal wording leaked into the document: placeholder
- Suggested direction: Remove internal prompt/validation wording from user-facing Markdown.

### 2. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 233
- Span: `MarqueeStopMode::IMMEDIATE`
- Reason: MarqueeStopMode::IMMEDIATE could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

### 3. UNKNOWN_SCOPED_SYMBOL (needs_review)

- Location: line 234
- Span: `MarqueeOrientation::HORIZONTAL`
- Reason: MarqueeOrientation::HORIZONTAL could not be resolved confidently from api_index/context pack.
- Suggested direction: Inspect whether this is a local helper, an omitted include, or a hallucinated API.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/ui-component-config.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/ui-component-config.md`
