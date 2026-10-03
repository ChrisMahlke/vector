# Furlong Ruler

An iPhone and iPad app for measuring distances and areas on Apple Maps. Touch and hold to drop points and measure along roads, trails, bike routes, or in straight lines; record a route as you move; search for places; save, compare and share measurements.

The app was first called Vector. The Xcode project, target, module and bundle ID (`io.chrismahlke.vector`) keep that name; users never see them.

## Setup

Open `vector.xcodeproj` and run the `vector` scheme. Maps, search and directions come from Apple's MapKit, so there's no API key, account or package to set up. The optional Topo (US) map loads public tiles from USGS The National Map, and elevation (the profile, relief, contours, line of sight and 3D models) comes from the keyless AWS Terrain Tiles, decoded on the device (`vector/Core/Terrain/TerrainTiles.swift`).

The app and its widget extension share the App Group `group.io.chrismahlke.vector`; with automatic signing, Xcode registers it on the first device build.

## Tests

`⌘U` runs the unit and UI tests. None of them ask Apple for directions: the UI tests measure in straight lines and launch with `-launch.fresh YES`, so they never start from a measurement left over from an earlier run, and `-map.locatesOnOpen NO`, so the map stays where the test puts it rather than centering on the simulator's location. Unit tests serve terrain tiles from memory; a few UI tests load real ones to draw relief and the 3D model.

Two UI tests check road and trail snapping against Apple's live directions. Opt in with:

```
TEST_RUNNER_VECTOR_LIVE_SERVICES=1 xcodebuild test -scheme vector -destination 'platform=iOS Simulator,name=iPhone 17 Pro'
```

## Releasing

[APP_STORE_PREPARATION.md](APP_STORE_PREPARATION.md) tracks what's left before submission, and [APP_STORE_SCREENSHOTS.md](APP_STORE_SCREENSHOTS.md) covers the screenshots in `AppStore/Screenshots` (plain captures in `Raw`, captioned ones to upload in `Final`). [APP_STORE.md](APP_STORE.md) keeps the metadata to paste and how to retake the screenshots. The privacy policy is [PRIVACY.md](PRIVACY.md).

Directions requests are paced to 30 a minute and place-name lookups to 20 (`vector/Core/Routing/RequestPacer.swift`), below the rate at which Apple turns an app away. A quick run of points routes a little slower rather than falling back to straight lines.

## Layout

- `vector/App`: the app's entry point, keyboard commands, Siri and Shortcuts intents, the inbox that carries their requests to the map, configuration and privacy manifest.
- `vector/Core`: location and route recording, distance and area math, routing with Apple's directions, place names and coordinate parsing, the saved-measurement library, GPX export and GPX/GeoJSON import. No UI.
- `vector/Features/Guide`: the in-app guide to every feature, with animated sketches of the gestures.
- `vector/Features/Map`: the screen, its model, and the MapKit bridge with everything it draws on the map.
- `vector/Features/Overlay`: the readout and its actions, tool rail, layer picker, scale bar, point and segment chips, segment list and location prompt.
- `vector/Features/Search`: the search panel, with the saved list and file import.
- `vector/Features/Settings`: the settings screen and the preferences behind it.
- `vector/Features/Share`: the shared image's card, the off-screen map renderer behind it, the flyover video, and the share sheet.
- `vector/Features/Splash`: the animated opening, drawn from the app icon's mark.
- `vector/DesignSystem`: palette (dark and light themes, and stronger contrast with Increase Contrast), type that scales with Larger Text, and shared controls.
- `Widgets`: the Last Measurement widget and the Control Center buttons, in the `FurlongWidgets` extension.
- `Shared`: code both the app and the widgets build: the widget's data and the app's `furlongruler://` links.
- `Design/AppIcon.swift`: draws the app icon. Run it to regenerate the images in the asset catalog.
- `Design/Screenshots.swift`: captions the App Store screenshots. See [APP_STORE.md](APP_STORE.md).
