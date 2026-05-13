# Adaptor Framework Prose Review

Changed prose:
- Revised the opening paragraph from “owns the platform lifecycle” to “connects a DALi application to the platform lifecycle” and explicitly tied UI creation to the emitted initialization point.
- Revised the lifecycle paragraph to say “common application state changes” and “app-owned activity,” avoiding the implication that `PauseSignal()` pauses DALi core itself.
- Revised the `GetResourcePath()` sentence to match the public header wording: it returns the full path where application resources are stored.

Source evidence:
- `repos/dali-adaptor/dali/public-api/adaptor-framework/application.h` documents `Dali::Application` as the object every DALi app creates, states that DALi objects should be created from `InitSignal()`, lists `-w|--width`, `-h|--height`, `-d|--dpi`, and `--help`, and says consumed options are stripped from `argv` while `argc` is updated.
- `repos/dali-adaptor/dali/internal/adaptor/common/application-impl.cpp` emits `InitSignal()`, `TerminateSignal()`, `PauseSignal()`, `ResumeSignal()`, locale signals, low battery/memory signals, and device orientation signals from framework callbacks; its pause comments state that DALi delivers the framework pause event to the app.
- `repos/dali-adaptor/dali/public-api/adaptor-framework/application.h` and `application-impl.cpp` support `MainLoop()`, `Lower()`, `Quit()`, `GetWindow()`, `GetResourcePath()`, `GetLanguage()`, and `GetRegion()`.
- `repos/dali-adaptor/dali/public-api/adaptor-framework/device-status.h` confirms `Dali::DeviceStatus::Battery::CRITICALLY_LOW`, `Dali::DeviceStatus::Memory::CRITICALLY_LOW`, and `Dali::DeviceStatus::Orientation::Status`.
- `repos/dali-adaptor/dali/devel-api/adaptor-framework/offscreen-application.h` confirms `Dali::OffscreenApplication`, `FrameworkBackend::ECORE`, `RenderMode::AUTO`, `RenderMode::MANUAL`, `Start()`, `GetWindow()`, `RenderOnce()`, and `Terminate()`, and states that `OffscreenApplication` has no event loop of its own.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h`, `view.cpp`, and `view.autogen.h` confirm the `Dali::Ui::View` APIs used by the examples, including fluent setters, `InvalidateMeasure()`, `InvalidateArrange()`, and `RemoveAllChildren()`.
- `repos/dali-ui/samples/absolutelayout/absolutelayout-example.cpp` and other dali-ui samples confirm the app pattern of `Application::New(&argc, &argv)`, connecting `InitSignal()`, creating a `Dali::Ui::View` tree, adding it to `Window`, and calling `MainLoop()`.

Remaining concerns:
- The guide intentionally keeps `Dali::Application` and `Dali::OffscreenApplication` in scope because this feature is the adaptor framework; they are dali-adaptor APIs used as application setup rather than dali-ui view APIs.
- The locale example reads `language` and `region` without applying them to real localized content. This is acceptable as a compact guide example, but a product guide could replace it with an actual text or resource refresh example when a concrete app component is available.
