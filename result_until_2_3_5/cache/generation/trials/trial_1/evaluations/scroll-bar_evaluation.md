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
| UNKNOWN_BACKTICK_API_TOKEN | 2 |

## Human Review

### 1. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 57
- Span: `Dali::Ui::ScrollBar::SetBarCornerRadius(float`
- Reason: Backticked API-like token `Dali::Ui::ScrollBar::SetBarCornerRadius(float` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 2. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 57
- Span: `Dali::Ui::ScrollBar::SetBarCornerRadius(const Dali::Vector4&`
- Reason: Backticked API-like token `Dali::Ui::ScrollBar::SetBarCornerRadius(const Dali::Vector4&` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 3. PREFERRED_USAGE_PATTERN_MISSING (warning)

- Location: line n/a
- Span: ``
- Reason: Preferred usage pattern is not visible: scroll-bar_signals
- Suggested direction: Consider adding the preferred dali-ui idiom for this feature.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/scroll-bar.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/scroll-bar.md`
