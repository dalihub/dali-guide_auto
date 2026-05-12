# addon-manager Evaluation - Trial 1

## Summary

- Status: needs_repair
- Publish ready: false
- Bugs: 2
- Warnings: 3
- Needs review: 0
- Repairable issues: 5

## Issue Counts

| Type | Count |
|---|---:|
| UNKNOWN_BACKTICK_API_TOKEN | 3 |
| UNKNOWN_RECEIVER_METHOD | 2 |

## Human Review

### 1. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 372
- Span: `loader.IsValid(`
- Reason: loader.IsValid() does not resolve on inferred type ImageLoaderAddOn.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 2. UNKNOWN_RECEIVER_METHOD (bug)

- Location: line 377
- Span: `loader.LoadImage(`
- Reason: loader.LoadImage() does not resolve on inferred type ImageLoaderAddOn.
- Suggested direction: Replace this call with an existing public method on the inferred receiver type.

### 3. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 26
- Span: `std::vector<std::string>`
- Reason: Backticked API-like token `std::vector<std::string>` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 4. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 38
- Span: `std::string`
- Reason: Backticked API-like token `std::string` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

### 5. UNKNOWN_BACKTICK_API_TOKEN (warning)

- Location: line 120
- Span: `std::function`
- Reason: Backticked API-like token `std::function` could not be resolved.
- Suggested direction: Use a resolvable public API symbol or remove backticks if this is prose.

## Machine Data

- Validation JSON: `cache/generation/trials/trial_1/validation/addon-manager.validation.json`
- Source draft: `cache/generation/trials/trial_1/drafts/addon-manager.md`
