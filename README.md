BGC Bus Tracker
Real-time campus shuttle tracking for BGC Trust University Bangladesh.
Live Demo (https://shaswatabarua.github.io/bus-tracker-app/)
HTML5
CSS3
JavaScript
Firebase
Leaflet
Capacitor
Students wait at a stop without knowing whether the bus has already passed, is running late, or is still on its way. BGC Bus Tracker lets a driver share the bus's live GPS position in one tap, and lets students see the bus moving on a map, along with the driver's name, the route stops, and the daily schedule — for all four campus buses.
The app is a mobile-first web application that also runs as a native Android app built with Capacitor.
Built for Versity Tech Fest 2026 — BGC Trust University Bangladesh.
Features
For students (track.html)
- All four bus routes with ordered stops and daily schedules
- Live / offline status per bus, including driver name and last update time
- Interactive live map with a moving bus marker
- Graceful fallback to the printed schedule when a bus is not sharing
- Works in any modern browser — no install required
For drivers (driver.html)
- Select a bus, enter a name, and start sharing
- Continuous GPS watch for smooth movement, plus a heartbeat every 10 seconds
- One-tap stop that immediately marks the bus as offline
- Native Android geolocation with permission handling and an "Open Settings" prompt
- Works from the browser as well as the Android app
Platform
- Responsive, mobile-first UI (phones, tablets, desktop)
- Same codebase deployed to GitHub Pages and packaged for Android
- No custom backend: Firebase Realtime Database handles live data
Tech stack
Layer
Frontend
Map
Realtime data
Mobile
Hosting
Architecture
flowchart LR
    L[login.html] --> S[Student app<br/>track.html]
    L --> D[Driver app<br/>driver.html]
    D -- "GPS + driver name<br/>every ~10 s" --> DB[(Firebase Realtime DB<br/>busLocation/routeN)]
    DB -- "live stream (onValue)" --> S
    S --- M[Leaflet + OpenStreetMap]
How it works
1. The driver opens the app, chooses a bus, enters their name, and taps Start Sharing Location.
2. The driver app watches the device GPS and writes the current position to busLocation/routeN in Firebase every ~10 seconds, marked with isSharing: true.
3. A student opens the same bus. The tracker subscribes to that route with onValue, moves the map marker, and updates the route card with the driver name and timestamp.
4. When the driver taps Stop, the app writes isSharing: false. Students see the bus switch to Offline within seconds.
Data shape
{
  "busLocation": {
    "route1": {
      "latitude": 22.3569,
      "longitude": 91.7832,
      "accuracy": 12,
      "updatedAt": 1760000000000,
      "isSharing": true,
      "driverName": "Driver Name"
    }
  }
}
Routes and schedule
Bus
Bus 1
Bus 2
Bus 3
Bus 4
All buses are scheduled to reach campus at 9:30 AM and leave for the return trip at 3:00 PM. Schedules may change; the app shows a contact notice on the login screen.
Project structure
bus-tracker-app/
├── index.html      # entry point, redirects to the login screen
├── login.html      # student / driver sign-in (demo)
├── track.html      # student view: route cards + live map
├── driver.html     # driver console: bus assignment + GPS sharing
└── README.md
The full Capacitor project keeps the same pages in www/ and adds the native Android wrapper:
Bus_tracker_new/
├── www/                  # the four web pages bundled into the app
├── android/              # generated Android project (Capacitor)
├── capacitor.config.json
└── package.json
Screenshots
<!-- Add three screenshots to a screenshots/ folder, then uncomment this block:

| Login | Route selection | Live map |
|---|---|---|
| ![Login](screenshots/login.png) | ![Routes](screenshots/routes.png) | ![Live map](screenshots/map.png) |

-->
Getting started
Run the web app
git clone https://github.com/ShaswataBarua/bus-tracker-app.git
cd bus-tracker-app
python -m http.server 5500
Open http://localhost:5500 in a browser.
Use a local server rather than opening the files directly. Geolocation and ES module imports require http://localhost or HTTPS.
Build the Android app
Requires Node.js 18+, JDK 17, and Android Studio, plus the Capacitor project.
npm install
npx cap sync android
npx cap open android
Run the app on a physical device or emulator from Android Studio and grant location permission when prompted. To build a debug APK from the command line:
cd android
./gradlew assembleDebug        # Windows: gradlew.bat assembleDebug
Try the live tracking
1. Open driver.html (or the live site and choose Driver Login), pick a bus, enter a name, and tap Start Sharing Location. Allow location access.
2. Open track.html on a second device or browser tab and select the same bus.
3. The map marker moves and the route card shows the driver's name and last update within ~10 seconds.
4. Tap Stop on the driver device — the tracker switches that bus to Offline shortly after.
Demo sign-in: any non-empty ID and password will sign you in. Student mode opens the tracker; Driver mode opens the driver console. This is a prototype flow, not real authentication.
Security, privacy and current limitations
- Demo authentication. Sign-in is handled entirely in the browser, so any non-empty credentials open the selected portal. A production version should use Firebase Authentication with student/driver roles.
- Database rules are the real security boundary. The Firebase web configuration is public by design; writes must be restricted with Realtime Database rules and Firebase App Check before real-world deployment. There is currently no server-side authorization.
- One active driver per bus. Each route stores a single latest heartbeat, so if two devices share the same bus, the last write wins.
- Assignments are device-local. Driver names and assignments are kept in the browser's localStorage, not in user accounts.
- Location is only shared while active. Pressing Stop writes isSharing: false, and the bus is shown as offline. Anyone who can read the database can still see the last known position.
- No ETA yet. The map shows where the bus is, not when it will reach a stop.
- CDN dependency. The web version loads Leaflet and the Firebase SDK from CDNs, so it needs an internet connection.
Roadmap
- Firebase Authentication and role-based database rules
- ETA to the next stop using route polylines and live speed
- Geofenced "bus reached stop" notifications
- Route polylines and trip history
- Admin dashboard for schedules, buses, and driver assignment
- Offline caching / installable PWA shell
Author
Shaswata Barua
GitHub: @ShaswataBarua (https://github.com/ShaswataBarua)
Built for Versity Tech Fest 2026, BGC Trust University Bangladesh.
Acknowledgements
- Leaflet (https://leafletjs.com/) and OpenStreetMap (https://www.openstreetmap.org/) contributors
- Firebase Realtime Database (https://firebase.google.com/products/realtime-database)
- Capacitor (https://capacitorjs.com/) and @capacitor/geolocation
License
MIT
