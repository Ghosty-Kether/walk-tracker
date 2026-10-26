# Walk Tracker

A lightweight, mobile-friendly web app that records your walks with GPS, shows live stats on a map, and keeps a history of past routes. It's a single HTML file with no backend, no build step, and no account required.

## Features

- **Live tracking:** start, pause, resume, and stop & save a walk using the browser's Geolocation API.
- **Live stats:** distance, elapsed time (paused time excluded), current speed, and average speed.
- **Route on the map:** your path is drawn as you walk on an OpenStreetMap map powered by [Leaflet](https://leafletjs.com/).
- **GPS accuracy badge:** shows signal quality (green ≤ 15 m, yellow ≤ 50 m, red beyond that). Fixes worse than ±50 m are ignored for the route and distance.
- **Pause-safe distance:** distance covered while paused isn't counted, and no line is drawn across the gap when you resume.
- **Crash recovery:** the walk in progress is backed up as you go. If the page reloads or the tab is killed, it comes back paused so you can resume or save it.
- **Screen stays awake** while a walk is active (where the browser supports the Screen Wake Lock API).
- **Re-center button:** jump back to your current location; the map stops following you if you drag it.
- **Walk history:** every saved walk is stored with its date, distance, duration, average speed, and full route. Tap a walk to see its route on the map, or delete one walk or all of them.
- **Private by design:** all data is stored in your browser's `localStorage`. Nothing is sent to a server.

## Getting started

No installation or dependencies are needed.

1. Clone the repo:
   ```bash
   git clone https://github.com/Ghosty-Kether/walk-tracker.git
   cd walk-tracker
   ```
2. Serve the folder over HTTP(S), for example:
   ```bash
   python3 -m http.server 8000
   ```
   Then open `http://localhost:8000` in your browser.

> **Note:** browsers only allow geolocation on secure origins. `localhost` and `https://` URLs work (for example GitHub Pages), but opening the file directly via `file://` will not.

To use it on your phone, host it somewhere with HTTPS (such as GitHub Pages) and open the link on your phone. You can also add it to your home screen.

## Usage

1. Open the app and allow location access when prompted.
2. Tap **Start Walk**.
3. Use **Pause** / **Resume** as needed.
4. Tap **Stop & Save** to finish. The walk is saved to your history.
5. Tap the clock icon (top right) to browse past walks, view their routes, or delete them.

## How it works

- Position updates come from `navigator.geolocation.watchPosition` with high accuracy enabled.
- Distance is the sum of [haversine](https://en.wikipedia.org/wiki/Haversine_formula) distances between consecutive points. Fixes with accuracy worse than 50 m and movements smaller than 2 m are ignored to reduce GPS noise.
- Temporary GPS errors (signal lost, timeout) don't end the walk; only a denied location permission does.
- Walks shorter than two recorded points are discarded instead of saved.
- Saved walks go to `localStorage` under the key `walkHistory` as JSON (id, date, coordinates, distance in metres, duration in milliseconds). The first point after a pause is flagged `gap: true` so routes are drawn as separate segments.
- The unfinished walk is backed up under `walkInProgress` every few seconds and when the page is hidden, and removed once the walk is saved.

## Tech

- Plain HTML, CSS, and JavaScript, all in `index.html`
- [Leaflet](https://leafletjs.com/) 1.9.4 for the map (loaded from unpkg)
- [OpenStreetMap](https://www.openstreetmap.org/copyright) tiles
- Roboto via Google Fonts

## Known limitations

- Tracking still depends on the browser staying in the foreground. If the app is backgrounded or the phone is locked, the GPS may stop, so there will be a gap in the route. Browsers without Wake Lock support can also let the screen sleep.
- Nothing is recorded while GPS accuracy is worse than ±50 m (for example indoors).
- History is stored per browser and device, with no export or sync.
- An internet connection is needed to load the map tiles and libraries.

## Credits

Map data © [OpenStreetMap](https://www.openstreetmap.org/copyright) contributors.
