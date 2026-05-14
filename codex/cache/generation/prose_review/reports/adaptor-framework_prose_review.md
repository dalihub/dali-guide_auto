# Adaptor Framework Prose Review

Changed prose:
- Clarified the overview to say the framework provides the main platform window, instead of implying a direct connection mechanism between the adaptor and a `Dali::Ui::View` tree.
- Adjusted the `InitSignal()` guidance to say it is the application initialization signal and the usual point for `GetWindow()`, avoiding an unsupported claim that all adaptor resources are ready.
- Replaced the broad `Application::New()` command-line wording with the public header's supported option categories: window size, DPI, and help.
- Changed `MainLoop()` prose from "blocks until quit" to "starts the application main loop", matching the public header wording.
- Tightened layout prose from "produced by the layout pass" to "computed by the layout system" to match dali-ui layout terminology.
- Clarified `EventThreadCallback` lifetime guidance: the public header requires creation on the main thread and `Trigger()` can be used from worker threads.
- Simplified `ApplicationController` sample by removing an unused `View` tree. The public `ApplicationController` header exposes lifecycle methods but no public `GetWindow()`, so the original embedded-view code did not show a complete supported view attachment path.
- Updated `ApplicationController` prose to match the header: `PreInitialize()` initializes the window and adaptor, and `PostInitialize()` completes initialization.

Source evidence:
- `repos/dali-adaptor/dali/public-api/adaptor-framework/application.h` defines `Application::New()`, supported command-line options, `MainLoop()`, `Lower()`, `Quit()`, `GetWindow()`, lifecycle signals, `GetLanguage()`, `GetRegion()`, `LowBatterySignal()`, and `LowMemorySignal()`.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` defines `View::New()`, `SetRequestedWidth()`, `SetRequestedHeight()`, `SetFocusable()`, `KeyEventSignal()`, `InvalidateMeasure()`, `InvalidateArrange()`, `ClearBackground()`, and `Children()`.
- `repos/dali-ui/wiki/Focus-&-Key.md` documents `View::KeyEventSignal()` callbacks returning `true` to consume key events.
- `repos/dali-adaptor/dali/devel-api/adaptor-framework/event-thread-callback.h` states that `EventThreadCallback` should be created on the main thread and that `Trigger()` can be used from worker threads.
- `repos/dali-adaptor/dali/integration-api/adaptor-framework/application-controller.h` defines `ApplicationController::New()`, `PreInitialize()`, `PostInitialize()`, `PrePause()`, `PostPause()`, `PreResume()`, `PostResume()`, `PreTerminate()`, and `PostTerminate()`.

Remaining concerns:
- The guide still uses compact examples and does not demonstrate every lifecycle signal listed in prose.
- `ApplicationController` remains advanced integration guidance, not a typical dali-ui application-development path.
