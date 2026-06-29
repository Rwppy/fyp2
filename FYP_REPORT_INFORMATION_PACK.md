# CHAPTER 7: CONCLUSION

## 7.1 Introduction

This chapter concludes the Final Year Project on **Intelligent Route Cost & Efficiency** (Android app name: **Route Estimator**). It summarises what was achieved in relation to the objectives set in Chapter 1, reflects on the expected findings, and discusses problems encountered during development and testing. It also outlines recommended work for a **next phase** beyond the current FYP scope.

The project successfully delivered an application-based system that helps drivers in Malaysia compare driving routes using **fuel cost (RM)**, **fuel volume (litres)**, **CO₂ emissions (kg)**, **distance**, and **duration**, rather than relying on travel time alone.

---

## 7.2 Achievement of Project Objectives

The seven project objectives from Chapter 1 are reviewed below.

**Table 7.1** Achievement of project objectives

| Objective | Target | Achievement | Status |
|-----------|--------|-------------|--------|
| **1. Route planning module** | Up to three driving routes in Malaysia | OSRM integration returns multiple alternatives; Nominatim geocoding with Malaysia bias; route search form with autocomplete and GPS origin | **Achieved** |
| **2. Fuel and emissions calculation** | Fuel (L), cost (RM), CO₂ (kg) per route | `calculateEstimates` module implemented; defaults RM 2.50/L, 2.31 kg CO₂/L; vehicle km/L from catalogue or 14 km/L default | **Achieved** |
| **3. Lowest-cost recommendation** | Recommend cheapest route; show comparison | `pickFuelEfficientRoute` selects minimum costRM; comparison stats for money, fuel, CO₂, and time vs alternative route | **Achieved** |
| **4. GPS navigation** | Turn-by-turn guidance, progress, off-route, arrival | MapLibre navigation map; Capacitor/browser GPS; 80 m off-route and arrival thresholds; OSRM step instructions | **Achieved** |
| **5. User engagement** | Accounts, trip history, analytics, leaderboard | Register/login/guest; SQLite trips; analytics dashboard; seven rank tiers (Bronze–Legend) | **Achieved** |
| **6. Cross-platform delivery** | Web and Android; responsive layout | React + Vite web app; Capacitor Android APK; bottom sheet (mobile) and sidebar (desktop ≥768 px) | **Achieved** |
| **7. Admin module** | Edit calculation settings and user roles | AdminPage, `/api/admin/config`, user role management; bcrypt password change for users | **Achieved** |

All **seven objectives were met** within the defined project scope. Chapter 6 testing recorded **42 module tests**, **7 integration tests**, and **system, usability, and acceptance tests** as passed or accepted for demonstration purposes.

---

## 7.3 Achievement of Deliverables and Expected Findings

### 7.3.1 Deliverables

**Table 7.2** Deliverables completed

| No. | Deliverable | Outcome |
|-----|-------------|---------|
| 1 | Web application (React) | Delivered — `frontend/` with Vite build |
| 2 | Android mobile application | Delivered — Capacitor 7 hybrid app |
| 3 | Backend REST API | Delivered — Express on port 4000 |
| 4 | SQLite database | Delivered — users, trips, leaderboard tables |
| 5 | Authentication module | Delivered — register, login, guest, password change |
| 6 | Route comparison and navigation | Delivered — Leaflet planning + MapLibre navigation |
| 7 | Trip history and analytics | Delivered — dashboard with totals and trip list |
| 8 | Leaderboard and ranks | Delivered — cumulative CO₂ savings and tiers |
| 9 | Admin module | Delivered — config and user roles |
| 10 | Technical documentation | Delivered — `docs/` folder and FYP report chapters |

### 7.3.2 Expected Findings

The expected findings from Chapter 1 were confirmed during implementation and testing:

1. **Different routes produce different costs and emissions** — OSRM alternatives often differ in distance, duration, costRM, and emissionKg under the same calculation rules.
2. **Lowest-cost recommendation is clear and objective** — Users can identify the recommended route by the “Best route” label and minimum RM cost.
3. **Side-by-side comparison supports decision-making** — Users can see trade-offs between saving money or CO₂ and longer or shorter travel time.
4. **GPS navigation is feasible on web and Android** — Turn-by-turn guidance works using the recommended route polyline and OSRM steps.
5. **Registered users can track savings over time** — Trip history and leaderboard record money, fuel, and emissions saved compared with alternative routes.

Usability testing (Chapter 6) indicated that qualified drivers understood the cost and emissions information and could complete main tasks without extensive training.

---

## 7.4 Problems Encountered During the Project

Several technical and practical problems arose during the project. The main issues and how they were addressed are summarised below.

**Table 7.3** Problems encountered and responses

| Problem | Impact | Solution / outcome |
|---------|--------|-------------------|
| **Mobile could not reach `localhost` backend** | Android app failed to load routes on phone | Server Settings UI; `VITE_API_BASE_URL` and LAN IP; `CORS_ALLOW_LAN=1` |
| **CORS blocked cross-origin API calls** | Network errors from Capacitor WebView | Extended CORS in `server.js` for LAN private IP ranges |
| **Mainstream apps do not show fuel/CO₂ comparison** | Confirmed problem gap; shaped requirements | Addressed by core calculation and results dashboard |
| **OSRM `exclude=motorway` sometimes rejected** | Route request failure | Automatic fallback request without exclude parameter |
| **Navigation crash on Start navigation** | App error boundary triggered | Fixed polyline coordinate format (`{lat,lon}` vs `[lat,lon]`) in map layers |
| **Leaderboard crash on mobile** | React error rendering rank object | Display `rank.name` instead of full rank object |
| **Autocomplete hidden under top bar** | Poor mobile UX for destination search | Portal dropdown to `document.body`; z-index adjustment |
| **GPS updates caused UI lag** | Sluggish navigation experience | Throttled position updates to parent component (~200 ms) |
| **Duration comparison showed wrong values** | Incorrect “faster by 0 min” message | Corrected time-difference logic in backend `calc.js` |
| **Skip vehicle setup did not apply 14 km/L** | Wrong efficiency sent to API | Wired `useDefaultEngine` in `RouteForm.jsx` |
| **Admin config lost on server restart** | Settings revert to defaults | Documented as limitation; future work to persist in database |
| **Public OSRM/Nominatim rate limits** | Occasional slow or failed requests | Retry and error messages; documented dependency |
| **No automated test suite** | Regression risk on future changes | Manual structured testing in Chapter 6; automated tests listed for next phase |

These problems were typical of a **full-stack mobile-connected application** and were resolved or mitigated without changing the core project objectives.

---

## 7.5 Limitations of the Completed System

The final system is suitable for **FYP demonstration and evaluation** on a local network, but it has known limitations:

- **Petrol-only** macroscopic fuel model (distance ÷ km/L);
- **No live traffic or live fuel price** integration;
- **Simplified authentication** (`X-User-Id` header, not JWT/OAuth);
- **Malaysia-focused** geocoding and vehicle catalogue;
- **LAN deployment** rather than cloud production hosting;
- **Manual testing** rather than continuous automated testing;
- **Dependence on third-party** OSRM and Nominatim services.

These limitations do not invalidate the project goals but define the boundary between a **prototype** and a **commercial product**.

---

## 7.6 Next Phase and Future Work

The current FYP phase delivered a working prototype. A **next phase** could focus on production readiness, wider evaluation, and enhanced features.

**Table 7.4** Recommended work for the next phase

| Priority | Area | Proposed work |
|----------|------|---------------|
| High | **Deployment** | Host backend on cloud VPS; enable HTTPS; fixed public API URL for mobile |
| High | **Security** | Implement JWT or session tokens; secure password reset flow |
| High | **Configuration** | Persist admin fuel price and emission factor in SQLite |
| Medium | **Routing** | Self-hosted OSRM instance; optional live traffic data |
| Medium | **Testing** | Jest/Supertest automated unit and integration tests; CI pipeline |
| Medium | **Usability** | Larger user study; formal SUS questionnaire; more Android devices |
| Medium | **Vehicles** | Support diesel, hybrid, and electric models with separate emission factors |
| Low | **Platform** | iOS Capacitor build; progressive web app offline cache |
| Low | **Features** | Push notifications; fuel price API; export trip reports (PDF/CSV) |
| Low | **Commercialisation** | Fleet dashboard; partnership with eco-driving programmes |

The next phase should begin with **cloud deployment and security hardening**, because these are prerequisites for real users outside a development LAN.

---

## 7.7 Final Summary

Intelligent Route Cost & Efficiency was developed as an **application-based Final Year Project** to address a practical gap in everyday navigation tools: the lack of integrated **fuel cost and CO₂ comparison** when choosing between driving routes.

The project achieved **all seven objectives** and delivered the planned **web application, Android app, backend API, SQLite database, and supporting modules** for authentication, route planning, navigation, trip history, leaderboard, and administration. Testing in Chapter 6 showed that core functions work reliably for guest users, registered users, and administrators in the intended deployment environment.

Development was not without difficulty. Issues such as **mobile LAN connectivity, CORS, external routing APIs, GPS performance, and UI defects on small screens** required iterative debugging and refinement. These experiences reinforced the importance of **integration testing early**, especially for mobile clients connected to a local backend.

In conclusion, the project demonstrates that a **transparent, lowest-cost routing model**—combined with GPS navigation and savings tracking—can be implemented using **open-source technologies** (React, Express, SQLite, OSRM, OpenStreetMap) and can help drivers make more informed decisions about fuel use and emissions. The system is **complete for FYP submission and demonstration**, with a clear roadmap for future enhancement in deployment, security, testing, and real-world evaluation.

---

*End of Chapter 7*
