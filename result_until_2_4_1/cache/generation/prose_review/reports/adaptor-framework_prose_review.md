# Adaptor Framework Prose Review

Changed prose:
- Replaced “starts a DALi process, owns the application main loop” with “initializes the DALi application runtime, provides the application main loop” to avoid overstating process ownership, especially because `Dali::Adaptor` supports externally owned platform loops.
- Refined the `InitSignal` guidance to say initial UI objects should be created from the callback, rather than broadly claiming all DALi objects must be created only after the signal.
- Clarified that `Dali::Application::New` may strip supported DALi command-line options and update `argc`/`argv`.
- Changed “starts event processing” to “starts the application event loop” for closer alignment with `MainLoop`.
- Changed “creates initialized view handles” to “creates an initialized view handle” for grammatical accuracy.
- Changed “use `GetLanguage` and `GetRegion`” to “call `GetLanguage` or `GetRegion`” because each locale signal may require one or both values depending on the update.

Source evidence:
- `repos/dali-adaptor/dali/public-api/adaptor-framework/application.h` defines `Dali::Application::New(int* argc, char** argv[], Dali::StringView stylesheet = "")`, documents supported command-line options, and says matching options are stripped from `argv` while `argc` is updated.
- `repos/dali-adaptor/dali/public-api/adaptor-framework/application.h` defines `MainLoop`, `GetWindow`, `GetLanguage`, `GetRegion`, `InitSignal`, lifecycle signals, and device status signals with the callback types used in the draft.
- `repos/dali-adaptor/dali/public-api/adaptor-framework/device-status.h` defines `Battery::Status`, `Memory::Status`, and `Orientation::Status`, including `POWER_OFF`, `CRITICALLY_LOW`, `ORIENTATION_90`, and `ORIENTATION_270`.
- `repos/dali-adaptor/dali/integration-api/adaptor-framework/adaptor.h` documents `Dali::Adaptor::New(Window)`, `Start`, and an external platform main loop pattern.
- `repos/dali-ui/dali-ui-foundation/public-api/view.h` and `view.autogen.h` define `Dali::Ui::View::New`, fluent typed setters such as `SetRequestedWidth`, `SetRequestedHeight`, and `SetBackgroundColor`.
- `repos/dali-ui/samples/absolutelayout/absolutelayout-example.cpp` and other dali-ui samples use `Application::New(&argc, &argv)`, connect `InitSignal`, retrieve `GetWindow`, create `View`-based content, add it to the window, then call `MainLoop`.

Remaining concerns:
- The guide intentionally keeps `Dali::Adaptor` coverage short because the target audience is application developers and `Dali::Application` is the preferred app-facing entry point.
- Code blocks were preserved except for prose around them; no full compile was run in the read-only review environment.
