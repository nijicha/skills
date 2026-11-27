# Flutter apps

A Flutter app is recorded on an iOS Simulator or an Android emulator, not in Chromium. The spec is `demo_test.dart`, an integration test that drives the app with finders and a Dart copy of the kit, while the host records the device's screen. Everything after the recording is the same as for the web: the same layers, cards, voice, `revoice`, `texts` and `rerender`.

The video keeps the profile's size (1280 × 720). A phone screen sits in a column at one side, with rounded corners and a frame line, and the step caption is large text in a panel at the other side. A wide screen (a tablet, or an app turned to landscape) is shown as large as the frame allows, with the caption bar at the bottom as on the web. The layout is picked from the screen's shape. Intro, chapter, summary and outro cards fill the whole frame, as on the web.

## When it applies

`record.runner` in the profile: `auto` (the default) picks `flutter` when `record.app_dir` (default the repo root) has a `pubspec.yaml` with `sdk: flutter`. walkie:start says which runner it picked. Set `runner: "browser"` to record a Flutter web build in Chromium instead.

## Setup, once per app

- `integration_test` as a dev dependency: `flutter pub add 'dev:integration_test:{"sdk":"flutter"}'`. record stops and says so when it is missing.
- `record.flutter` in the profile when the repo runs Flutter through a version manager: `"puro flutter"`, `"fvm flutter"`.
- `record.device`: a simulator or emulator name, iOS UDID or Android serial (walkie:record's `device` overrides it). Without it, record takes the running one, else the first iPhone simulator. An iOS simulator is booted when it is not running; an Android emulator must be running (`flutter emulators --launch <id>`).
- `debugShowCheckedModeBanner: false` in the app, or the DEBUG ribbon is in the video (simulators run debug builds).

## The spec

walkie:start copies the template to `demo_test.dart`. Replace `package:APP/main.dart` with the app's package name. record copies the test and `walkie_kit.dart` into the app's `integration_test/walkie/` for the run and removes them after, so the app's own tests stay as they are.

| Call | Does |
|---|---|
| `Demo.create(tester)` | After `app.main()`. Reads the clip lengths, zoom and sequence that record passes in |
| `intro(html, 'n0')` / `chapter` / `summary` / `outro` | The same cards as the web kit, on or off by the profile's sequence |
| `stepCaption(html)` / `caption(html)` | Caption in the side panel. `<b>` is marked with the emphasis color |
| `highlight(finder)` | Highlight box around one widget. `null` hides it. Scrolls the widget into view when it is not. The box follows the widget while it moves (a keyboard opening, a banner pushing it down) |
| `highlight(finder, spotlight: true)` | Also dims the rest of the screen. For text-heavy screens |
| `highlight(finder, zoom: true)` | Also zooms the phone onto it (`zoom: 1.6` for a scale). The next `highlight` zooms back out |
| `tap(finder)` | Scrolls the widget into view when it is not (below the fold, under the keyboard), then a tap dot on it and the tap |
| `type(finder, text)` | Types one character at a time |
| `scroll(scrollable, dy)` | Scrolls the list down by `dy` points, smoothly |
| `navigate(() async { … })` | A screen change behind a veil: use it for every tap that opens a new screen |
| `say` / `untilQuiet` / `pause` / `wait(ms)` / `settle()` | Timing, as on the web. `wait` and `settle` also draw frames |
| `finish()` | Call last |

Each scene: `highlight`, `stepCaption`, `say`, act, then `expect` the result.

Rules that differ from widget tests:

- Never call `tester.pumpAndSettle()`. On a device a focused text field's blinking cursor keeps scheduling frames, and it waits for seconds. Use `demo.settle()` (at most 600 ms) or `demo.wait(ms)`.
- Keep `binding.framePolicy = LiveTestWidgetsFlutterBindingFramePolicy.fullyLive` (in the template), or the video drops frames.
- Get past login and seed data before `intro`: the build cuts everything before it.
- Scroll with `demo.scroll`, never `tester.drag` or `tester.fling`: the test binding draws a green crosshair under a test finger for as long as it is down, and it is in the video. A tap is too short to show one.
- To record in landscape, call `SystemChrome.setPreferredOrientations([DeviceOrientation.landscapeLeft])` before `app.main()`. The simulator and the emulator record the screen on its side; the build finds the sync square's corner and turns the recording upright.
- Use finders a widget test would use: `find.byKey` for widgets without unique text, `find.text`, `find.widgetWithText(TextField, 'Email')`. A `Text` gets a box around its letters, not the whole row.

## Zoom

The phone shows at about 300 × 640 px in a 720p video, so its text is small. Zoom onto what each scene is about. The recording has three to four times the pixels it is shown at, so a zoom stays sharp.

- The zoom is capped so the widget's height fits. A widget wider than the zoomed view (most fields and rows) is shown from its left edge, where its label and text start, and the box is cut at the view's edge.
- When the scene goes on to tap something below the highlighted widget, pick a smaller zoom (`zoom: 1.6`) so both stay in view, rather than zooming twice.
- `profile theme layout.device.zoom_scale` is the default scale (2.2).

## Recording

walkie:record, for a Flutter app:

1. Keeps system pop-ups out of the video. An app that may ask for permissions (iOS: usage descriptions in `Info.plist`; Android: runtime permissions in `AndroidManifest.xml`) is built and installed first with them granted; `flutter test` installs over it and the grants stay. On iOS it also quits open apps (an app started over another one shows a "◀ App" link), restarts SpringBoard to clear alerts other apps left, and turns off keyboard tips. On Android it turns on Do Not Disturb and closes open system dialogs. Then a clean status bar: 9:41, full battery (Android demo mode). Do Not Disturb and demo mode are restored after.
2. Records the screen: `xcrun simctl io recordVideo` on iOS (a foldable's primary display), the emulator's own `screenrecord` on Android.
3. Runs `flutter test integration_test/walkie/demo_test.dart -d <device>`, with the voice lengths and the profile's sequence passed in.
4. Stops the recording, restores the status bar, removes the copied test, and builds the video.

The test's output is in `test.log` in the walkie folder. A failing `expect` fails the recording, as on the web.

iOS cannot grant the notifications permission up front: an app that asks for it on start shows that prompt in the video. Ask for it later in the app, or answer it once before recording (the answer is lost when `flutter test` removes the app, so in practice: move the request off the first screen). The first recording of an app that asks for permissions takes about a minute longer (the extra build).

Record on one simulator or emulator at a time: each takes several CPU cores, and a busy machine stutters the recording.

## Look

`theme.layout.device` in the profile: `side` (left or right) and `margin` place the phone; `radius`, `bezel` and `caption_size` style it; `zoom_scale` is the default zoom. walkie:rerender can change the style; a new side or margin needs a new recording. The configurator preview (`walkie preview`) shows the phone layout with the profile's settings.

The layout is `side` when the screen in a full-height column takes at most 42 % of the video's width (phones, foldables, a tablet upright), else `full` (a phone or a tablet in landscape). The build writes which it picked to `stage.json`.

## Not yet

- Physical phones: only simulators and emulators.
- Apps that are not Flutter (SwiftUI, Kotlin, React Native): the kit is Dart.
- Flutter web: record a Flutter app in the browser only with `record.runner: "browser"` and its semantics turned on; there is no kit support for it.
- The Android status bar's clock sits in the screen's corner, and a large `radius` cuts into it.
