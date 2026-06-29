# CHAPTER 5: IMPLEMENTATION

## 5.1 Deployment

### Implementation scope

This chapter describes how **Intelligent Route Cost & Efficiency** was built. The implementation covers:

- A **React** single-page application (web and responsive mobile browser);
- An **Android hybrid app** via Capacitor 7;
- A **Node.js and Express** REST API;
- A **SQLite** database for users, trips, and leaderboard data;
- Integration with **OSRM** (routing) and **Nominatim** (geocoding).

Deployment is intended for a **local or LAN environment**: the backend runs on a PC (`HOST=0.0.0.0`, `PORT=4000`), and clients connect over Wi‑Fi. There is no cloud production deployment in the current codebase.

### Mapping modules to project objectives

**Table 5.1** Module mapping to Chapter 1 objectives

| Objective | Implemented module | Main location |
|-----------|-------------------|---------------|
| Multi-route planning | Route API + OSRM integration | `backend/src/services/maps.js`, `routes/routes.js` |
| Fuel cost and CO₂ calculation | Calculation engine | `backend/src/utils/calc.js` |
| Lowest-cost recommendation | Route comparison logic | `backend/src/utils/calc.js` |
| GPS navigation | Navigation overlay + MapLibre map | `NavigationOverlay.jsx`, `NavigationMapView.jsx` |
| User accounts and engagement | Auth, trips, leaderboard | `auth.js`, `trips.js`, `leaderboard.js` |
| Cross-platform delivery | Vite + Capacitor | `frontend/`, `frontend/android/` |
| Admin configuration | Admin API and pages | `routes/admin.js`, `AdminPage.jsx` |

---

## 5.2 Development Environment

### 5.2.1 Programming Languages Used

| Language | Use |
|----------|-----|
| **JavaScript (ES modules)** | Frontend and backend application logic |
| **JSX** | React UI components |
| **SQL** | SQLite schema and queries (embedded in JavaScript) |
| **CSS** | Styling via Tailwind CSS and `styles.css` |
| **JSON** | Configuration (`package.json`, `capacitor.config.json`, API payloads) |

### 5.2.2 Frameworks and Libraries

**Backend** (`backend/package.json`)

| Package | Version (approx.) | Purpose |
|---------|-------------------|---------|
| express | 4.19 | HTTP server and routing |
| better-sqlite3 | 11.10 | SQLite database access |
| bcryptjs | 3.0 | Password hashing |
| zod | 3.23 | Request validation |
| axios | 1.7 | HTTP calls to OSRM/Nominatim |
| cors | 2.8 | Cross-origin support |
| dotenv | 16.4 | Environment variables |

**Frontend** (`frontend/package.json`)

| Package | Purpose |
|---------|---------|
| react, react-dom 18.3 | UI framework |
| vite 5.4 | Build tool and dev server |
| tailwindcss 3.4 | Utility-first CSS |
| axios 1.7 | API client |
| leaflet 1.9 | Planning map |
| maplibre-gl 5.24 | Navigation map |
| @capacitor/core, android, geolocation 7.x | Android shell and GPS |
| polyline 0.2 | Decode route geometry |

*Note: `react-leaflet` and `recharts` are listed in `package.json` but are not used in `frontend/src/`.*

### 5.2.3 IDEs and Tools

| Tool | Use |
|------|-----|
| **Visual Studio Code / Cursor** | Code editing |
| **Node.js** (LTS) | Runtime for backend and npm scripts |
| **Android Studio** | Build and run Capacitor Android project |
| **Chrome DevTools** | Web debugging and network inspection |
| **Vite dev server** | Hot reload during frontend development |

### 5.2.4 Version Control System

The project is structured for use with **Git** (separate `frontend/` and `backend/` folders, npm scripts, documentation). Commits can track changes to source files, configuration, and documentation. If a remote repository (e.g. GitHub) is used, it supports backup and submission evidence for the FYP.

*Author: state your actual repository URL and branch name if applicable.*

### 5.2.5 Operating System Used

Development was carried out on **Microsoft Windows 10/11**. The stack is cross-platform: Node.js and SQLite also run on Linux and macOS. Android builds require Android Studio (Windows, macOS, or Linux).

---

## 5.3 System Configuration and Setup

### 5.3.1 Backend Setup

**Server:** Node.js runs `backend/src/server.js` (Express). No Apache or IIS is used.

**Steps:**

1. Install Node.js LTS.
2. In `backend/`: run `npm install`.
3. Copy `backend/.env.example` to `.env`:
   ```
   PORT=4000
   HOST=0.0.0.0
   CORS_ALLOW_LAN=1
   ```
4. Start server: `npm run dev` or `npm start`.
5. Verify: open `http://localhost:4000/api/health` → `{ "ok": true }`.

**Middleware (Express):**

| Middleware | File | Role |
|------------|------|------|
| CORS | `server.js` | Allows web and Capacitor origins; optional LAN IPs |
| `express.json()` | `server.js` | Parses JSON request bodies |
| Error handler | `server.js` | Returns JSON errors |
| `requireAuth` | `middleware/auth.js` | Validates `X-User-Id` header |
| `requireAdmin` | `middleware/auth.js` | Checks `role === 'admin'` |

**Database:** SQLite file created automatically at `backend/data/app.db` on first run (`database.js`). WAL mode and foreign keys are enabled. No separate MySQL/PostgreSQL server is required.

### 5.3.2 Frontend Setup

**Web development:**

1. In `frontend/`: run `npm install`.
2. Optional: create `.env` with `VITE_API_BASE_URL=http://localhost:4000`.
3. Run `npm run dev` → app at `http://localhost:5173`.

**Production build:**

```bash
npm run build
```

Output: `frontend/dist/`.

**Android (Capacitor):**

1. Set `VITE_API_BASE_URL` in `.env.production` to PC LAN IP (see `frontend/.env.production.example`).
2. Run `npm run cap:sync` (build + sync to `frontend/android/`).
3. Open Android Studio: `npm run cap:open`.
4. Run on device/emulator.

**UI integration:** **Tailwind CSS** is used for layout and components (`tailwindcss`, `styles.css`). No Bootstrap or Material UI. Custom components include `BottomSheet.jsx`, `AuthModal.jsx`, and form inputs.

### 5.3.3 Build Tools and Package Managers

| Tool | Scope |
|------|--------|
| **npm** | Package manager for frontend and backend |
| **Vite** | Frontend bundling, code splitting (MapLibre/Leaflet chunks in `vite.config.js`) |
| **Capacitor CLI** | Sync web build to native Android project |
| **Gradle** (via Android Studio) | Android APK build |

---

## 5.4 Database Implementation

### 5.4.1 Database Schema Design

SQLite stores three main entities: **users**, **trips**, and **leaderboard_entries**. Schema is created in `initSchema()` in `backend/src/services/database.js`.

**Relationships:**

- One user → many trips (`trips.user_id` → `users.user_id`);
- One user → zero or one leaderboard row (`leaderboard_entries.user_id`).

### 5.4.2 SQL Database Tables

**Table: `users`**

| Column | Type | Notes |
|--------|------|-------|
| user_id | TEXT | Primary key |
| username | TEXT | Unique, not null |
| email | TEXT | Optional |
| password_hash | TEXT | bcrypt hash |
| role | TEXT | `user` or `admin` |
| created_at | TEXT | ISO timestamp |

**Table: `trips`**

| Column | Type | Notes |
|--------|------|-------|
| trip_id | TEXT | Primary key |
| user_id | TEXT | Foreign key → users |
| origin_summary, destination_summary | TEXT | Place labels |
| distance_km, duration_min | REAL | Trip metrics |
| fuel_liters, cost_rm, emission_kg | REAL | Actual trip |
| emissions_saved_kg, money_saved_rm, fuel_saved_liters | REAL | Vs alternative |
| vehicle_brand, vehicle_model, route_summary | TEXT | Optional |
| completed_at | TEXT | ISO timestamp |

**Table: `leaderboard_entries`**

| Column | Type | Notes |
|--------|------|-------|
| user_id | TEXT | Primary key |
| user_name | TEXT | Display name |
| total_emissions_saved, total_money_saved | REAL | Cumulative |
| trip_count | INTEGER | Number of qualifying trips |
| joined_at, last_updated | TEXT | Timestamps |

**Index:** `idx_trips_user_completed` on `(user_id, completed_at DESC)`.

### 5.4.3 Stored Procedures or Triggers

**Not applicable.** Logic is implemented in JavaScript services (`trips.js`, `leaderboard.js`, `database.js`) using prepared statements. SQLite triggers and stored procedures were not used.

### 5.4.4 Tools Used for Database Management

| Tool | Use |
|------|-----|
| **better-sqlite3** | Programmatic access from Node.js |
| **DB Browser for SQLite** (optional) | Visual inspection of `backend/data/app.db` |
| **Backend scripts** | `backend/scripts/clear-trip-leaderboard-data.js` for test data reset |

---

## 5.5 Key Modules and Features Developed

Each subsection describes **functionality**, **technologies**, **logic/pseudocode**, and **integration**.

---

### 5.5.1 User Authentication Module

**Functionality:** Register, login, guest access, password change, role-based admin access.

**Technologies:** **bcryptjs** (not Firebase Auth). Session is client-side: `userId`, `username`, and `role` stored in `localStorage` (`authStorage.js`). Protected API calls send header `X-User-Id`.

**Pseudocode — login:**

```
FUNCTION login(username, password):
    user ← findUserByUsername(username)
    IF user is NULL THEN
        RETURN error "Invalid username or password"
    IF NOT bcrypt.compare(password, user.passwordHash) THEN
        RETURN error "Invalid username or password"
    IF password hash is legacy SHA-256 THEN
        upgradeToBcrypt(user, password)
    RETURN sanitize(user)  // no password in response
```

**Pseudocode — protect route:**

```
MIDDLEWARE requireAuth(request):
    userId ← request.header["X-User-Id"]
    IF userId is empty THEN return 401
    user ← getUserById(userId)
    IF user is NULL THEN return 401
    request.authUser ← user
    CONTINUE
```

**Integration:** On success, `AuthModal.jsx` calls `setStoredUser()` and `App.jsx` moves to **car** phase. Trips and password change use `getAuthHeaders()` in `api.js`.

*Screenshot: Figure 5.1 — Login / register screen*

---

### 5.5.2 Vehicle Setup Module

**Functionality:** User selects brand/model (Toyota, Honda, Proton, Perodua) or skips with default **14 km/L**.

**Technologies:** `vehicles.js` data file; `carStorage.js` for `localStorage`.

**Pseudocode:**

```
FUNCTION completeCarSetup(brand, model):
    save { brand, model, useDefaultEngine: false } to localStorage
    set carSetupComplete flag

FUNCTION skipCarSetup():
    save { useDefaultEngine: true } to localStorage
    efficiency for API ← 14 km/L
```

**Integration:** `RouteForm.jsx` sends `efficiencyOverride: { kmPerLiter }` in `POST /api/route`.

*Screenshot: Figure 5.2 — Vehicle setup screen*

---

### 5.5.3 Route Planning and Calculation Module

**Functionality:** Geocode origin/destination, fetch up to three OSRM routes, compute fuel/cost/CO₂, recommend lowest cost.

**Technologies:** `maps.js`, `calc.js`, `routes/routes.js`; Zod validation on request body.

**Pseudocode — cost per route:**

```
FOR each route r:
    distanceKm ← r.distanceMeters / 1000
    kmPerLiter ← efficiencyOverride OR default 14
    fuelLiters ← distanceKm / kmPerLiter
    costRM ← fuelLiters × fuelPricePerLiter
    emissionKg ← fuelLiters × petrol_per_litre_factor

recommended ← route with minimum costRM
comparator ← Route 2 if exists, else highest cost among others
stats ← difference(recommended, comparator)
```

**Pseudocode — OSRM request:**

```
coords ← origin.lon,origin.lat;destination.lon,destination.lat
CALL OSRM with alternatives=3, steps=true, exclude=motorway
IF exclude fails THEN retry without exclude
RETURN up to 3 routes with polyline and steps
```

**Integration:** Frontend `requestRouteEstimate()` → `ResultsDashboard.jsx` + `PlanningMapView.jsx`.

*Screenshot: Figure 5.3 — Route results screen*  
*Code snippet: include `calculateEstimates` loop from `calc.js` in appendix*

---

### 5.5.4 GPS Navigation Module

**Functionality:** Turn-by-turn guidance, route progress, off-route warning (80 m), arrival detection, map follow.

**Technologies:** Capacitor Geolocation / browser GPS; MapLibre GL; `geo.js` (`snapToRoute`); `navigation.js` (turn instructions).

**Pseudocode — snap to route:**

```
FUNCTION snapToRoute(userLat, userLon, routePoints):
    FOR each segment on polyline:
        find closest point and distance to user
    progressKm ← distance along route to closest point
    remainingKm ← totalRouteKm - progressKm
    RETURN progressKm, remainingKm, distanceFromRouteM
```

**Pseudocode — active turn:**

```
FUNCTION getActiveGuidance(lat, lon, steps, progressKm):
    find next step where distanceFromStartKm > progressKm
    distanceToTurnM ← haversine(user, step location)
    RETURN instruction title, subtitle, distance
```

**Integration:** `NavigationOverlay.jsx` updates map via throttled callbacks to `App.jsx`; `NavigationMapView.jsx` renders polyline split (travelled / remaining).

*Screenshot: Figure 5.4 — Navigation screen*

---

### 5.5.5 Trip History and Leaderboard Module

**Functionality:** Save completed trips; aggregate analytics; rank users by cumulative CO₂ saved.

**Technologies:** `trips.js`, `leaderboard.js`; lazy-loaded dashboards.

**Pseudocode — save trip:**

```
FUNCTION completeNavigation():
    savings ← recommendedRoute vs compareRoute
    IF user logged in AND emissionsSaved > 0 THEN
        POST /api/leaderboard/add
    IF user logged in THEN
        POST /api/trips with labels, metrics, savings
```

**Rank tiers:** Bronze (0–10 kg) through Legend (1000+ kg) in `leaderboard.js`.

**Integration:** Triggered from `App.jsx` after navigation **Complete trip**.

*Screenshot: Figure 5.5 — Trip history dashboard*

---

### 5.5.6 Admin Module

**Functionality:** Edit fuel price and emission factor; assign admin/user roles; full admin page at `/admin`.

**Technologies:** `configState.js` (in-memory config); `requireAdmin` middleware.

**Integration:** `AdminPage.jsx`, `AdminConfigModal.jsx`, `AdminUsersPanel.jsx` call `/api/admin/config` and `/api/users`.

*Screenshot: Figure 5.6 — Admin configuration*

---

## 5.6 APIs and Integration

### 5.6.1 Internal and Third-Party APIs

| API | Type | Purpose |
|-----|------|---------|
| **Express REST API** | Internal | Auth, routes, trips, leaderboard, admin |
| **OSRM** | Third-party | `https://router.project-osrm.org/route/v1/driving/` |
| **Nominatim** | Third-party | Geocoding (backend and frontend autocomplete) |
| **OpenStreetMap tiles** | Third-party | Leaflet planning map |
| **OpenFreeMap** | Third-party | MapLibre navigation style |

### 5.6.2 API Endpoints Implemented

**Table 5.2** Backend endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/api/health` | None | Health check |
| POST | `/api/route` | None | Plan and estimate routes |
| POST | `/api/auth/register` | None | Register user |
| POST | `/api/auth/login` | None | Login |
| POST | `/api/auth/change-password` | User | Change password |
| GET | `/api/auth/user/:userId` | None | Get user profile |
| GET | `/api/trips` | User | List trips |
| GET | `/api/trips/analytics` | User | Trip statistics |
| POST | `/api/trips` | User | Record trip |
| GET | `/api/leaderboard` | None | Top users |
| GET | `/api/leaderboard/user/:userId` | None | User stats |
| POST | `/api/leaderboard/add` | None* | Add savings |
| GET | `/api/admin/config` | Admin | Get config |
| POST | `/api/admin/config` | Admin | Update config |
| GET | `/api/users` | Admin | List users |
| PATCH | `/api/users/:userId/role` | Admin | Set role |

*Verifies user exists in body.

### 5.6.3 JSON Payload Structure

**Example — `POST /api/route` request:**

```json
{
  "origin": "Ipoh, Perak",
  "destination": "George Town, Penang",
  "originLat": 4.5975,
  "originLon": 101.0901,
  "destinationLat": 5.4141,
  "destinationLon": 100.3288,
  "efficiencyOverride": { "kmPerLiter": 15.5 }
}
```

**Example — response (abbreviated):**

```json
{
  "origin": { "lat": 4.5975, "lon": 101.0901, "displayName": "..." },
  "destination": { "lat": 5.4141, "lon": 100.3288, "displayName": "..." },
  "routes": [
    {
      "id": "Route 1",
      "distanceKm": 98.2,
      "durationMin": 95,
      "costRM": 15.84,
      "emissionKg": 14.65,
      "fuelUsedLiters": 6.34,
      "polyline": "encoded..."
    }
  ],
  "recommendation": {
    "routeId": "Route 1",
    "by": "lowest_cost",
    "stats": { "costSavedRm": 1.2, "fasterByMinutes": 0 }
  }
}
```

**Example — authenticated header:**

```
X-User-Id: user_1710000000_abc123
Content-Type: application/json
```

### 5.6.4 Authentication Mechanisms

This project does **not** use JWT or OAuth2. Authentication uses:

1. **bcrypt** password hashing on register/login;
2. **Client storage** of `userId` after login;
3. **Header-based identification** (`X-User-Id`) on protected routes;
4. **Role checks** for admin endpoints (`requireAdmin`).

This is sufficient for a prototype but would need JWT or session tokens for production.

---

## 5.7 Network Configuration

### 5.7.1 Hosting Setup

| Component | Environment |
|-----------|-------------|
| Backend | Local PC, listens on all interfaces (`0.0.0.0`) |
| Web frontend | Vite dev server (`localhost:5173`) or static `dist/` |
| Android app | Capacitor WebView; API URL points to PC LAN IP |

No cloud server (AWS, Azure, etc.) is configured in the repository.

### 5.7.2 Port Configuration

| Service | Port |
|---------|------|
| Backend API | **4000** (default, `PORT` in `.env`) |
| Vite dev server | **5173** |
| Android WebView | Uses configured API URL (includes port 4000) |

Windows Firewall must allow inbound TCP on port 4000 for phone testing (`backend/scripts/allow-firewall-port.ps1`).

### 5.7.3 Deployment to Server or Live Environment

**Current deployment steps:**

1. Start backend on PC: `cd backend && npm run dev`.
2. Web: `cd frontend && npm run dev` or serve `dist/` with any static host.
3. Android: `npm run cap:sync`, install APK from Android Studio.
4. On phone: **Server Settings** → enter `http://<PC_IP>:4000` → **Test connection** → **Save**.

Future improvement: deploy backend to a VPS with HTTPS and a public domain.

---

## 5.8 Security Measures

### 5.8.1 Input Validation, Encryption, HTTPS

| Measure | Implementation |
|---------|----------------|
| **Input validation** | Zod schemas on all POST/PATCH routes (`routes/*.js`) |
| **Password encryption** | bcrypt, 12 rounds (`auth.js`) |
| **HTTPS** | Not enforced in LAN demo; HTTP used between phone and PC |
| **SQL injection** | Parameterised queries via better-sqlite3 prepared statements |

### 5.8.2 Role-Based Access Control (RBAC)

| Role | Access |
|------|--------|
| Guest | Route plan, navigate; no trip save |
| `user` | Trips, profile, leaderboard write |
| `admin` | Config, user roles, admin page |

Enforced in `requireAuth` and `requireAdmin` (`middleware/auth.js`). Last admin cannot be demoted (`auth.js`).

### 5.8.3 Error Handling and Logging

| Layer | Handling |
|-------|----------|
| Frontend | `ErrorBoundary.jsx` catches render errors; API errors shown in UI |
| Backend | Global error middleware returns JSON `{ error: message }` |
| Development logs | Request method/path logged when `NODE_ENV !== 'production'` |
| Validation errors | HTTP 400 with Zod `details` |

---

## 5.9 Challenges Encountered and Solutions

### 5.9.1 Challenges Encountered

1. **Mobile cannot reach `localhost` backend** — Phone treats localhost as itself, not the development PC.
2. **CORS blocks LAN requests** — Browser/WebView rejects cross-origin calls from Capacitor to PC IP.
3. **OSRM `exclude=motorway` rejected** — Some requests return HTTP 400 from public OSRM.
4. **Navigation crash on start** — Polyline coordinates passed in wrong format to map layer.
5. **Leaderboard UI crash on mobile** — Rank object rendered as React child instead of tier name.
6. **Autocomplete clipped on mobile** — Dropdown hidden under top bar in bottom sheet layout.
7. **GPS causes UI lag** — Every GPS tick re-rendered entire app tree.
8. **Admin config lost on restart** — Config stored in memory only (`configState.js`).

### 5.9.2 Solutions

| Challenge | Solution |
|-----------|----------|
| Mobile localhost | `ServerSettings.jsx` + `VITE_API_BASE_URL`; user sets PC LAN IP |
| CORS | `CORS_ALLOW_LAN=1` and private IP check in `server.js` |
| OSRM exclude | Fallback: retry routing without `exclude` parameter |
| Navigation crash | Separate helpers for `{lat,lon}` objects vs `[lat,lon]` arrays in `NavigationMapView.jsx` |
| Leaderboard crash | Display `rank.name` in `Leaderboard.jsx` |
| Autocomplete | Portal dropdown to `document.body`; z-index fix |
| GPS lag | Throttle position updates to parent (~200 ms) in `NavigationOverlay.jsx` |
| Config persistence | Documented as limitation; defaults in `defaults.js`; future: save to SQLite |

---

## 5.10 Summary

This chapter described the **implementation** of Intelligent Route Cost & Efficiency. The system was built with **JavaScript**, **React**, **Express**, and **SQLite**, packaged for web and **Android** via **Capacitor**. Backend and frontend setup, database tables, and seven key modules were explained with pseudocode and API details.

Security uses **bcrypt**, **Zod validation**, and **role-based middleware**. Deployment targets a **LAN** setup on port **4000**, with server URL configuration for mobile clients.

The implementation matches the design in Chapter 4: phase-based UI, OSRM/Nominatim integration, lowest-cost recommendation, GPS navigation, and trip/leaderboard persistence.

**Future improvements:**

- JWT or session-token authentication;
- Persist admin config in database;
- Self-hosted OSRM/Nominatim;
- HTTPS and cloud deployment;
- Automated unit and integration tests;
- iOS Capacitor build;
- Remove unused npm dependencies (`react-leaflet`, `recharts`).

Chapter 6 presents **testing and evaluation** of this implementation.

---

*Author reminder: Insert Figures 5.1–5.6 (screenshots) and optional code snippets in Appendix. Replace Git/repository note with your actual VCS details.*
