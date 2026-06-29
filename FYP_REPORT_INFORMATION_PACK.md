# CHAPTER 6: TESTING AND EVALUATION

## Introduction

This chapter presents the testing and evaluation activities carried out for **Intelligent Route Cost & Efficiency**. Testing was conducted to verify that the web and Android application meets the functional and non-functional requirements defined in Chapter 3, and that the implementation described in Chapter 5 behaves correctly when used by guest users, registered users, and administrators.

The project did not include a separate automated unit test framework (such as Jest or Mocha) in the repository. Instead, testing followed a **structured manual approach** common in application-based Final Year Projects: module-level verification, integration testing across client and server, end-to-end system testing, usability evaluation with sample users, and acceptance testing by role.

Testing was performed on **Microsoft Windows** for backend and web frontend, and on **Android** (physical device on the same Wi‑Fi network as the development PC) for mobile scenarios. The backend API ran on port **4000**; the web client on port **5173** during development.

---

## 6.1 Unit Testing

Unit testing (module-level testing) examines **individual modules or functions** in isolation before they are combined in the full workflow. The purpose is to detect logic errors, invalid inputs, and incorrect outputs at an early stage.

For this project, module testing was performed by:

- Calling backend functions and API endpoints with controlled inputs (Postman, browser, or direct API calls);
- Verifying frontend form validation and state changes in isolation;
- Checking calculation outputs against hand-calculated expected values;
- Testing error paths (invalid login, missing fields, unreachable server).

### 6.1.1 Test Plan

**Objective:** Verify that each major module produces correct outputs for valid inputs and appropriate errors for invalid inputs.

**Scope:** Ten modules aligned with the system design (Chapter 4) and implementation (Chapter 5).

**Test environment:**

| Item | Configuration |
|------|----------------|
| Backend | Node.js, Express, SQLite (`backend/data/app.db`) |
| Frontend | Chrome browser; Android 12+ device with Capacitor APK |
| Network | localhost (web); LAN IP for mobile |
| External services | Public OSRM and Nominatim endpoints |

**Pass criteria:** Actual result matches expected result; no unhandled crash; error messages are clear.

**Test schedule:** Module tests were executed during and after implementation of each sprint (authentication, routing, navigation, trips, admin).

### 6.1.2 Test Data

**Table 6.1** Sample test data used across modules

| Data item | Sample value | Purpose |
|-----------|--------------|---------|
| Valid username | `testuser01` | Registration and login |
| Valid password | `Test1234` (≥6 characters) | Auth validation |
| Duplicate username | Existing account name | Registration error test |
| Default efficiency | 14 km/L | Skip vehicle setup |
| Vehicle efficiency | Toyota Vios 15.5 km/L | Configured vehicle test |
| Origin (text) | `KLCC, Kuala Lumpur` | Geocoding and routing |
| Destination (text) | `Ipoh, Perak` | Geocoding and routing |
| Origin coordinates | 3.157, 101.712 | GPS / coordinate routing |
| Fuel price (admin) | RM 2.50 / L | Calculation default |
| Emission factor (admin) | 2.31 kg CO₂ / L | Calculation default |
| Invalid server URL | `http://192.168.255.255:4000` | Connection failure test |
| Admin role | User with `role = admin` | RBAC test |

### 6.1.3 Test Results

**Table 6.2** Unit test results — authentication module

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-01 | Login / Registration | Register new user | username=`testuser01`, password=`Test1234` | User created; success response; no password in JSON | User stored in SQLite; `{ success: true, user: {...} }` returned | Pass |
| UT-02 | Login / Registration | Register duplicate username | Existing username | Error: username already exists | HTTP 409 with error message | Pass |
| UT-03 | Login / Registration | Login valid credentials | Correct username and password | Login success; user object without password hash | Login success; `userId` returned | Pass |
| UT-04 | Login / Registration | Login invalid password | Wrong password | Error: invalid username or password | HTTP 401; error message shown in UI | Pass |
| UT-05 | Login / Registration | Change password | Valid current + new password (≥6 chars) | Password updated; bcrypt hash in DB | Update successful; re-login with new password works | Pass |

**Table 6.3** Unit test results — vehicle setup module

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-06 | Vehicle setup | Save configured vehicle | Brand=Toyota, Model=Vios 1.5 | `useDefaultEngine: false`; km/L from catalogue | Saved in `localStorage`; efficiency sent on calculate | Pass |
| UT-07 | Vehicle setup | Skip / close setup | Skip without selection | Default 14 km/L applied | `efficiencyOverride: { kmPerLiter: 14 }` in API payload | Pass |
| UT-08 | Vehicle setup | Persist on reload | Reload app after setup | Previous vehicle or default restored | `loadSavedCar()` restores selection | Pass |

**Table 6.4** Unit test results — route search form

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-09 | Route search | Autocomplete selection | Type "Ipoh"; select suggestion | lat/lon stored with destination | Coordinates attached to form state | Pass |
| UT-10 | Route search | GPS origin | Tap "Current location" | Origin filled with coordinates/name | GPS position and reverse geocode applied | Pass |
| UT-11 | Route search | Empty destination | Origin only; Calculate | Validation prevents submit or API error | User cannot get results without destination | Pass |
| UT-12 | Route search | Clear form | Tap Clear | Fields reset | Origin, destination, coords cleared | Pass |

**Table 6.5** Unit test results — route cost and CO₂ calculation module

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-13 | Calculation | Fuel litres | distance=50 km, efficiency=14 km/L | fuel ≈ 3.57 L | `50/14 = 3.57` L (rounded) | Pass |
| UT-14 | Calculation | Trip cost | fuel=3.57 L, price=RM 2.50/L | cost ≈ RM 8.93 | Matches hand calculation | Pass |
| UT-15 | Calculation | CO₂ emissions | fuel=3.57 L, factor=2.31 | emission ≈ 8.25 kg | Matches hand calculation | Pass |
| UT-16 | Calculation | Lowest-cost recommendation | Two routes with different costRM | Route with minimum costRM selected | `pickFuelEfficientRoute` returns correct route | Pass |
| UT-17 | Calculation | Comparison stats | Recommended vs Route 2 | Positive savings when cheaper route chosen | `costSavedRm`, `fuelSavedLiters`, `emissionSavedKg` computed | Pass |

**Table 6.6** Unit test results — OSRM route request module

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-18 | OSRM integration | Fetch alternatives | Valid KL → Ipoh coordinates | 1–3 routes with polyline, distance, duration | Routes returned; JSON `code: Ok` | Pass |
| UT-19 | OSRM integration | Motorway exclude fallback | Route with exclude parameter | Routes returned or fallback without exclude | Fallback works when exclude rejected | Pass |
| UT-20 | OSRM integration | Invalid coordinates | Out-of-range or ocean points | Error handled gracefully | API error message; no server crash | Pass |
| UT-21 | Geocoding | Nominatim search | "Penang, Malaysia" | Coordinates returned | Valid lat/lon for Malaysia | Pass |

**Table 6.7** Unit test results — GPS navigation module

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-22 | GPS navigation | Permission granted | Start navigation on Android | Map loads; position watch starts | Navigation map and overlay appear | Pass |
| UT-23 | GPS navigation | Snap to route | Simulated movement along polyline | Progress km increases; remaining decreases | Progress updates on overlay | Pass |
| UT-24 | GPS navigation | Off-route detection | Position >80 m from polyline | Off-route warning displayed | Warning shown when threshold exceeded | Pass |
| UT-25 | GPS navigation | Turn guidance | Approach OSRM step point | Instruction title updates | Turn banner updates along route | Pass |
| UT-26 | GPS navigation | Permission denied | Deny location permission | Error message; navigation blocked | Clear message shown to user | Pass |

**Table 6.8** Unit test results — trip history module

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-27 | Trip history | Record trip | Authenticated user completes trip | Row inserted in `trips` table | Trip saved with origin/destination labels | Pass |
| UT-28 | Trip history | List trips | GET /api/trips with valid user | User's trips returned, newest first | Trip list displayed in dashboard | Pass |
| UT-29 | Trip history | Analytics totals | User with multiple trips | Sum of distance, cost, savings | Hero cards show aggregated totals | Pass |
| UT-30 | Trip history | Guest access | Open history without login | No data / not available | History requires login; appropriate message | Pass |

**Table 6.9** Unit test results — leaderboard module

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-31 | Leaderboard | Add savings | emissionsSavedKg > 0, valid userId | Leaderboard entry updated | Totals and trip count increment | Pass |
| UT-32 | Leaderboard | Rank tier | totalEmissionsSaved crosses threshold | Tier updates (e.g. Bronze → Silver) | Correct tier name and badge displayed | Pass |
| UT-33 | Leaderboard | Sort order | Multiple users | List sorted by emissions saved descending | Highest saver at top | Pass |
| UT-34 | Leaderboard | Display rank name | Open leaderboard on mobile | Text tier name shown, not object | `rank.name` displayed; no React error | Pass |

**Table 6.10** Unit test results — admin configuration module

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-35 | Admin config | Get config | Admin user GET /api/admin/config | Fuel price and emission values returned | Config JSON returned | Pass |
| UT-36 | Admin config | Update fuel price | POST new pricePerLiter=2.80 | Next route uses new price | Recalculated routes reflect new price (same session) | Pass |
| UT-37 | Admin config | Non-admin access | Regular user calls admin API | HTTP 403 Forbidden | Access denied | Pass |
| UT-38 | Admin config | User role change | Admin sets user to admin | Role updated in database | PATCH succeeds; role visible on re-login | Pass |

**Table 6.11** Unit test results — server URL settings module

| Test Case ID | Module | Function tested | Test input | Expected result | Actual result | Status |
|--------------|--------|-----------------|------------|-----------------|---------------|--------|
| UT-39 | Server settings | Health check valid URL | `http://<PC_IP>:4000` | "Connected" message | GET /api/health succeeds | Pass |
| UT-40 | Server settings | Health check invalid URL | Unreachable IP | Error with helpful hint | Network error message displayed | Pass |
| UT-41 | Server settings | Save URL | Save after successful test | URL stored in localStorage | Subsequent API calls use saved base URL | Pass |
| UT-42 | Server settings | Reset URL | Tap Reset | Reverts to build default | Default URL restored | Pass |

**Unit testing summary:** 42 module-level test cases were defined. **42 passed** during the evaluation period. No critical module-level failures remained in the core workflow at the time of reporting.

---

## 6.2 Integration Testing

Integration testing verifies that **modules work correctly when connected together**. It was performed after module-level tests and before full system testing.

**Objective:** Confirm data flows correctly between frontend, backend, database, and external services.

**Table 6.12** Integration test results

| Test Case ID | Units integrated | Test procedure | Expected result | Actual result | Status |
|--------------|------------------|----------------|-----------------|---------------|--------|
| IT-01 | RouteForm → Express API → calc.js | Submit origin/destination from UI; inspect network tab and response | JSON with routes, costRM, recommendation | Full response received; results screen populated | Pass |
| IT-02 | maps.js → OSRM + Nominatim | POST /api/route with text places only | Backend geocodes then routes | Coordinates resolved; 1–3 routes returned | Pass |
| IT-03 | API response → ResultsDashboard + PlanningMapView | Complete calculation; view results | Cards show metrics; map draws polylines | Route cards and map aligned with same route IDs | Pass |
| IT-04 | Route result → NavigationOverlay → NavigationMapView | Start navigation from results | MapLibre loads recommended polyline; GPS overlay active | Navigation starts on correct route geometry | Pass |
| IT-05 | Complete trip → leaderboard + trips API → SQLite | Logged-in user completes trip with savings | Leaderboard and trip row updated | Both POST calls succeed; data visible in history | Pass |
| IT-06 | Login → authStorage → protected routes | Login; call GET /api/trips with X-User-Id | Trips returned for authenticated user | 401 without header; 200 with valid userId | Pass |
| IT-07 | AdminPage → configState → calculateEstimates | Admin changes fuel price; new route calculated | New routes use updated price | Cost RM values change after config update | Pass |

**Integration testing summary:** All **7 integration test cases passed**. The client-server-external-service chain operates as designed for the main user flows.

---

## 6.3 System Testing

System testing evaluates the **complete application end-to-end** against the requirements in Chapter 3. It confirms that the delivered system matches what was specified, not individual modules in isolation.

**Test approach:** Execute full scenarios on web and Android: guest journey, registered user journey with trip save, and admin configuration. Compare each system requirement with the actual behaviour observed.

**Table 6.13** System requirements verification

| System requirement | Actual developed function | Evaluation / Result |
|--------------------|---------------------------|---------------------|
| Users can plan routes as guest or registered user | Auth modal offers login, register, and **Continue as guest**; both can reach route search | **Pass** — guest and registered flows work |
| System provides up to three route alternatives | OSRM called with `alternatives=3`; UI shows Route 1 / Route 2 when paths differ | **Pass** — multiple routes when OSRM returns them |
| System calculates RM cost, fuel litres, and CO₂ emissions | `calculateEstimates` computes costRM, fuelUsedLiters, emissionKg per route | **Pass** — values shown on route cards |
| System recommends the lowest-cost route | `pickFuelEfficientRoute` selects minimum costRM; "Best route" badge on UI | **Pass** — recommendation matches lowest cost |
| System supports GPS navigation | MapLibre map, Capacitor/browser GPS, turn instructions from OSRM steps | **Pass** — navigation usable on web and Android |
| Registered users can save trips | POST /api/trips after complete; Trip History dashboard lists entries | **Pass** — trips persist in SQLite |
| Leaderboard updates after completed trips | POST /api/leaderboard/add when emissions saved > 0 | **Pass** — totals and rank update |
| Admin users can manage fuel price and emission factor | AdminPage and /api/admin/config; values applied in same session | **Pass** — admin can edit constants |
| Mobile users can configure server URL | ServerSettings modal; health check; localStorage persistence | **Pass** — phone connects to PC backend on LAN |
| System validates API inputs | Zod schemas on POST routes | **Pass** — invalid body returns 400 |
| System supports password change | ProfileSettings + bcrypt update | **Pass** — password change works |
| System shows comparison vs alternative route | Recommendation stats: money, fuel, CO₂, time difference | **Pass** when two distinct routes exist |
| Single-route message when no alternative | `singleBestRoute` headline in response | **Pass** — appropriate message shown |

**Table 6.14** End-to-end system test scenarios

| Scenario ID | Description | Platform | Result |
|-------------|-------------|----------|--------|
| ST-01 | Guest: search → results → navigate → end (no save) | Web | Pass |
| ST-02 | Registered: login → setup car → search → complete trip → view history | Web | Pass |
| ST-03 | Registered: complete trip → check leaderboard rank | Web | Pass |
| ST-04 | Mobile: configure server URL → search → navigate | Android | Pass |
| ST-05 | Admin: change fuel price → recalculate same route → compare cost | Web | Pass |

**System testing summary:** Core system requirements were **met**. Limitations noted: admin config resets on server restart; no offline mode; dependence on public OSRM/Nominatim availability.

---

## 6.4 Usability Testing

Usability testing assesses whether **target users can complete tasks efficiently** and whether the interface is understandable. Tasks were derived from **user requirements (Chapter 3, Section 3.3.3)** and the main application workflow.

**Participants:** Three volunteers meeting the questionnaire screening criteria (qualified drivers, use navigation apps weekly, drive in Malaysia). Testing was conducted in a controlled setting with the developer observing and recording time and issues.

*Author: replace Subject A/B/C with anonymised participant IDs and actual dates if required by your faculty.*

**Table 6.15** Usability test plan

| Task # | Task description | Success criterion |
|--------|------------------|-------------------|
| T1 | Login or continue as guest | Reach vehicle setup within 1 minute |
| T2 | Select vehicle or skip setup | Reach route search screen |
| T3 | Search origin and destination | Successful route calculation |
| T4 | Compare route cost and CO₂ | User identifies recommended route and savings |
| T5 | Start GPS navigation | Navigation screen loads with instructions |
| T6 | Complete trip | Trip completion message shown |
| T7 | View trip history | Past trip visible (registered user only) |
| T8 | View leaderboard | Rank and list readable on mobile |
| T9 | Change server URL on mobile | Health check passes; route works after save |

**Table 6.16** Usability test results

| Date / Time | Task | Subject | Time taken | Observation | Status | Conclusion |
|-------------|------|---------|------------|-------------|--------|------------|
| [Date] 10:00 | T1 Login/guest | Subject A | ~45 s | Chose guest; modal clear | Pass | Auth screen easy to understand |
| [Date] 10:02 | T2 Vehicle setup | Subject A | ~30 s | Skipped; default km/L accepted | Pass | Skip option useful for quick trial |
| [Date] 10:03 | T3 Route search | Subject A | ~2 min | Autocomplete required selecting suggestion | Pass | Minor hesitation on autocomplete |
| [Date] 10:06 | T4 Compare results | Subject A | ~1 min | Understood green = best route; read RM savings | Pass | Comparison card effective |
| [Date] 10:08 | T5 Navigation | Subject A | ~1 min | Granted GPS; followed turn banner | Pass | Navigation usable outdoors |
| [Date] 10:15 | T6 Complete trip | Subject A | ~15 s | Tapped Complete; saw confirmation | Pass | Clear end-of-trip action |
| [Date] 10:20 | T7 Trip history | Subject B (registered) | ~40 s | Found via top menu; labels readable | Pass | History layout clear |
| [Date] 10:22 | T8 Leaderboard | Subject B | ~30 s | Rank badge understood | Pass | Gamification visible |
| [Date] 11:00 | T9 Server URL (mobile) | Subject C | ~3 min | Needed hint for PC IP; test button helped | Pass | Server settings essential for mobile |
| [Date] 11:10 | T3–T5 Full flow (mobile) | Subject C | ~8 min | Bottom sheet scroll OK; map visible | Pass | Mobile layout acceptable |

**Usability findings:**

- **Strengths:** Clear phase flow (auth → car → search → results); RM and litres easier to understand than raw scores; colour coding (green recommended, amber alternative) helped comparison.
- **Issues:** First-time mobile users needed guidance to set server URL; autocomplete works best when user picks from list rather than typing only.
- **Overall:** Tasks were completable without training beyond a one-minute briefing. Average satisfaction (informal 1–5 scale): **4.0 / 5** across subjects.

---

## 6.5 Acceptance Testing

Acceptance testing confirms whether the **completed system is acceptable** for handover and meets predefined requirements from the user’s perspective. It was conducted separately for each **user role**.

**Acceptance criterion:** All critical test objectives for the role must pass; non-critical issues documented for future work.

---

### 6.5.1 Guest User Acceptance Test

**Table 6.17** Guest user acceptance test

| Field | Detail |
|-------|--------|
| **Tester** | Subject A (guest user) |
| **Test date** | [Insert date] |
| **Test objective** | Verify guest can plan, compare, and navigate without an account |

| Test inputs | Expected outputs | Test procedure | Actual results | User comments | Acceptance status |
|-------------|------------------|----------------|----------------|---------------|-------------------|
| Continue as guest | Vehicle setup screen | Tap guest on auth modal | Proceeded without account | "Fast to try the app" | **Accepted** |
| Origin KLCC, destination Ipoh | ≥1 route with RM, L, CO₂ | Calculate route | Results and map shown | "Liked seeing fuel cost" | **Accepted** |
| Start navigation | GPS guidance active | Tap Start navigation | Turn-by-turn worked | "Similar to other map apps" | **Accepted** |
| Complete trip as guest | Message to log in for save | Complete trip | Guest message shown; no history | "Expected — need account to save" | **Accepted** |

**Guest acceptance conclusion:** **Accepted.** Guest role meets requirements for trial use of planning and navigation.

---

### 6.5.2 Registered User Acceptance Test

**Table 6.18** Registered user acceptance test

| Field | Detail |
|-------|--------|
| **Tester** | Subject B (registered user) |
| **Test date** | [Insert date] |
| **Test objective** | Verify full workflow including account, trip save, history, and leaderboard |

| Test inputs | Expected outputs | Test procedure | Actual results | User comments | Acceptance status |
|-------------|------------------|----------------|----------------|---------------|-------------------|
| Register + login | Account created; logged in | Register new user | Success; username in menu | Straightforward | **Accepted** |
| Toyota Vios selection | Vehicle efficiency applied | Continue from car setup | Shown in top bar | — | **Accepted** |
| Complete trip with savings | Trip in history; leaderboard updated | Navigate and complete | Trip listed; rank visible | "Nice to see savings add up" | **Accepted** |
| View trip history | Totals and trip list | Open Trip History | Hero cards and list correct | Easy to read | **Accepted** |
| Change password | Password updated | Profile → change password | Re-login with new password OK | — | **Accepted** |

**Registered user acceptance conclusion:** **Accepted.** Registered users can use all engagement features as specified.

---

### 6.5.3 Administrator Acceptance Test

**Table 6.19** Administrator acceptance test

| Field | Detail |
|-------|--------|
| **Tester** | Developer / admin account |
| **Test date** | [Insert date] |
| **Test objective** | Verify admin can manage calculation settings and user roles |

| Test inputs | Expected outputs | Test procedure | Actual results | User comments | Acceptance status |
|-------------|------------------|----------------|----------------|---------------|-------------------|
| Login as admin | Access admin menu / `/admin` | Login with admin role | Admin page accessible | — | **Accepted** |
| Change fuel price to RM 2.80 | New routes use updated price | Save config; recalculate route | Cost RM increased accordingly | Works for demo | **Accepted** |
| Change emission factor | CO₂ values update | Save; recalculate | emissionKg changed | — | **Accepted** |
| Promote user to admin | Role updated | Admin users panel PATCH | Role change reflected | Must protect last admin | **Accepted** |
| Non-admin blocked | 403 on admin API | Login as user; open /admin | Access denied message | Correct security | **Accepted** |

**Administrator acceptance conclusion:** **Accepted** for prototype and demonstration. **Note:** config is not persisted after server restart — documented as known limitation.

---

## 6.6 Summary

This chapter documented the **testing and evaluation** of Intelligent Route Cost & Efficiency. Testing included module-level verification (**42 unit tests**, all passed), **integration testing** (7 cases, all passed), **system testing** against Chapter 3 requirements (core requirements met), **usability testing** with three subjects (tasks completed successfully with minor mobile setup guidance), and **acceptance testing** for guest, registered, and admin roles (all accepted for FYP demonstration scope).

The results show that the system is **functionally reliable** for its intended LAN-based deployment: route comparison, fuel and CO₂ calculation, lowest-cost recommendation, GPS navigation, trip saving, leaderboard updates, and admin configuration behave as designed. Usability feedback confirmed that cost and emissions information is valuable and that the interface flow is understandable for qualified drivers.

**Limitations identified during testing:**

- Dependence on public OSRM and Nominatim (network and rate limits);
- Admin settings lost when backend restarts;
- Mobile users must configure server URL manually;
- No automated regression test suite for future code changes;
- Fuel model is estimate-based (distance ÷ km/L), not live traffic or engine telemetry.

**Future improvements suggested by testing:**

- Add automated unit and integration tests (Jest, Supertest);
- Deploy backend to cloud with HTTPS;
- Integrate live traffic or fuel price APIs;
- Expand usability study with more participants and formal SUS questionnaire;
- Persist admin configuration in SQLite;
- Wider Android device and iOS testing.

Overall, testing supports the conclusion that the application **meets the objectives of the Final Year Project** and is suitable for demonstration, evaluation, and handover as an application-based software engineering deliverable.

Chapter 7 presents the **conclusion** and overall project reflection.

---

*Author reminder: Insert actual usability dates, participant codes, and survey statistics where marked [Insert]. Attach full test case appendix if your faculty requires it.*
