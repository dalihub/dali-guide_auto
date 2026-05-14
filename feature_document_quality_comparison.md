# DALi Guide 자동 생성 문서 품질 비교

작성일: 2026-05-14

비교 대상:

- `cline/generated/*.md`: `internal` 옵션으로 생성된 문서
- `codex/generated/*.md`: `external + codex` 옵션으로 생성된 문서
- 실제 구현 참조: `dali-ui-documentation-cli/repos/dali-ui`, `repos/dali-core`, `repos/dali-adaptor`
- 보조 참조: 각 생성물의 `generated/evaluations/*_evaluation.md`, `cache/generation/final_report.md`, `cache/feature_spec/*.json`

평가 기준:

1. 사용자가 이해하기 쉬운 구성인지
2. 실제 코드 구현 기준으로 자연어 설명이 정확한지
3. 실제 코드 구현 기준으로 코드 예제가 정확한지
4. 실제 개발자 관점에서 부족한 점이 무엇인지

## 요약 결론

전체적으로 `codex` 결과가 더 안정적이다. 35개 feature 중 validator 기준 `codex`는 clean 25개, warning 5개, needs_review 4개, needs_repair 1개이고, `cline`은 clean 4개, warning 29개, needs_review 2개, needs_repair 1개다. `cline` 문서는 예제 수가 많고 API를 넓게 건드리는 장점이 있지만, 빈 섹션/얇은 섹션이 반복되고 문서 구조가 API 나열식으로 흐르는 경우가 많다. 반대로 `codex` 문서는 보수적으로 실제 public API와 typed setter 패턴에 맞추는 경향이 강해서 바로 읽고 따라 하기 좋다.

다만 `codex`도 완성본이라고 보기는 어렵다. `visuals`는 validator가 publish 불가로 판단했고, `web-view`, `attachment-id`, `image-loader`, `view`는 unresolved symbol 검토가 남아 있다. 또한 `text`처럼 `cline`이 더 넓은 API 표면을 다루는 경우도 있어, 최종 문서는 `codex`를 베이스로 하되 `cline`의 누락 보완 아이디어를 선별적으로 흡수하는 방식이 가장 좋다.

## 전체 판정표

| Feature | 구성 이해도 | 자연어 정확도 | 코드 정확도 | 개발자 관점 부족/판정 |
|---|---|---|---|---|
| accessibility-highlight-overlay | 둘 다 짧고 단순하다. `codex`가 설명과 예제가 조금 더 충분하다. | 둘 다 큰 문제 없음. | 둘 다 큰 문제 없음. | `codex` 우세. 다만 실제 접근성 overlay가 언제 활성화되는지 운영 맥락 보강 필요. |
| adaptor-framework | `codex`는 lifecycle/window/timer를 균형 있게 묶고, `cline`은 섹션은 많지만 여러 섹션이 thin. | `codex`가 더 소스 기반이다. | `cline`은 예제가 18개로 많지만 맥락 없는 조각이 많다. | `codex` 우세. 개발자는 platform/plugin 제약과 main loop/Window ownership 설명을 더 원할 수 있다. |
| addon-manager | `codex`는 AddOnInfo/dispatch 흐름이 비교적 명확하다. `cline`은 빈 섹션이 있다. | `codex` 우세. | `codex` 예제가 더 따라 하기 좋다. | `codex` 우세. ABI/동적 로딩 실패 처리 설명 보강 필요. |
| animated-image-view | `codex`는 생성, playback, loading, cache 순서가 자연스럽다. | `codex`가 public API에 더 밀착. | 둘 다 예제는 많지만 `codex`가 중복이 적다. | `codex` 우세. resource ready와 loading policy의 실제 비용 설명 추가 권장. |
| animation | `codex`는 view-first animation 흐름이 읽힌다. `cline`은 빈/얇은 섹션 경고. | `codex` 우세. | `cline`은 예제 수는 많지만 단편적이다. | `codex` 우세. property animation 실패/수명 관리 설명 필요. |
| attachment-id | `codex`는 더 상세하지만 `Dali::MakeUnique` unresolved 검토가 있다. `cline`은 짧다. | 둘 다 unique pointer 계열 설명 검토 필요. | `codex` 예제는 풍부하나 unresolved symbol 때문에 보수적 확인 필요. | 무승부에 가깝다. `Dali::UniquePtr`/`MakeUnique` 실제 include와 사용 패턴을 기준으로 재검토 필요. |
| focus-manager | `codex`가 focus 이동, 그룹, indicator를 실사용 흐름으로 구성. `cline`은 빈 섹션 존재. | `codex` 우세. | `codex` 예제가 더 일관됨. | `codex` 우세. 키 이벤트와 접근성 focus 충돌 사례 보강 필요. |
| i-scroll-bar | `codex`가 interface 성격을 더 잘 제한한다. | `codex` 우세. | 둘 다 짧지만 `codex`가 더 안전하다. | `codex` 우세. 직접 구현해야 하는 pure virtual/contract를 더 명확히 쓰면 좋다. |
| image-loader | 둘 다 needs_review. `codex`는 간결, `cline`은 더 많은 예제. | `Dali::ImageDimensions`, `NativeImageInterfacePtr` 같은 context symbol 검토 필요. | 둘 다 실제 include/namespace 확인 필요. | 보류. source-grounded 재작성 권장. |
| image-view | 둘 다 warning. `codex`는 구성 좋고 `cline`은 sampling 섹션이 비어 있다. | `codex` 우세. | `cline` 예제가 많지만 산만하다. | `codex` 우세. view animation usage pattern 보강 필요. |
| image | `codex`는 image enum/value 중심으로 명확하다. `cline`은 loading/animated 섹션이 얇다. | `codex` 우세. | `codex`가 더 안전하다. | `codex` 우세. ImageView/Visual과의 경계 설명 추가 필요. |
| input-event | `codex`가 이벤트 타입과 handler 흐름을 간결하게 설명. `cline`은 빈 섹션 존재. | `codex` 우세. | `codex` 우세. | `codex` 우세. 실제 signal callback signature와 event propagation 설명 보강. |
| input-field | `cline`은 예제가 매우 많지만 빈 섹션이 10개다. `codex`는 더 압축적. | `codex` 우세. | `cline` 코드 조각은 많으나 maintenance 부담이 크다. | `codex` 우세. 단, password/placeholder/cursor 등은 cline의 범위를 참고해 보강할 가치가 있다. |
| interactive-view | `codex`가 clicked/touched/focus 흐름을 더 자연스럽게 묶는다. | `codex` 우세. | `codex` 우세. | `codex` 우세. gesture와 state transition의 관계 설명 필요. |
| label | `cline`은 넓지만 빈 섹션과 과도한 예제. `codex`는 기본 사용 흐름이 좋다. | `codex` 우세. | `codex`가 더 안정적이다. | `codex` 우세. multiline/overflow/markup은 cline 범위를 참고해 추가하면 좋다. |
| layouts | `codex`가 layout class별 역할과 params 사용을 더 잘 설명한다. | `codex` 우세. | `codex` 예제가 더 간결. | `codex` 우세. measure/arrange custom layout의 최소 완성 예제 추가 권장. |
| lottie-animation-view | `codex`는 생성, playback, loop, loading, rendering, marker 순서가 매우 좋다. `cline`은 더 교과서식이다. | 둘 다 대체로 정확하나 `codex`가 public header와 더 밀착. | 둘 다 실제 API와 대부분 맞는다. `codex`가 chaining/typed setter 패턴이 좋다. | `codex` 우세. 단, sample처럼 `SetRequestedWidth`와 layout params를 함께 쓰는 실제 UI 배치 예제가 추가되면 더 좋다. |
| render-effects | 둘 다 warning. `codex`는 한 backtick token 오탐/표기 문제가 있고, `cline`은 thin section 다수. | `codex`가 더 낫지만 MaskEffect 표기 점검 필요. | `codex` 예제가 더 실용적. | `codex` 우세. effect 적용 대상/View lifecycle 설명 보강. |
| scroll-bar | 둘 다 signal usage 경고. `codex`가 구조상 낫다. | `codex` 우세. | `codex` 우세. | `codex` 우세. scroll-bar signal callback과 ScrollView 연동 예제 필요. |
| scroll-view | `codex`가 programmatic scroll, constraints, signals를 더 잘 배치. | `codex` 우세. | `codex` 우세. | `codex` 우세. overscroll/fling edge case 설명 보강. |
| signals | `codex`가 Signal/ConnectionTracker 수명 관리를 더 잘 보여준다. | `codex` 우세. | `codex` 우세. | `codex` 우세. disconnect/lambda capture lifetime 사례 추가 권장. |
| state-event | 둘 다 warning. `codex`는 backtick parsing 문제, `cline`은 얇은 섹션. | `codex` 내용 자체는 더 풍부하나 표기 수정 필요. | `codex` 예제가 더 많지만 `Added(state` 등 표기 오류 수정 필요. | `codex`를 고쳐 쓰는 쪽 권장. |
| text | `codex`는 보수적으로 Bevel/Fit/InputFilter/FontVariation 중심. `cline`은 Underline/Shadow/Outline/LineThrough까지 넓게 다룸. | 둘 다 실제 API와 상당히 맞다. `cline`의 broad coverage가 장점. | `cline` 예제도 source에서 확인되는 API가 많다. 다만 구성은 `codex`가 더 안정적. | 혼합 권장. `codex` 구조에 `cline`의 누락 스타일 객체를 흡수하는 것이 좋다. |
| trait-id | 둘 다 clean. `codex`가 조금 더 친절하다. | 둘 다 큰 문제 없음. | 둘 다 큰 문제 없음. | `codex` 소폭 우세. 실제 extension author가 trait id를 정의하는 흐름 보강 필요. |
| ui-color | `codex`가 짧고 정확하다. `cline`은 theme/alpha 섹션이 얇다. | `codex` 우세. | `codex` 우세. | `codex` 우세. 색 공간/alpha premultiply와의 관계 설명은 부족. |
| ui-color-manager | `codex` clean. `cline`은 query theme colors 빈 섹션 등 needs_review. | `codex` 우세. | `codex` 우세. | `codex` 우세. theme reload/cache invalidation 실제 호출 시점 보강 필요. |
| ui-component-config | `codex`가 config surface를 더 안전하게 제한. | `codex` 우세. | `cline`은 backtick API token 오류가 있다. | `codex` 우세. broken image URL 타입별 정책 설명 보강. |
| ui-config | `codex`가 Apply/default setup 흐름이 명확. `cline`은 많은 섹션이 얇다. | `codex` 우세. | `codex` 우세. | `codex` 우세. app 초기화 순서와 중복 Apply 영향 설명 필요. |
| ui-scale-manager | `codex`는 짧고 안정적. `cline`은 실용 예제 섹션이 비어 있다. | `codex` 우세. | `codex` 우세. | `codex` 우세. `_spx`/scale unit과의 연동 예제 보강. |
| ui-theme-manager | 둘 다 clean이나 `codex`가 더 충실하다. | 둘 다 큰 문제 없음. | `codex` 우세. | `codex` 우세. theme file format/override priority 설명 필요. |
| unique-any | `cline` clean이지만 매우 짧다. `codex`는 풍부하나 `Dali::UniquePtr<T>` unresolved token 경고. | 실제 소스상 `UniqueAny`와 ownership 설명은 재검토 필요. | `codex` 예제가 더 유용하지만 token 표기 수정 필요. | `codex`를 고쳐 쓰는 쪽 권장. |
| view | `cline`은 범위가 넓지만 빈 섹션 9개. `codex`는 needs_review 하나. | `codex` 우세. | `codex`는 `Dali::MakeUnique` 확인 필요. | `codex` 우세. Layout/sizing/view hierarchy/focus는 더 깊게 보강 필요. |
| view-state | `codex`가 state set/check 흐름을 더 잘 설명. `cline`은 빈 섹션. | `codex` 우세. | `codex` 우세. | `codex` 우세. 실제 style/state propagation 사례 보강. |
| visuals | 둘 다 needs_repair. `codex`는 더 최신 visual surface를 포함하고 LottieAnimationVisual까지 다룸. `cline`은 transform/decorations를 넓게 다룸. | `codex` 자연어가 더 정확하지만 validator상 코드 formatting issue가 남음. | 둘 다 실제 API 상당수와 맞지만 publish 전 수동 검토 필수. | 혼합 후 재작성 권장. `codex`를 기본으로 하고 `cline`의 transform/corner/borderline 설명을 검증해 흡수. |
| web-view | 둘 다 needs_review. `codex`는 API 범위가 균형 있고 `cline`은 매우 넓지만 thin/needs_review가 많다. | `codex`가 더 정확하지만 callback `New`와 `BoundsInteger` symbol 검토 필요. | 둘 다 callback 예제는 실제 `Callback<T>::New` 패턴과 대체로 맞지만 include/namespace를 명확히 해야 한다. | `codex` 우세. plugin requirement, async callback lifetime, platform limitation을 더 명시해야 한다. |

## 차이가 큰 문서별 상세 관찰

### `text`

`cline`은 `Underline`, `Shadow`, `Outline`, `LineThrough`, `Bevel`, `InputFilter`, `FitRange`, `FitCandidate`, `FontVariationAxis`를 폭넓게 다룬다. 실제 소스에서도 `repos/dali-ui/dali-ui-foundation/public-api/text/style/*.h`, `label.h`, `input-field.h`에 해당 setter가 존재하므로 API 표면 자체는 상당히 유효하다. 문제는 첫 섹션이 thin으로 잡히고, 전체 구조가 “스타일 객체 나열”에 가까워 사용자가 언제 어떤 객체를 선택해야 하는지가 덜 선명하다는 점이다.

`codex`는 `Bevel`, fit, font variation, input filter를 더 차분하게 설명하고 getter/setter 예제가 안정적이다. 하지만 `Underline`, `Shadow`, `Outline`, `LineThrough`를 사실상 빠뜨려 Text feature 전체 문서로는 범위가 좁다. 최종 문서는 `codex`의 구성과 문장 톤을 유지하고, `cline`의 스타일 객체 범위를 검증해 추가하는 것이 좋다.

### `lottie-animation-view`

`codex`가 가장 좋은 축에 속한다. `LottieAnimationView::New`, `SetResourceUrl`, `Play/Pause/Stop`, `SetLoopCount`, `SetLoopingMode`, `SetMinMaxFrame`, `SetMinMaxFrameByMarker`, `ResourceReadySignal`, `GetContentInfo`, `GetMarkerInfo`가 실제 `lottie-animation-view.h/.cpp`에 맞게 설명된다. 사용 흐름도 생성 -> playback -> loop/frame -> loading -> render tuning 순서라 자연스럽다.

`cline`도 주요 API는 대체로 맞지만 “Creating”, “Playback”, “Looping”처럼 API reference식 구성이 강하고 validator상 빈 섹션이 많다. `SetRequestedWidth/Height` 같은 View 배치 API를 보여주는 점은 실제 sample과 맞으므로, `codex` 문서에 layout 배치 예제로 보강하면 좋다.

### `visuals`

둘 다 publish-ready가 아니다. `codex`는 `ImageVisual`, `AnimatedImageVisual`, `ColorVisual`, `LottieAnimationVisual`, `View::AddVisual`, `View::Visuals`, `GetVisualAt`, `RemoveVisual` 등 실제 public API의 큰 줄기를 잘 잡았다. 실제 구현에도 `visual-base.h`, `image-visual.h`, `animated-image-visual.h`, `lottie-animation-visual.h`, `color-visual.h`, `view.h`에 해당 메서드가 확인된다. 다만 validator가 fenced code의 newline escape 문제를 잡았고, 최종 배포 전 코드 블록을 직접 정리해야 한다.

`cline`은 transform, position, corner radius, borderline 같은 실전 항목을 더 많이 다룬다. 이 부분도 실제 `VisualBase`/`View` API와 상당 부분 맞지만, 얇은 섹션이 많고 Lottie visual을 빠뜨려 coverage가 불완전하다. 따라서 `visuals`는 둘 중 하나를 고르는 대신 재작성 대상이다.

### `web-view`

`codex`는 생성/로드, navigation, input/zoom/rendering, page content, event, resource 순서로 읽기 쉽다. 실제 `web-view.h`의 `LoadUrl`, `LoadHtmlString`, `LoadContents`, `CanGoBack`, `ReloadWithoutCache`, `EvaluateJavaScript`, `AddJavaScriptMessageHandler`, `GetScreenshotAsynchronously`, `PageLoadStartedSignal` 계열과 잘 맞는다.

남은 리스크는 callback 예제다. `WebView::JavaScriptCallback`은 `Callback<void(const Dali::String&)>` alias이고, `Callback<T>::New`는 실제로 존재한다. 하지만 validator가 `WebView::JavaScriptCallback::New`, `PlainTextCallback::New`, `BoundsInteger` 해석을 confidence 낮게 본 만큼 include와 namespace를 문서에서 더 명확히 해야 한다. `cline`은 signals/scroll/custom header까지 넓지만 thin section과 needs_review가 많아 그대로 쓰기 어렵다.

### `view`, `input-field`, `label`

이 세 feature는 `cline`이 압도적으로 많은 예제를 생성했지만 빈 섹션이 많다. 개발자가 보기에는 “많은 조각”보다 “대표 사용 흐름 하나가 제대로 완성된 예제”가 더 중요하다. `codex` 쪽이 더 안정적인 기준 문서이며, `cline`에서 가져올 부분은 `input-field`의 placeholder/password/cursor, `label`의 multiline/overflow/markup, `view`의 hierarchy/focus 관련 항목 정도다.

## 공통 품질 패턴

### 구성 이해도

`codex`는 대부분의 문서에서 사용자 작업 순서에 맞춘다. 예를 들어 view 계열은 생성 -> 속성 설정 -> signal/상태 확인 -> resource/lifecycle 순서로 전개된다. 이 방식은 신규 사용자가 따라 하기 쉽다.

`cline`은 API 표면을 넓게 펼치는 경향이 있다. 숙련자가 “이 feature에 어떤 API가 있나”를 훑기에는 좋지만, 빈 섹션과 얇은 섹션이 많아 완성 문서로는 신뢰도가 떨어진다.

### 자연어 정확도

`codex`는 feature spec과 public header의 symbol 목록에 더 강하게 묶인 문장을 만든다. 그래서 과장된 동작 설명이 적고, “이 API가 무엇을 한다”를 조심스럽게 설명한다.

`cline`은 설명이 더 자신감 있게 쓰였지만, 구현 근거가 약한 일반론이 섞인다. 특히 addon, framework, web-view, visual 계열에서 플랫폼 제약이나 plugin requirement를 충분히 제한하지 않고 일반 웹/GUI 프레임워크처럼 설명하는 부분이 있다.

### 코드 정확도

양쪽 모두 전체 compile 검증을 통과한 예제는 아니다. 다만 token/source 대조 기준으로는 `codex`가 더 안정적이다. `cline`은 예제 수가 많아 표면적으로 풍부하지만, 그만큼 include, namespace, 변수 정의, callback 수명, platform 조건이 생략된 조각이 많다.

실제 개발자에게 바로 도움이 되려면 각 문서마다 다음 기준의 예제를 1개 이상 두는 것이 좋다.

- 필요한 include가 있는 완성 함수 또는 최소 class 형태
- `using namespace`에 의존하지 않는 핵심 타입 표기
- signal callback signature가 실제 public header와 일치
- resource loading은 async/ready/failure 흐름을 포함
- `View` 계열은 window/layout에 붙이는 실제 경로 포함

## 우선 수정 권장 순서

1. `visuals`: 둘 다 needs_repair라 최우선 재작성 대상이다. `codex`를 베이스로, `cline`의 transform/corner/borderline 항목을 실제 API 확인 후 합치는 방식이 좋다.
2. `web-view`: `codex`를 베이스로 callback alias, include, plugin requirement, async callback lifetime을 명확히 한다.
3. `attachment-id`, `unique-any`, `view`: `Dali::MakeUnique`, `Dali::UniquePtr<T>` unresolved token을 실제 source/include 기준으로 정리한다.
4. `image-loader`: `Dali::ImageDimensions`, `NativeImageInterfacePtr` 등 cross-package symbol의 실제 public availability를 확인하고 예제를 줄인다.
5. `text`, `label`, `input-field`: `codex` 구조를 유지하면서 `cline`이 잡은 넓은 API 범위를 선별 보강한다.
6. 나머지 clean feature: `codex`를 기본 채택하되, 개발자가 궁금해할 lifecycle, ownership, error/failure, platform limitation을 한 단락씩 추가한다.

## 최종 추천

최종 문서 세트를 만든다면 `codex/generated`를 기본으로 채택하는 것이 좋다. `cline/generated`는 버리기보다는 “coverage 보강 후보”로 쓰는 편이 낫다. 특히 `text`, `input-field`, `label`, `visuals`, `web-view`처럼 API 면적이 큰 feature에서는 `cline`이 건드린 항목 중 실제 public API에 맞는 내용을 골라 `codex` 문서에 흡수하면 품질이 가장 좋아진다.

현재 상태 그대로 publish한다면 `codex` 쪽이 사용자 혼란을 덜 만들 가능성이 높다. 하지만 `visuals`와 `web-view`는 사용 빈도와 영향도가 큰 문서라, 배포 전 수동 수정 없이는 개발자 문서로 신뢰를 주기 어렵다.
