# scroll-bar Evaluation - Trial 1

## Summary

- Status: warning
- Publish ready: true
- Bugs: 0
- Warnings: 3
- Needs review: 0
- Repairable issues: 3

## Issue Counts

| Type | Count |
|---|---:|
| PREFERRED_USAGE_PATTERN_MISSING | 1 |
| THIN_SECTION | 2 |

## Human Review

### 1. THIN_SECTION (warning)

- Location: line 47
- Span: `## Configuring Bar Appearance`
- Reason: Section ## Configuring Bar Appearance has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 2. THIN_SECTION (warning)

- Location: line 156
- Span: `## Managing Scroll Position`
- Reason: Section ## Managing Scroll Position has very little explanatory or example content.
- Suggested direction: Add source-grounded explanation or a compact API example only if this section is meant to be substantive.

### 3. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: scroll-bar_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/scroll-bar.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/scroll-bar.md`
