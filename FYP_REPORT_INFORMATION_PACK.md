# FYP Report Information Pack

> Generated from codebase inspection. Evidence paths cite actual project files.  
> **Note:** No git history was available in the workspace at generation time.

---

## 1. Project Overview

| Item | Detail | Evidence |
|------|--------|----------|
| **Project title (UI)** | Intelligent Route Cost & Efficiency | `frontend/index.html` (line 9, `<title>`) |
| **App name (Android)** | Route Estimator | `frontend/capacitor.config.json` (`appName`) |
| **Package names** | `route-estimator-frontend`, `route-estimator-backend` | `frontend/package.json`, `backend/package.json` |
| **Type of application** | Cross-platform **web + hybrid mobile (Android)** route planning and navigation application with REST API backend | `frontend/package.json` (Capacitor), `backend/src/server.js` |
| **Main purpose** | Compare multiple driving routes by fuel cost, CO₂ emissions, and duration; recommend the lowest-cost route; provide GPS navigation and track user savings | `backend/src/utils/calc.js`, `frontend/src/App.jsx`, `docs/PROJECT_OVERVIEW.md` |
| **Target users** | Malaysian drivers (petrol vehicles); registered users for history/leaderboard; administrators for configuration | `backend/src/services/maps.js` (`countrycodes: "my"`), `frontend/src/data/vehicles.js`, role logic in `backend/src/services/auth.js` |
| **Main problem** | Generic navigation apps optimize for time/distance, not fuel cost or environmental impact; drivers lack an integrated tool to choose fuel-efficient routes and quantify savings | Inferred from calculation module purpose in `backend/src/utils/calc.js` and UI in `frontend/src/components/ResultsDashboard.jsx` |
| **Why important** | Rising fuel prices and transport emissions in Malaysia; helps users make informed route choices and gamifies CO₂/money savings | Defaults in `backend/src/config/defaults.js`; leaderboard in `backend/src/services/leaderboard.js` |

### Short summary (approx. 200 words)

**Intelligent Route Cost & Efficiency** (branded **Route Estimator** on Android) is a Final Year Project application that helps drivers plan trips with a focus on **fuel economy and carbon savings**, not only travel time. The system consists of a **React + Vite** frontend (responsive web and **Capacitor Android** app) and a **Node.js + Express** backend with **SQLite** persistence. Users authenticate (or continue as guest), configure a vehicle or use a default efficiency of **14 km/L**, and search for routes using **Nominatim** geocoding. The backend requests up to three alternatives from **OSRM**, applies a petrol cost and CO₂ model, and recommends the route with the **lowest estimated fuel cost (RM)**. Results show distance, duration, fuel used, emissions, and comparison statistics versus an alternative route. Users can start **GPS turn-by-turn navigation** on a **MapLibre** map with route progress tracking. Logged-in users accumulate **leaderboard ranks** (Bronze to Legend based on kg CO₂ saved) and **trip history analytics**. Administrators can adjust fuel price and emission factors and manage user roles. The project uses free/open map services and is designed for LAN deployment (PC backend + phone client on the same Wi‑Fi).

---

## 2. Problem Statement

### What problem exists before this system?

Drivers typically rely on mainstream map applications that prioritize **fastest arrival** or **shortest distance**. These tools do not consistently expose **fuel cost (RM)**, **litres consumed**, or **CO₂ emissions** per route alternative, nor do they recommend a path based on **lowest fuel cost** using vehicle-specific efficiency.

### Who is affected?

- **Daily commuters and private car owners** in Malaysia using petrol vehicles (supported brands in `frontend/src/data/vehicles.js`: Toyota, Honda, Proton, Perodua).
- **Environmentally conscious users** who want to track cumulative emissions savings (`backend/src/services/leaderboard.js`).
- **Administrators/researchers** who need configurable emission and fuel price assumptions (`backend/src/routes/admin.js`, `backend/src/config/configState.js`).

### Limitations of current/manual/existing solutions

| Limitation | Evidence in project design |
|------------|---------------------------|
| No unified cost + emissions comparison across route alternatives | Addressed by `calculateEstimates()` in `backend/src/utils/calc.js` |
| Manual spreadsheet calculations for fuel cost | Replaced by automated formulas using distance and km/L |
| Generic routing without fuel-efficiency bias | OSRM called with `exclude=motorway` in `backend/src/services/maps.js` |
| No personal trip/savings history in free map apps | SQLite `trips` table + `TripHistoryDashboard.jsx` |
| Mobile users cannot reach localhost backend | `ServerSettings.jsx` + `CORS_ALLOW_LAN` in `backend/.env.example` |

### Why a software solution is needed?

A software system can **integrate geocoding, routing, cost modelling, GPS navigation, and user accounts** in one workflow. The codebase implements this end-to-end pipeline from `RouteForm.jsx` → `POST /api/route` → `ResultsDashboard.jsx` → `NavigationOverlay.jsx` → trip/leaderboard persistence.

---

## 3. Project Objectives

1. **Develop a route comparison system** that retrieves multiple driving routes via OSRM and computes fuel cost (RM), fuel volume (L), and CO₂ (kg) for each route using configurable efficiency and price factors (`backend/src/services/maps.js`, `backend/src/utils/calc.js`).

2. **Recommend the most fuel-cost-efficient route** and present clear comparison metrics (money, fuel, emissions, time) against an alternative route (`pickFuelEfficientRoute`, `simpleRouteDiff` in `backend/src/utils/calc.js`; `ResultsDashboard.jsx`).

3. **Implement GPS navigation** with turn-by-turn guidance, route progress tracking, off-route detection, and arrival detection on mobile and web (`NavigationOverlay.jsx`, `NavigationMapView.jsx`, `frontend/src/services/geolocation.js`).

4. **Provide user engagement features** including registration/login, trip history with analytics, and a gamified leaderboard with rank tiers (`backend/src/routes/auth.js`, `backend/src/routes/trips.js`, `backend/src/services/leaderboard.js`, `Leaderboard.jsx`).

5. **Deliver cross-platform deployment** as a responsive web application and Android hybrid app communicating with a LAN-hosted REST API (`frontend/capacitor.config.json`, `frontend/src/utils/platform.js`, `backend/src/server.js`).

---

## 4. Expected Deliverables

| Deliverable | Status | Evidence |
|-------------|--------|----------|
| Web application (React SPA) | ✅ Exists | `frontend/src/`, `npm run dev` / `npm run build` in `frontend/package.json` |
| Android mobile application | ✅ Exists | `frontend/android/`, `frontend/capacitor.config.json`, `@capacitor/android` |
| REST API backend | ✅ Exists | `backend/src/server.js`, version `0.1.0` |
| SQLite database | ✅ Exists | `backend/src/services/database.js` → `backend/data/app.db` |
| User authentication (register/login/guest) | ✅ Exists | `AuthModal.jsx`, `backend/src/routes/auth.js` |
| Password change | ✅ Exists | `ProfileSettings.jsx`, `POST /api/auth/change-password` |
| Admin module (config + user roles) | ✅ Exists | `AdminPage.jsx`, `AdminConfigModal.jsx`, `AdminUsersPanel.jsx`, `/api/admin`, `/api/users` |
| Trip history & analytics dashboard | ✅ Exists | `TripHistoryDashboard.jsx`, `GET /api/trips`, `GET /api/trips/analytics` |
| Leaderboard & rank system | ✅ Exists | `Leaderboard.jsx`, `backend/src/services/leaderboard.js` |
| Route planning map (Leaflet) | ✅ Exists | `PlanningMapView.jsx` |
| Navigation map (MapLibre) | ✅ Exists | `NavigationMapView.jsx` |
| Technical documentation | ✅ Exists | `docs/PROJECT_OVERVIEW.md`, `docs/PROJECT_DETAILED.md`, this file |
| Automated unit/integration test suite | ❌ Not found | No `*.test.js` / `*.spec.js` under `frontend/src` or `backend/src` |
| Cloud production deployment | ❌ Not configured | LAN/local deployment only per `.env.example` files |
| Payment / email / AI modules | ❌ Not in scope | Not present in codebase |

---

## 5. Scope of the Project

### Included

- Petrol vehicle route cost and CO₂ estimation (single fuel type model).
- Malaysia-biased geocoding and place search.
- Up to 3 OSRM route alternatives with motorway exclusion attempt.
- Vehicle catalogue (4 brands) or default 14 km/L efficiency.
- Guest mode (no persistence), user mode (trips/leaderboard), admin mode.
- GPS navigation with in-app guidance (OSRM steps).
- Admin-editable fuel price, emission factor, and feature flags (in-memory config).
- Server URL configuration for mobile LAN access.

### Not included

- iOS native build (only Android Capacitor project present).
- Diesel/electric/hybrid-specific emission models (only petrol `petrol_per_litre` in config).
- Real-time fuel price API integration (static/admin-configured price).
- JWT/OAuth session tokens (uses `X-User-Id` header).
- Offline routing or offline maps.
- Payment, notifications, email, file upload, or AI/ML routing.
- Persisted admin config across server restart (`configState.js` is in-memory).
- Automated CI/CD or cloud hosting configuration.

### User roles

| Role | Description | Evidence |
|------|-------------|----------|
| **Guest** | Can plan/navigate; no trip save | `App.jsx` — `handleAuthComplete(null)` sets `userId` null |
| **User** | Trips, leaderboard, profile | `requireAuth` in `backend/src/middleware/auth.js` |
| **Admin** | Config + user role management | `requireAdmin`, `AdminPage.jsx`, role `admin` in `users` table |

### Main modules

1. Authentication & profile  
2. Vehicle setup  
3. Route search & autocomplete  
4. Route calculation & results  
5. Maps (planning + navigation)  
6. Trip history & analytics  
7. Leaderboard & ranks  
8. Admin configuration & user management  
9. Server connection settings  

### Assumptions & constraints

- Backend and OSRM/Nominatim require **network connectivity**.
- Android demo assumes **same Wi‑Fi LAN** as development PC (`frontend/.env.production.example`).
- OSRM public demo server rate limits apply (`backend/src/services/maps.js`).
- Password minimum length: **6 characters** (`backend/src/routes/auth.js`, Zod schema).
- Off-route threshold: **80 m**; arrival threshold: **80 m** (`NavigationOverlay.jsx`, `OFF_ROUTE_M`).

---

## 6. Literature Review Support

| # | Topic | Relevance | Source types | Keywords |
|---|-------|-----------|----------------|----------|
| 1 | **Green routing / eco-routing** | Core to fuel-efficient route recommendation | Journal, conference | `eco-routing`, `energy-efficient routing`, `CO2-aware navigation` |
| 2 | **Vehicle fuel consumption modelling** | km/L efficiency and litres = distance/efficiency | Journal, transport reports | `fuel consumption model`, `km per liter`, `petrol vehicle efficiency` |
| 3 | **Transport CO₂ emission factors** | `petrol_per_litre: 2.31` in defaults | Government/industry docs, IPCC | `CO2 emission factor petrol`, `Malaysia transport emissions` |
| 4 | **OpenStreetMap routing (OSRM)** | Primary routing engine | Official docs, papers on OSM routing | `OSRM`, `Open Source Routing Machine`, `alternative routes` |
| 5 | **Geocoding & LBS** | Nominatim search/reverse geocode | Documentation, survey papers | `Nominatim`, `reverse geocoding`, `location-based services` |
| 6 | **Mobile hybrid app development** | Capacitor Android architecture | Documentation, comparative studies | `Capacitor hybrid app`, `Progressive Web App vs native wrapper` |
| 7 | **Gamification for sustainable behaviour** | Leaderboard ranks by emissions saved | HCI, sustainability journals | `gamification carbon savings`, `behaviour change transport app` |
| 8 | **Usability of navigation interfaces** | Turn-by-turn UX, mobile bottom sheet | HCI, usability studies | `mobile navigation usability`, `turn-by-turn guidance` |

---

## 7. Requirements Analysis

### 7.1 Functional Requirements

| ID | Requirement | Module / file | Role | Priority |
|----|-------------|---------------|------|----------|
| FR-01 | User shall register with username and password (optional email) | `AuthModal.jsx`, `POST /api/auth/register` | Guest | High |
| FR-02 | User shall log in and remain authenticated via stored userId | `authStorage.js`, `POST /api/auth/login` | User | High |
| FR-03 | User may continue as guest without account | `AuthModal.jsx`, `App.jsx` | Guest | Medium |
| FR-04 | User shall select vehicle brand/model or skip with default 14 km/L | `CarSetupPanel.jsx`, `carStorage.js`, `RouteForm.jsx` | All | High |
| FR-05 | User shall enter origin/destination with autocomplete | `RouteForm.jsx`, `AutocompleteInput.jsx`, `nominatim.js` | All | High |
| FR-06 | User shall use current GPS location as origin | `RouteForm.jsx`, `geolocation.js` | All | Medium |
| FR-07 | System shall calculate up to 3 route alternatives | `maps.js`, `POST /api/route` | All | High |
| FR-08 | System shall compute fuel cost, litres, CO₂ per route | `calc.js` | All | High |
| FR-09 | System shall recommend lowest cost route | `pickFuelEfficientRoute` in `calc.js` | All | High |
| FR-10 | System shall display route comparison statistics | `ResultsDashboard.jsx` | All | High |
| FR-11 | User shall view routes on planning map | `PlanningMapView.jsx`, `MapPreview.jsx` | All | High |
| FR-12 | User shall start GPS navigation on recommended route | `NavigationOverlay.jsx`, `NavigationMapView.jsx` | All | High |
| FR-13 | System shall provide turn-by-turn instructions | `navigation.js`, OSRM steps in `maps.js` | All | High |
| FR-14 | User shall complete trip and save stats (if logged in) | `App.jsx` `completeNavigation`, `POST /api/trips` | User | High |
| FR-15 | System shall update leaderboard on savings | `POST /api/leaderboard/add`, `leaderboard.js` | User | Medium |
| FR-16 | User shall view trip history and analytics | `TripHistoryDashboard.jsx`, `GET /api/trips/analytics` | User | High |
| FR-17 | User shall view leaderboard and personal rank | `Leaderboard.jsx`, `GET /api/leaderboard` | All | Medium |
| FR-18 | User shall change password | `ProfileSettings.jsx`, `POST /api/auth/change-password` | User | Medium |
| FR-19 | Admin shall edit fuel price and emission factors | `AdminPage.jsx`, `POST /api/admin/config` | Admin | High |
| FR-20 | Admin shall promote/demote user roles | `AdminUsersPanel.jsx`, `PATCH /api/users/:id/role` | Admin | Medium |
| FR-21 | User shall configure backend API URL (mobile) | `ServerSettings.jsx`, `api.js` `setApiBaseUrl` | All | High |
| FR-22 | Admin shall access dedicated admin page at `/admin` | `AdminPage.jsx`, `App.jsx` | Admin | Medium |
| FR-23 | System shall validate API request payloads | Zod schemas in `routes/*.js` | System | High |
| FR-24 | User shall log out and clear session | `App.jsx` `handleLogout` | User | Medium |

### 7.2 Non-Functional Requirements

| ID | Requirement | Category | How supported | Evidence |
|----|-------------|----------|---------------|----------|
| NFR-01 | Passwords stored hashed | Security | bcrypt (12 rounds); legacy SHA-256 upgrade | `backend/src/services/auth.js` |
| NFR-02 | Protected routes require valid user | Security | `requireAuth`, `requireAdmin` middleware | `backend/src/middleware/auth.js` |
| NFR-03 | Admin config restricted to admin role | Security | `requireAdmin` on `/api/admin/*`, `/api/users` | `admin.js`, `users.js` |
| NFR-04 | Responsive UI for mobile and desktop | Usability | Bottom sheet vs sidebar via `usePlatformLayout()` | `frontend/src/utils/platform.js`, `BottomSheet.jsx` |
| NFR-05 | Mobile font size prevents iOS zoom | Usability | CSS `font-size: 16px` on inputs | `frontend/src/styles.css` |
| NFR-06 | Graceful UI error recovery | Reliability | `ErrorBoundary.jsx` | `frontend/src/main.jsx` |
| NFR-07 | API input validation | Reliability | Zod on all POST/PATCH bodies | `routes/routes.js`, `auth.js`, etc. |
| NFR-08 | Lazy loading for performance | Performance | `React.lazy` for maps, modals | `MapPreview.jsx`, `App.jsx` |
| NFR-09 | GPS update throttling during navigation | Performance | 200 ms interval lift to parent | `NavigationOverlay.jsx` |
| NFR-10 | SQLite WAL mode for concurrency | Performance | `journal_mode = WAL` | `database.js` |
| NFR-11 | CORS control for web/mobile origins | Security | Configurable origins + LAN option | `backend/src/server.js` |
| NFR-12 | Maintainable modular structure | Maintainability | Separated routes/services/utils/components | Project folder structure |
| NFR-13 | Configurable calculation constants | Maintainability | Admin config API + defaults | `configState.js`, `defaults.js` |
| NFR-14 | Reduced motion accessibility | Usability | `prefers-reduced-motion` CSS | `frontend/src/styles.css` |

---

## 8. Methodology

### Recommended methodology: **Agile / Iterative Incremental Development**

### Why it fits

The codebase shows **evolving features across modules** (auth → routing → navigation → trips → admin → performance), **frequent UI refinements** (bottom sheet, platform layout, lazy loading), and **incremental database schema migration** (ALTER TABLE for `role`, `fuel_saved_liters` in `database.js`). There is no single big-bang waterfall specification document; instead, working software is organized in vertical slices (e.g., full trip flow in `App.jsx`).

### Main phases (mapped to this application)

| Phase | Application activity | Evidence |
|-------|---------------------|----------|
| **Planning & requirements** | Define route cost model, user roles, Malaysia scope | `defaults.js`, `vehicles.js`, docs |
| **Design** | Client-server architecture, SQLite schema, phase-based UI | `database.js`, `App.jsx` phases |
| **Implementation — Sprint 1** | Backend API, OSRM integration, calculations | `server.js`, `maps.js`, `calc.js` |
| **Implementation — Sprint 2** | Frontend forms, results, Leaflet map | `RouteForm.jsx`, `ResultsDashboard.jsx`, `PlanningMapView.jsx` |
| **Implementation — Sprint 3** | Auth, SQLite users, leaderboard, trips | `auth.js`, `trips.js`, `leaderboard.js` |
| **Implementation — Sprint 4** | Capacitor Android, geolocation, MapLibre navigation | `capacitor.config.json`, `NavigationMapView.jsx` |
| **Implementation — Sprint 5** | Admin, profile, performance, documentation | `AdminPage.jsx`, lazy imports, `docs/` |
| **Testing** | Manual functional & usability testing (no automated suite) | No test files found |
| **Evaluation** | User task completion, savings accuracy review | To be completed by student |
| **Documentation & deployment** | Build APK, LAN setup guides | `.env.example`, `ServerSettings.jsx` |

---

## 9. System Design

### 9.1 System Architecture

| Layer | Technology | Path / notes |
|-------|------------|--------------|
| **Frontend** | React 18.3, Vite 5.4 | `frontend/package.json`, `frontend/src/main.jsx` |
| **Styling** | Tailwind CSS 3.4 | `frontend/package.json`, `frontend/src/styles.css` |
| **Mobile shell** | Capacitor 7.2 (Android) | `frontend/capacitor.config.json`, `frontend/android/` |
| **Backend runtime** | Node.js (ES modules) | `backend/package.json` `"type": "module"` |
| **Backend framework** | Express 4.19 | `backend/src/server.js` |
| **Database** | SQLite via better-sqlite3 11.10 | `backend/src/services/database.js` |
| **Validation** | Zod 3.23 | Route handlers |
| **HTTP client** | Axios 1.7 | Frontend & backend external calls |
| **Auth** | Custom header `X-User-Id` + bcrypt passwords | `authStorage.js`, `middleware/auth.js` |
| **Deployment** | Local/LAN: backend on port 4000; frontend dev 5173 or static `dist/` | `backend/.env.example`, `vite.config.js` |

**External services/libraries:**

| Service | Use | File |
|---------|-----|------|
| OSRM | Driving routes, polylines, steps | `backend/src/services/maps.js` |
| Nominatim | Geocoding (backend + frontend) | `maps.js`, `nominatim.js` |
| OpenStreetMap tiles | Planning map | `PlanningMapView.jsx` |
| OpenFreeMap Liberty | Navigation map style | `NavigationMapView.jsx` |
| Leaflet 1.9 | Planning map rendering | `PlanningMapView.jsx` |
| MapLibre GL 5.24 | Navigation map rendering | `NavigationMapView.jsx` |
| polyline 0.2 | Decode route geometry | `geo.js`, `NavigationMapView.jsx` |

**Unused dependencies (listed in package.json but not imported in src):** `react-leaflet`, `recharts` (`frontend/package.json`).

### 9.2 Suggested Diagrams

| Diagram | Chapter | Should show |
|---------|---------|-------------|
| **System architecture** | Ch. 4 | React client, Express API, SQLite, OSRM, Nominatim, Capacitor Android |
| **Use case** | Ch. 3–4 | Actors: Guest, User, Admin; use cases: Plan Route, Navigate, View History, Manage Config |
| **Activity — Route planning** | Ch. 4 | Auth → car setup → form submit → API → results |
| **Activity — Navigation** | Ch. 4 | Start nav → GPS watch → snap to route → complete trip → save |
| **Sequence — POST /api/route** | Ch. 4–5 | Client → Express → Nominatim → OSRM → calc.js → JSON response |
| **Sequence — Trip completion** | Ch. 5 | NavigationOverlay → App.jsx → leaderboard API + trips API |
| **ERD** | Ch. 4 | `users`, `trips`, `leaderboard_entries` relationships |
| **Component diagram** | Ch. 4–5 | App.jsx phases → major components |
| **UI navigation flow** | Ch. 4 | Phase state machine: auth, car, search, results, navigate |
| **Deployment diagram** | Ch. 4 | Phone (APK) + PC (backend) on LAN Wi‑Fi |

### 9.3 Database Design

**Source:** `backend/src/services/database.js` (lines 33–69)

#### Table: `users`

| Column | Type | Notes |
|--------|------|-------|
| user_id | TEXT | **PK** |
| username | TEXT | UNIQUE, NOT NULL |
| email | TEXT | Optional |
| password_hash | TEXT | NOT NULL |
| role | TEXT | DEFAULT `'user'` (`admin` or `user`) |
| created_at | TEXT | ISO timestamp |

#### Table: `leaderboard_entries`

| Column | Type | Notes |
|--------|------|-------|
| user_id | TEXT | **PK** |
| user_name | TEXT | NOT NULL |
| total_emissions_saved | REAL | DEFAULT 0 |
| total_money_saved | REAL | DEFAULT 0 |
| trip_count | INTEGER | DEFAULT 0 |
| joined_at | TEXT | |
| last_updated | TEXT | |

#### Table: `trips`

| Column | Type | Notes |
|--------|------|-------|
| trip_id | TEXT | **PK** |
| user_id | TEXT | **FK → users(user_id)** |
| origin_summary | TEXT | |
| destination_summary | TEXT | |
| distance_km | REAL | |
| duration_min | REAL | |
| fuel_liters | REAL | |
| cost_rm | REAL | |
| emission_kg | REAL | |
| emissions_saved_kg | REAL | |
| money_saved_rm | REAL | |
| fuel_saved_liters | REAL | Added via migration |
| vehicle_brand | TEXT | Optional |
| vehicle_model | TEXT | Optional |
| route_summary | TEXT | Optional |
| completed_at | TEXT | |

**Index:** `idx_trips_user_completed` on `(user_id, completed_at DESC)`.

**Relationships:**

- One `users` → many `trips`
- One `users` → zero or one `leaderboard_entries` (by user_id)

### 9.4 UI/UX Design

#### Phase-based screens (`App.jsx` `phase` state)

| Phase | UI | Component(s) |
|-------|-----|--------------|
| auth | Login/register/guest modal | `AuthModal.jsx` |
| car | Vehicle brand/model selection | `CarSetupPanel.jsx` (mobile sheet), `CarSetupModal.jsx` (desktop) |
| search | Route form | `RouteForm.jsx` in `BottomSheet` or sidebar |
| results | Route cards + comparison | `ResultsDashboard.jsx` |
| navigate | Full-screen map + overlay | `MapPreview.jsx`, `NavigationOverlay.jsx` |

#### Modal overlays (lazy-loaded)

- `Leaderboard.jsx`
- `TripHistoryDashboard.jsx`
- `ProfileSettings.jsx`
- `AdminConfigModal.jsx` (in-app admin shortcut)
- `ServerSettings.jsx`

#### Navigation flow

```
Auth → Car setup → Search → Calculate → Results → [Start navigation] → Navigate → Complete → Results/History
```

#### Layout rules

- **Native or narrow web:** bottom sheet (`useBottomSheet`) — `platform.js`
- **Wide web (≥768px):** left sidebar — `useSidebar`

#### Access control (UI)

| Feature | Guest | User | Admin |
|---------|-------|------|-------|
| Plan route | ✅ | ✅ | ✅ |
| Navigate | ✅ | ✅ | ✅ |
| Trip history | ❌ | ✅ | ✅ |
| Leaderboard write | ❌ | ✅ | ✅ |
| Profile / change password | ❌ | ✅ | ✅ |
| Admin config | ❌ | ❌ | ✅ |
| `/admin` page | ❌ | ❌ | ✅ |

---

## 10. Implementation Details

### 10.1 Module Table

| Module | Purpose | Role | Key files | Main components/functions | Validation / security |
|--------|---------|------|-----------|---------------------------|------------------------|
| **Authentication** | Register, login, guest, password change | All / User | `auth.js`, `AuthModal.jsx`, `ProfileSettings.jsx` | `registerUser`, `loginUser`, `changeUserPassword`, `setStoredUser` | bcrypt; Zod min password 6; duplicate username check |
| **Vehicle setup** | Store brand/model or default engine | All | `CarSetupPanel.jsx`, `carStorage.js`, `vehicles.js` | `saveCarConfigured`, `saveCarWithDefaultEngine` | localStorage only |
| **Route search** | Origin/destination input | All | `RouteForm.jsx`, `AutocompleteInput.jsx` | `handleSubmit`, `useCurrentLocationAsOrigin` | Sends lat/lon with autocomplete selection |
| **Route API** | Geocode + OSRM + calculate | System | `routes.js`, `maps.js`, `calc.js` | `getRouteAlternatives`, `calculateEstimates` | Zod coord pair validation |
| **Results UI** | Display routes & savings | All | `ResultsDashboard.jsx` | `buildTripMetrics`, comparison cards | Handles empty routes |
| **Planning map** | Show route polylines | All | `PlanningMapView.jsx` | Leaflet layers, polyline decode | Safe polyline decode try/catch |
| **Navigation** | GPS + guidance + map | All | `NavigationOverlay.jsx`, `NavigationMapView.jsx`, `navigation.js`, `geo.js` | `snapToRoute`, `getActiveGuidance`, camera smoother | Off-route 80 m; permission handling |
| **Trip persistence** | Save completed trips | User | `trips.js`, `TripHistoryDashboard.jsx` | `recordTrip`, `getTripAnalyticsForUser` | `requireAuth` |
| **Leaderboard** | Rank users by CO₂ saved | User / All | `leaderboard.js`, `Leaderboard.jsx` | `addEmissionsSaved`, `getRankTier` | Positive emissions required for add |
| **Admin** | Config + roles | Admin | `AdminPage.jsx`, `admin.js`, `users.js` | `updateConfig`, `setUserRole` | `requireAdmin`; cannot remove last admin |
| **Server settings** | Mobile API URL | All | `ServerSettings.jsx`, `api.js` | `checkApiHealthAt`, `setApiBaseUrl` | Health check timeout 10 s |
| **Error handling** | Catch render errors | All | `ErrorBoundary.jsx` | `componentDidCatch` | Reload button |

### 10.2 Authentication & Authorization

- **Registration:** `POST /api/auth/register` — creates user in SQLite (`auth.js`).
- **Login:** `POST /api/auth/login` — verifies bcrypt/hash; returns sanitized user (no password).
- **Session:** Frontend stores `userId`, `username`, `userRole` in `localStorage` (`authStorage.js`).
- **Authorized requests:** Header `X-User-Id: <userId>` (`getAuthHeaders()`).
- **Admin check:** Server loads user and verifies `role === 'admin'`.

### 10.3 CRUD Operations

| Entity | Create | Read | Update | Delete |
|--------|--------|------|--------|--------|
| Users | Register | GET user, list (admin) | Change password, role | Not exposed via API |
| Trips | POST /api/trips | GET /api/trips, analytics | — | Script only (`clear-trip-leaderboard-data.js`) |
| Leaderboard | POST /add (upsert) | GET /api/leaderboard | Increment on trip | Script only |
| Admin config | — | GET /api/admin/config | POST /api/admin/config | In-memory only |

### 10.4 API Endpoints Summary

| Method | Path | Auth | Handler file |
|--------|------|------|--------------|
| GET | `/api/health` | None | `server.js` |
| POST | `/api/route` | None | `routes/routes.js` |
| POST | `/api/auth/register` | None | `routes/auth.js` |
| POST | `/api/auth/login` | None | `routes/auth.js` |
| POST | `/api/auth/change-password` | User | `routes/auth.js` |
| GET | `/api/auth/user/:userId` | None | `routes/auth.js` |
| GET | `/api/trips` | User | `routes/trips.js` |
| GET | `/api/trips/analytics` | User | `routes/trips.js` |
| POST | `/api/trips` | User | `routes/trips.js` |
| GET | `/api/leaderboard` | None | `routes/leaderboard.js` |
| GET | `/api/leaderboard/user/:userId` | None | `routes/leaderboard.js` |
| GET | `/api/leaderboard/ranks` | None | `routes/leaderboard.js` |
| POST | `/api/leaderboard/add` | None* | `routes/leaderboard.js` |
| GET | `/api/admin/config` | Admin | `routes/admin.js` |
| POST | `/api/admin/config` | Admin | `routes/admin.js` |
| GET | `/api/users` | Admin | `routes/users.js` |
| PATCH | `/api/users/:userId/role` | Admin | `routes/users.js` |

*Leaderboard add verifies userId exists but does not require header auth.

### 10.5 State Management

- **No Redux/Zustand** — React `useState` / `useEffect` in `App.jsx` for global phase, user, result, navigation state.
- **Persistent client state:** `localStorage` (user, car, API URL).
- **Server state:** Fetched on demand via Axios; no React Query.

### 10.6 Database Connection

- Singleton SQLite connection in `getDb()` (`database.js`).
- WAL mode, foreign keys ON.
- Auto-creates schema on first access.

### 10.7 Integrations NOT present

- ❌ Email  
- ❌ Payment  
- ❌ Push notifications  
- ❌ AI/ML models  
- ❌ File upload/storage  

---

## 11. Testing and Evaluation

### 11.1 Test Strategy

| Type | Relevance | Notes |
|------|-----------|-------|
| **Functional testing** | High | Core deliverable; all modules need manual verification |
| **Integration testing** | High | Frontend ↔ backend ↔ OSRM/Nominatim chain |
| **Usability testing** | High | Mobile bottom sheet, navigation, readability of savings |
| **Security testing** | Medium | Auth header, admin routes, password hashing |
| **Performance testing** | Medium | Map load time, navigation GPS throttling, route API timeout (120 s) |
| **Unit testing** | Low (gap) | **No automated unit tests found** in project source |
| **Regression testing** | Medium | Re-test after fixes (e.g., navigation crash, duration comparison) |

### 11.2 Test Cases (25)

| TC ID | Module | Scenario | Steps | Expected result | Actual | Status |
|-------|--------|----------|-------|-----------------|--------|--------|
| TC-01 | Auth | Valid registration | Open app → Register → submit valid username/password | Account created; proceeds to car setup | | |
| TC-02 | Auth | Duplicate username | Register existing username | Error: username already exists | | |
| TC-03 | Auth | Valid login | Login with correct credentials | userId stored; car/search phase | | |
| TC-04 | Auth | Invalid login | Wrong password | Error message; stay on auth | | |
| TC-05 | Auth | Guest mode | Click continue as guest | No userId; car setup accessible | | |
| TC-06 | Car | Configure vehicle | Select Toyota Vios → Continue | Brand/model saved; efficiency from vehicles.js | | |
| TC-07 | Car | Skip/default engine | Skip/Close car setup | useDefaultEngine true; 14 km/L sent | | |
| TC-08 | Route | Autocomplete origin | Type "KLCC" → select suggestion | lat/lon populated | | |
| TC-09 | Route | GPS origin | Tap Current location | Origin filled with coordinates/name | | |
| TC-10 | Route | Calculate route | Valid origin + destination → Calculate | Results shown; map polylines | | |
| TC-11 | Route | Invalid location | Non-existent place without coords | API 400 geocoding error | | |
| TC-12 | Results | Lowest cost recommendation | Compare route costs | Cheapest costRM marked "Best route" | | |
| TC-13 | Results | Comparison stats | Two distinct routes | Savings card shows RM/L/CO₂/time diff | | |
| TC-14 | Results | Single route | Location pair with one alternative | singleBestRoute message | | |
| TC-15 | Map | Planning map | After calculate | Origin, destination, routes visible on Leaflet | | |
| TC-16 | Nav | Start navigation | Results → Start navigation | MapLibre map loads; GPS overlay | | |
| TC-17 | Nav | Turn guidance | Move along route | Instruction updates | | |
| TC-18 | Nav | Off-route | Deviate >80 m | Off-route warning shown | | |
| TC-19 | Nav | Arrival | Reach destination (<80 m) | Arrived state | | |
| TC-20 | Trip | Complete (logged in) | Complete trip with savings | Trip in history; leaderboard updated | | |
| TC-21 | Trip | Complete (guest) | Complete as guest | Message to log in; no DB record | | |
| TC-22 | History | View analytics | Open Trip History | Totals and trip list displayed | | |
| TC-23 | Leaderboard | View ranks | Open Leaderboard | Sorted list; rank tier names | | |
| TC-24 | Admin | Update config | Admin changes fuel price → Save | Calculations use new price (same session) | | |
| TC-25 | Mobile | LAN connection | Set Server URL → Test | Health check OK on phone | | |

### 11.3 Evaluation Metrics

| Metric | How to measure |
|--------|----------------|
| Login success rate | Successful logins / attempts |
| Route calculation success rate | Successful POST /api/route / attempts |
| Geocoding accuracy | % autocomplete selections leading to valid routes |
| Recommendation correctness | Manual verify lowest costRM = recommended route |
| Navigation task completion | % users completing route without abandoning |
| Trip save correctness | DB row matches displayed trip metrics |
| Page/map load time | Stopwatch / browser dev tools (target: document in NFR) |
| Usability score | Likert questionnaire after task scenarios |
| GPS accuracy | Compare snap-to-route vs actual path |
| Error handling | Count of unhandled crashes (ErrorBoundary triggers) |

---

## 12. Results Presentation

| ID | Title | Chapter | Content | Why useful |
|----|-------|---------|---------|------------|
| **Figure 1.1** | System context diagram | Ch. 1 | User, app, backend, external APIs | Introduces scope |
| **Figure 1.2** | Application main screen collage | Ch. 1 | Auth, search, results, navigation screenshots | Visual overview |
| **Table 1.1** | Project deliverables checklist | Ch. 1 | List from Section 4 | Maps to objectives |
| **Table 2.1** | Literature review summary | Ch. 2 | Topics vs project features | Shows research grounding |
| **Figure 3.1** | Use case diagram | Ch. 3 | Guest/User/Admin cases | Requirements basis |
| **Table 3.1** | Functional requirements | Ch. 3 | FR-01 to FR-24 | Traceability |
| **Table 3.2** | Non-functional requirements | Ch. 3 | NFR-01 to NFR-14 | Quality attributes |
| **Figure 4.1** | System architecture diagram | Ch. 4 | 3-tier + external services | Core design |
| **Figure 4.2** | ERD | Ch. 4 | users, trips, leaderboard_entries | Database design |
| **Figure 4.3** | UI phase flowchart | Ch. 4 | auth→car→search→results→navigate | UX design |
| **Figure 4.4** | Sequence diagram: route calculation | Ch. 4 | POST /api/route flow | Technical design |
| **Table 4.1** | Technology stack | Ch. 4 | From package.json files | Implementation choices |
| **Figure 5.1** | Route calculation formula diagram | Ch. 5 | fuel, cost, CO₂ equations | Methodology |
| **Table 5.1** | API endpoint summary | Ch. 5 | Section 10.4 | Implementation reference |
| **Figure 5.2** | Navigation component architecture | Ch. 5 | Overlay + MapView + geo utils | Complex module explanation |
| **Table 5.2** | Vehicle catalogue sample | Ch. 5 | Sample from vehicles.js | Domain data |
| **Figure 6.1** | Test case summary chart | Ch. 6 | Pass/fail counts | Testing results |
| **Table 6.1** | Full test case matrix | Ch. 6 | Section 11.2 with actual results filled | Evidence |
| **Table 6.2** | Usability evaluation results | Ch. 6 | User questionnaire scores | Evaluation |
| **Figure 6.2** | Sample route comparison screenshot | Ch. 6 | ResultsDashboard | Validates FR-09/10 |
| **Figure 7.1** | Trip analytics screenshot | Ch. 7 | TripHistoryDashboard | Shows project output |
| **Table 7.1** | Objectives vs achievements | Ch. 7 | Objective fulfilment matrix | Conclusion |
| **Table 7.2** | Limitations and future work | Ch. 7 | From scope exclusions | Critical analysis |

---

## 13. Commercialisation Proposal Ideas

| Item | Suggestion |
|------|------------|
| **Product name** | Route Estimator / EcoRoute MY (UI title: Intelligent Route Cost & Efficiency) |
| **Target market** | Malaysia private car owners, eco-conscious commuters, university/corporate green travel programmes |
| **Customers/users** | B2C drivers; potential B2B fleet managers |
| **Value proposition** | Only app needed to compare routes by **RM cost and CO₂**, not just ETA; gamified savings |
| **Competitors/alternatives** | Google Maps, Waze, in-car GPS — compare on cost/emissions transparency |
| **Revenue models** | Freemium (history/leaderboard premium); fleet SaaS dashboard; sponsored eco-challenges; white-label for NGOs |
| **Deployment** | Phase 1: self-hosted backend + mobile app; Phase 2: cloud VPS with own OSRM instance |
| **Maintenance** | Monitor OSRM/Nominatim usage; update fuel/emission factors; periodic vehicle DB updates |
| **Scalability** | Self-hosted OSRM; JWT auth; Redis sessions; persisted admin config; iOS Capacitor build |

---

## 14. References Needed

| Topic | Why needed | Keywords | Source type |
|-------|------------|----------|-------------|
| Eco-routing algorithms | Justify route comparison approach | eco routing, green navigation | Journal / conference |
| OSRM documentation | Routing implementation | OSRM API alternatives | Official documentation |
| Nominatim usage policy | Ethical API use | Nominatim usage policy | Official website |
| CO₂ emission factors (petrol) | Validate 2.31 kg/L assumption | petrol CO2 emission factor Malaysia | Government / IPCC report |
| Fuel prices Malaysia | Validate RM 2.50 default | RON95 price Malaysia | News / official statistics |
| Capacitor hybrid apps | Mobile deployment chapter | Capacitor architecture | Official documentation |
| bcrypt password hashing | Security section | bcrypt password storage | OWASP / documentation |
| SQLite WAL mode | Database design | SQLite write-ahead logging | Official documentation |
| Gamification sustainability | Leaderboard design | gamification environmental behaviour | HCI journal |
| Usability of mobile navigation | Evaluation chapter | mobile GPS navigation usability | HCI conference |

---

## 15. Appendices

| Appendix | Content | Source |
|----------|---------|--------|
| **A** | Installation guide (backend + frontend + Android) | `.env.example`, `docs/PROJECT_DETAILED.md` §4 |
| **B** | User manual (step-by-step flows) | Phase descriptions in `App.jsx` |
| **C** | API documentation | `server.js` root JSON + Section 10.4 |
| **D** | Database schema SQL | `database.js` initSchema |
| **E** | Calculation formulas & worked example | `calc.js`, `docs/PROJECT_DETAILED.md` §7 |
| **F** | Full test case table | Section 11.2 |
| **G** | Source code snippets (calc, auth, snapToRoute) | Respective files |
| **H** | UI screenshots (all phases) | To be captured by student |
| **I** | Admin configuration guide | `AdminPage.jsx` |
| **J** | Vehicle data catalogue | `frontend/src/data/vehicles.js` |
| **K** | Development evidence | Cursor/chat logs, build outputs — student to supply |
| **L** | Usability questionnaire & raw results | Student to supply |

---

## 16. Missing Information

Please provide the following (not found in codebase):

1. **Official FYP project title** (if different from HTML title "Intelligent Route Cost & Efficiency")
2. **Student name and ID**
3. **Supervisor name(s)**
4. **University / faculty / programme name**
5. **Academic year and semester**
6. **Original problem background** from your FYP proposal document
7. **Project start and end dates**
8. **User evaluation results** (participants, questionnaire, scores)
9. **Final test case actual results** (Pass/Fail column filled)
10. **Deployment URL** (if any public demo exists — currently LAN-only)
11. **Screenshots** for all report figures
12. **References actually cited** in your literature review
13. **Git repository URL / commit history** (no git repo detected in workspace)
14. **Group member roles** (if group project)
15. **Commercialisation proposal marks rubric** from your faculty
16. **Exact research paper topic** (if separate from commercialisation)

---

*End of FYP Report Information Pack*
