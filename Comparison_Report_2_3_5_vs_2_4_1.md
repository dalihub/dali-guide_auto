# Result Comparison: Phase 2.3.5 vs Repair 2.4.1

이 보고서는 `dali-guide_auto/result_until_2_3_5`와 `dali-guide_auto/result_until_2_4_1`의 최종 generated 문서, final report, trial validation/evaluation 결과를 비교한 것이다. 실제 코드 확인은 `dali-ui-documentation-cli/repos` 및 포함된 parsed Doxygen/api_index를 기준으로 샘플링 및 잔여 이슈 중심으로 수행했다.

## Executive Summary

- `2.3.5`: publish-ready 30/33, status={'clean': 9, 'repaired': 21, 'blocked': 3}
- `2.4.1`: publish-ready 32/33, status={'clean': 17, 'repaired': 15, 'blocked': 1}

**판단:** 2.4.1은 2.3.5 대비 명확히 개선되었다. 2.3.5에서는 3개 문서가 blocked였고, 2.4.1에서는 1개만 blocked로 줄었다. clean 문서는 9개에서 17개로 증가했다. 전체 validation 이슈도 bug 32→28, warning 99→31, needs_review 40→20으로 감소했다. 특히 prompt leakage/TODO류와 backtick API 오탐/실탐이 크게 줄었다. 다만 `lottie-animation-view`의 blocker는 실제 코드상 `Dali::Property::Map::Count()`가 존재하므로 validator false positive 가능성이 높고, `web-view`, `image-loader`, `ui-color-manager`에는 public alias/callback 계열의 needs_review가 남아 있다.

## Quantitative Validation Delta

| Metric | 2.3.5 | 2.4.1 | Delta |
|---|---:|---:|---:|
| bug | 3 | 3 | +0 |
| warning | 53 | 25 | -28 |
| needs_review | 40 | 20 | -20 |
| PROMPT_LEAKAGE | 3 | 0 | -3 |
| UNKNOWN_BACKTICK_API_TOKEN | 49 | 21 | -28 |
| UNKNOWN_SCOPED_SYMBOL | 40 | 20 | -20 |
| UNKNOWN_RECEIVER_METHOD | 0 | 3 | +3 |
| PREFERRED_USAGE_PATTERN_MISSING | 4 | 4 | +0 |
| SCOPED_VALUE_OWNER_MISMATCH | 0 | 0 | +0 |

## Per-Feature Final Status

| Feature | 2.3.5 status | 2.4.1 status | 2.4.1 latest issues | Size/code-block change | Assessment |
|---|---|---|---:|---|---|
| `accessibility-highlight-overlay` | clean | clean | 0 | lines 185→230, cpp 9→11 | good / clean validation |
| `adaptor-framework` | repaired | repaired | 1 | lines 252→241, cpp 6→6 | usable, residual review/warning |
| `addon-manager` | repaired | repaired | 1 | lines 361→267, cpp 9→9 | usable, residual review/warning |
| `animated-image-view` | blocked | clean | 0 | lines 387→298, cpp 19→13 | good / clean validation |
| `animation` | repaired | clean | 0 | lines 255→255, cpp 10→10 | good / clean validation |
| `focus-manager` | clean | clean | 0 | lines 304→259, cpp 18→17 | good / clean validation |
| `i-scroll-bar` | clean | clean | 0 | lines 187→213, cpp 9→11 | good / clean validation |
| `image` | clean | clean | 0 | lines 175→243, cpp 7→9 | good / clean validation |
| `image-loader` | repaired | repaired | 6 | lines 243→291, cpp 8→9 | usable, residual review/warning |
| `image-view` | blocked | repaired | 4 | lines 331→281, cpp 20→14 | usable, residual review/warning |
| `input-event` | repaired | clean | 0 | lines 229→194, cpp 12→8 | good / clean validation |
| `input-field` | repaired | clean | 0 | lines 359→320, cpp 15→14 | good / clean validation |
| `interactive-view` | repaired | repaired | 4 | lines 460→324, cpp 16→13 | usable, residual review/warning |
| `label` | repaired | clean | 0 | lines 226→295, cpp 14→15 | good / clean validation |
| `layouts` | clean | repaired | 1 | lines 244→298, cpp 14→11 | usable, residual review/warning |
| `lottie-animation-view` | repaired | blocked | 4 | lines 450→430, cpp 29→24 | not fully publish-ready; inspect before release |
| `render-effects` | repaired | clean | 0 | lines 276→300, cpp 12→13 | good / clean validation |
| `scroll-bar` | repaired | repaired | 1 | lines 189→188, cpp 13→14 | usable, residual review/warning |
| `scroll-view` | repaired | repaired | 3 | lines 263→360, cpp 16→20 | usable, residual review/warning |
| `signals` | repaired | clean | 0 | lines 494→368, cpp 11→12 | good / clean validation |
| `state-event` | clean | clean | 0 | lines 227→251, cpp 5→11 | good / clean validation |
| `text` | repaired | repaired | 1 | lines 195→280, cpp 15→14 | usable, residual review/warning |
| `trait-id` | clean | clean | 0 | lines 146→146, cpp 6→6 | good / clean validation |
| `ui-color` | repaired | repaired | 2 | lines 191→167, cpp 10→11 | usable, residual review/warning |
| `ui-color-manager` | repaired | repaired | 4 | lines 320→310, cpp 14→10 | usable, residual review/warning |
| `ui-component-config` | repaired | clean | 0 | lines 173→121, cpp 7→7 | good / clean validation |
| `ui-config` | blocked | repaired | 1 | lines 267→294, cpp 14→13 | usable, residual review/warning |
| `ui-scale-manager` | clean | clean | 0 | lines 190→230, cpp 9→10 | good / clean validation |
| `ui-theme-manager` | repaired | clean | 0 | lines 268→146, cpp 9→6 | good / clean validation |
| `view` | clean | clean | 0 | lines 476→358, cpp 16→20 | good / clean validation |
| `view-state` | repaired | repaired | 3 | lines 284→361, cpp 15→15 | usable, residual review/warning |
| `visuals` | repaired | repaired | 1 | lines 247→303, cpp 11→15 | usable, residual review/warning |
| `web-view` | repaired | repaired | 11 | lines 547→656, cpp 23→28 | usable, residual review/warning |

## High-Signal Source-Grounded Findings

### Improved Cases
- `animated-image-view`, `image-view`, `ui-config`: 2.3.5에서는 blocked였으나 2.4.1에서는 publish-ready 상태가 되었다. 이전에 문제였던 enum owner/property owner 계열 오류와 prompt leakage 계열 문제가 크게 줄었다.
- `animation`, `label`, `input-event`, `input-field`, `ui-theme-manager`: 2.4.1에서 clean 상태로 정리되었고, generated 문서에 Korean prose/TODO/prompt wording 누출은 발견되지 않았다.
- `scroll-view`, `text`, `visuals`, `web-view`: 2.4.1에서 문서 길이와 코드 예제가 늘어났으며, 사용자 가이드로서 설명량은 개선되었다. 다만 web-view는 callback alias 계열 needs_review가 남는다.

### Remaining Concerns
- `lottie-animation-view`: final report상 blocked이지만 주요 blocker는 `contentInfo.Count()` / `markerInfo.Count()`가 `Dali::Property::Map`에 없다고 판단한 것이다. 실제 `repos/dali-core/dali/public-api/object/property-map.h`에는 `SizeType Count() const`가 있고 manual test에서도 `map.Count()`를 사용한다. 따라서 문서 오류라기보다 validator의 receiver-method index/alias 보강 대상이다.
- `image-loader`: `Dali::ImageDimensions`가 needs_review로 남는다. 실제 `repos/dali-adaptor/dali/public-api/adaptor-framework/image-options.h`에 `using ImageDimensions = Dali::Uint16Pair;`가 있으므로 public alias 처리 보강 대상이다.
- `web-view`: `Dali::BoundsInteger`와 `Dali::Ui::WebView::*Callback::New` 계열이 needs_review로 남는다. `Dali::BoundsInteger`는 `dali-core/dali/public-api/math/rect.h`의 public alias이고, callback aliases는 public headers의 callback typedef/using과 `Callback::New` factory 패턴을 함께 해석해야 한다.
- `ui-color-manager`: `Dali::Ui::ColorCallback::New`는 `ui-color-manager.h`에서 `using ColorCallback = Callback<void(const Vector4&)>;`로 정의된 alias의 factory 사용이다. 문서 자체는 public usage pattern에 부합할 가능성이 높다.

## Code and Prose Quality Judgment

2.4.1 결과는 “대부분의 가이드를 실제 사용자에게 보여줄 수 있는 수준”에 가까워졌다. 특히 `View`, `ImageView`, `Label`, `Animation` 같은 핵심 문서는 View 중심 설명, typed setter/method chaining, enum owner 정확성, signal callback 설명이 2.3.5보다 안정적이다. `ImageView`의 `Dali::Ui::Image::FittingMode::FIT_KEEP_ASPECT_RATIO` 같은 과거 오류 유형은 더 이상 보이지 않는다. `View` 문서는 raw Actor 중심으로 흐르지 않고 `Dali::Ui::View`와 `InteractiveTrait` 중심으로 설명해 public API 관점에 맞다.

다만 “완전히 만족할 단계”라고 하기에는 validator가 아직 public alias/member method를 완전하게 해석하지 못하는 영역이 있다. 특히 `Property::Map::Count`, `ImageDimensions`, `BoundsInteger`, callback alias의 `New`는 코드 기반으로는 유효하거나 유효 가능성이 높은데 validation에서 needs_review/blocker로 남는다. 이는 문서 생성 품질보다는 validation symbol resolution의 마지막 보강 과제다.

## Recommendation

2.4.1은 2.3.5 대비 충분히 좋아졌고, 다음 구현 단계로 넘어가도 되는 수준이다. 단, release candidate로 보기 전에는 다음 보강을 권장한다:
1. public type alias resolution을 api_index validation에 연결한다: `ImageDimensions`, `BoundsInteger`, `ColorCallback` 같은 alias를 원본 타입/namespace와 함께 해석한다.
2. receiver-method validation에서 return type으로 등장한 public compound의 member까지 확인한다: `Dali::Property::Map::Count()` 같은 케이스.
3. callback alias의 static factory 패턴을 특수 casing이 아니라 generic alias-to-template/member lookup으로 처리한다.
4. blocked라도 실제 코드상 유효성이 확인된 false positive는 final report에 “validator_false_positive_suspected”처럼 별도 마킹한다.

## Translation Scope Note

요청된 한국어 번역 대상은 `result_until_2_4_1/docs/generated/*.md` 중 `generation_report.md`를 제외한 33개 최종 가이드 문서다. 코드 블록과 API 토큰은 번역하지 않고 보존해야 한다.
