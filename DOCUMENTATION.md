# Basketball Points Counter — Project Documentation

A small Flutter application that tracks the score of a basketball game between
two teams ("Team A" and "Team B"). Each team can be awarded 1, 2, or 3 points,
and a single Reset button clears both scores.

- Package name: `basketball_points_counter_app`
- App version: `1.0.0+1`
- Dart SDK constraint: `>=3.4.3 <4.0.0`
- Android application id / namespace: `com.example.basketball_points_counter_app`

## Features

- Independent score counters for Team A and Team B
- Three scoring buttons per team: `Add 1 Point`, `Add 2 Point`, `Add 3 Point`
- `Reset` button that sets both scores back to `0`
- Single-screen, portrait-oriented layout with an orange app bar titled
  "Points Counter"; debug banner is disabled

## Tech stack

| Area | Choice |
| --- | --- |
| Framework | Flutter (Material Design, `uses-material-design: true`) |
| Language | Dart |
| State management | Built-in `StatefulWidget` + `setState` |
| Dependencies | `flutter`, `cupertino_icons ^1.0.6` |
| Dev dependencies | `flutter_test`, `flutter_lints ^3.0.0` |
| Lints | `package:flutter_lints/flutter.yaml` via `analysis_options.yaml` |

No external state management, networking, database, or persistence packages are
used — the score lives only in widget state and is lost when the app restarts.

## Repository layout

```
lib/main.dart          Entire application (entry point + UI + state)
test/widget_test.dart  Widget test (default Flutter template test, see Known issues)
pubspec.yaml           Package metadata and dependencies
analysis_options.yaml  Analyzer/lint configuration
android/ ios/ web/ linux/ macos/ windows/
                       Generated platform runner projects
```

## Architecture

The whole app is one file, `lib/main.dart`, with three logical parts:

1. `main()` — calls `runApp(const Basketball_app())`.
2. `Basketball_app` — a `StatefulWidget` that owns the app.
3. `_Basketball_appState` — holds the mutable state and builds the UI.

### State

```dart
class _Basketball_appState extends State<Basketball_app> {
  int teamApoints = 0;
  int teamBpoints = 0;
  ...
}
```

Two integers are the complete application state. Every button handler mutates
one of them inside `setState`, which triggers a rebuild so the `Text` widgets
display the new totals:

```dart
onPressed: () {
  setState(() {
    teamApoints += 3; // or += 1 / += 2, and teamBpoints for team B
  });
}
```

Reset sets both back to zero in a single `setState`:

```dart
onPressed: () {
  setState(() {
    teamApoints = 0;
    teamBpoints = 0;
  });
}
```

### Widget tree

```
MaterialApp (debugShowCheckedModeBanner: false)
└── Scaffold
    ├── AppBar (orange, "Points Counter")
    └── Padding (top: 45)
        └── Column
            ├── Row (spaceEvenly)
            │   ├── Column  → "Team A", score Text (fontSize 150), 3 ElevatedButtons
            │   ├── SizedBox(height: 400) → VerticalDivider (grey)
            │   └── Column  → "Team B", score Text (fontSize 150), 3 ElevatedButtons
            ├── Spacer
            ├── ElevatedButton "Reset"
            └── Spacer
```

Styling is inline: orange `ElevatedButton`s with `minimumSize: Size(150, 50)`,
black label text at `fontSize: 18`, team names at `fontSize: 30`, and scores at
`fontSize: 150`. Vertical spacing between buttons comes from
`Padding(EdgeInsets.only(top: 16))` wrappers.

## Getting started

Prerequisites: the Flutter SDK (stable channel) with a Dart SDK in the
`>=3.4.3 <4.0.0` range, plus the toolchain for whichever target you build
(Android SDK, Xcode, or a desktop toolchain). Verify with `flutter doctor`.

```bash
# 1. Clone
git clone https://github.com/Ayman-ELwarak/Basketball_Points_Counter_App.git
cd Basketball_Points_Counter_App

# 2. Fetch dependencies
flutter pub get

# 3. Run (pick a device from `flutter devices`)
flutter run                 # default device
flutter run -d chrome       # web
flutter run -d linux        # desktop
```

### Build release artifacts

```bash
flutter build apk           # Android APK
flutter build appbundle     # Android App Bundle
flutter build ios           # iOS (macOS + Xcode required)
flutter build web           # Web bundle in build/web
```

### Quality checks

```bash
flutter analyze             # static analysis / lints
flutter test                # widget tests
dart format .               # formatting
```

## Known issues / notes

- `test/widget_test.dart` is still the unmodified Flutter counter template test:
  it looks for `Icons.add` and expects the text `1` after tapping, neither of
  which exists in this app, so `flutter test` fails until the test is rewritten
  against the real UI (e.g. tap `find.text('Add  2 Point')` and assert the score
  text).
- Class names `Basketball_app` / `_Basketball_appState` do not follow Dart's
  `UpperCamelCase` convention; the lint is suppressed with
  `// ignore: camel_case_types` comments.
- Button labels read "Add  2 Point" / "Add  3 Point" (double space, singular
  "Point").
- The layout uses fixed sizes (score `fontSize: 150`, `SizedBox(height: 400)`)
  and is not scrollable, so it can overflow on small screens or in landscape.
- Scores are not persisted and there is no undo, no game clock, no fouls, and no
  win detection.

## Possible next steps

- Rewrite the widget test to cover incrementing each team and resetting
- Extract a reusable `TeamPanel` widget to remove the duplicated Team A/Team B
  button code
- Rename classes to `BasketballApp` / `_BasketballAppState`
- Wrap the body in a `SingleChildScrollView` or use `LayoutBuilder` for
  responsiveness
- Add an undo button, and persist scores with `shared_preferences`
