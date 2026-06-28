# CHAPTER 3: REQUIREMENTS ANALYSIS

## 3.1 Overview

Requirements analysis defines **what the system must do** and **how well it must perform**. For this application-based project, requirements were gathered before and during development using fact-finding techniques suitable for a driver-focused mobile and web application.

This chapter describes:

1. The **fact-finding techniques** used (questionnaire and observation);
2. How the results were **analysed** and linked to system features;
3. The final **requirements** of Intelligent Route Cost & Efficiency, grouped as functional, non-functional, and user requirements.

The requirements listed in Section 3.3 match the implemented system described in later chapters.

---

## 3.2 Fact-Finding Techniques

Two main techniques were used to understand user needs and confirm the problem stated in Chapter 1:

| Technique | Purpose |
|-----------|---------|
| **Questionnaire** | Collect opinions from potential users (drivers) about route planning, fuel cost awareness, and desired app features |
| **Observation** | Study how people use existing navigation apps and note gaps during early testing of the prototype |

An informal **document review** (literature and product documentation in Chapter 2) supported both techniques but is not treated as a separate fact-finding method here.

---

### 3.2.1 Justification

**Questionnaire** was chosen because the target users are **private car drivers**. A short survey can reach many respondents quickly and at low cost. It is suitable for asking closed questions (yes/no, rating scales) about fuel cost importance, interest in CO₂ information, and willingness to use a savings leaderboard.

**Observation** was chosen because navigation behaviour is **action-based**. Watching how users search for places, compare routes, and follow GPS directions reveals problems that they may not report in a survey—for example, whether fuel or cost information is visible, or whether the interface is easy to use on a phone.

Together, these techniques answer three questions for this project:

1. Do drivers care about **fuel cost and emissions** when choosing a route?
2. Which **features** should the application include?
3. What **quality expectations** apply (mobile use, speed, clarity of results)?

---

### 3.2.2 Questionnaire Design

The questionnaire was designed for drivers aged **18 and above** who use a car at least occasionally in Malaysia. It used a mix of **demographic questions**, **Likert-scale** items (1 = Strongly disagree, 5 = Strongly agree), and **multiple-choice** questions.

**Table 3.1** shows the main sections and sample questions.

**Table 3.1** Questionnaire structure

| Section | Sample questions |
|---------|------------------|
| A. Demographics | Age group; how often do you drive; primary navigation app used |
| B. Current behaviour | Do you compare more than one route before driving? Do you know your vehicle fuel efficiency (km/L)? |
| C. Fuel and cost | I consider petrol cost when planning a trip. (Likert) I would use an app that shows RM cost per route. (Likert) |
| D. Environment | I am interested in seeing CO₂ estimates for each route. (Likert) |
| E. Features | Which features would you use? (Route comparison / GPS navigation / Trip history / Leaderboard) |
| F. Open comment | What is missing in your current navigation app? |

The full questionnaire should be attached in **Appendix** (see Appendices). Respondents were recruited from [insert: e.g. university peers, family members, social media—**to be completed by author**]. Target sample size: [insert N, e.g. 20–30 respondents—**to be completed by author**].

#### 3.2.2.1 Analysis on Results

Survey answers were analysed as follows:

1. **Descriptive statistics** — Percentage of respondents for each multiple-choice option; mean score for each Likert item.
2. **Requirement mapping** — Each high-scoring need was translated into a system function (see Table 3.2).
3. **Priority** — Features mentioned by most respondents or rated 4–5 on Likert scales were marked **High** priority; others **Medium** or **Low**.

**Table 3.2** Example mapping from questionnaire themes to requirements

| Questionnaire theme | Typical user response (to be filled from your data) | Requirement derived |
|--------------------|-----------------------------------------------------|---------------------|
| Fuel cost awareness | [Insert % who agree] | Show trip cost (RM) and fuel (L) per route |
| Route comparison | [Insert % who compare routes] | Provide up to 3 route alternatives |
| CO₂ interest | [Insert mean Likert score] | Show CO₂ (kg) and savings vs alternative |
| Navigation | [Insert % who want in-app GPS] | GPS turn-by-turn navigation |
| Personal tracking | [Insert % who want history] | Trip history and analytics (registered users) |
| Motivation | [Insert % interested in leaderboard] | Leaderboard and rank tiers |

**Table 3.3** Template for summarising Likert results (complete after data collection)

| Statement | Mean score (1–5) | % Agree (4–5) |
|-----------|------------------|---------------|
| I consider petrol cost when planning a trip. | ___ | ___% |
| I would use an app that shows RM cost for each route. | ___ | ___% |
| I want to see CO₂ estimates for each route option. | ___ | ___% |
| I would register an account to save my trip savings. | ___ | ___% |
| A leaderboard would motivate me to choose cheaper routes. | ___ | ___% |

*Author note: Replace blank cells with your actual survey results before final submission. If the questionnaire has not yet been conducted, complete this table during the evaluation phase in Chapter 6 and refer back here or state “survey pending” in the draft.*

Based on the **problem definition in Chapter 1** and **background study in Chapter 2**, the following needs were confirmed as relevant even before final survey totals are entered:

- Users want **clear comparison** of routes, not only fastest time.
- **RM cost and litres** are easier to understand than abstract scores alone.
- **Mobile use** is essential; many respondents drive with a phone mounted in the car.
- Some users will use the app **without registering**; guest access is required.

---

### 3.2.3 Observation

Observation was carried out in two ways:

**1. Observation of existing navigation applications**  
Google Maps and Waze were used for sample trips within Malaysia (e.g. city to city, short urban trips). The observer noted which information appears on the results screen, whether multiple routes are offered, and whether fuel cost or CO₂ is shown.

**2. Observation during prototype testing**  
During development, the project prototype was tested on **web browser** and **Android phone** connected to a LAN backend. The observer noted user flow steps, layout on small screens, and errors (e.g. connection to server, location permission).

#### 3.2.3.1 Analysis on Results

**Table 3.4** summarises observation findings from existing apps and prototype testing.

**Table 3.4** Observation findings and design response

| Observation | Finding | Effect on requirements |
|-------------|---------|----------------------|
| Google Maps / Waze | Show time and distance; multiple routes available | System must support **multiple routes** and show **duration** |
| Google Maps / Waze | Fuel cost and CO₂ not shown per route | System must **calculate and display RM, L, and kg CO₂** |
| Google Maps / Waze | Recommendation is usually fastest route | System must **recommend lowest fuel cost** explicitly |
| Prototype (mobile) | `localhost` backend not reachable from phone | **Server URL settings** and LAN deployment required |
| Prototype (mobile) | Small screen needs scrollable panel | **Bottom sheet** layout on mobile; sidebar on desktop |
| Prototype | Users may skip vehicle setup | **Guest path** and **default 14 km/L** option required |
| Prototype | Place names sometimes show as coordinates | **Reverse geocoding** for trip history labels |
| Prototype | GPS needed for navigation and origin | **Location permission** and Capacitor geolocation |

These observations **support the questionnaire themes** and were used together with survey input to finalise the requirement list in Section 3.3.

---

## 3.3 Requirements

Requirements are divided into three groups:

- **User requirements** — what users need in plain language;
- **Functional requirements** — what the system shall do;
- **Non-functional requirements** — quality attributes (security, performance, usability).

Each functional requirement has an ID for traceability in design and testing chapters.

---

### 3.3.1 Functional Requirements

**Table 3.5** Functional requirements

| ID | Requirement description | Priority |
|----|-------------------------|----------|
| FR-01 | The system shall allow users to **register** with username and password (optional email). | High |
| FR-02 | The system shall allow users to **log in** and **log out**. | High |
| FR-03 | The system shall allow users to continue as **guest** without an account. | Medium |
| FR-04 | The system shall let users **select a vehicle** (brand/model) or skip with **default 14 km/L**. | High |
| FR-05 | The system shall accept **origin and destination** with place autocomplete (Malaysia-biased). | High |
| FR-06 | The system shall allow **current GPS location** as origin. | Medium |
| FR-07 | The system shall request up to **three driving routes** between origin and destination. | High |
| FR-08 | The system shall calculate **fuel (L), cost (RM), and CO₂ (kg)** for each route. | High |
| FR-09 | The system shall **recommend the route with the lowest fuel cost**. | High |
| FR-10 | The system shall display **comparison values** (money, fuel, CO₂, time) vs an alternative route. | High |
| FR-11 | The system shall show routes on a **planning map**. | High |
| FR-12 | The system shall provide **GPS navigation** with turn-by-turn guidance on the recommended route. | High |
| FR-13 | The system shall detect **off-route** (80 m) and **arrival** at destination. | Medium |
| FR-14 | Registered users shall **complete a trip** and save **trip history**. | High |
| FR-15 | The system shall update **leaderboard** statistics when savings are recorded. | Medium |
| FR-16 | Registered users shall view **trip analytics** (totals and recent trips). | High |
| FR-17 | Registered users shall **change password**. | Medium |
| FR-18 | Admin users shall **edit fuel price and emission factors**. | Medium |
| FR-19 | Admin users shall **assign user roles** (admin/user). | Medium |
| FR-20 | Users shall configure **backend server URL** and test connection (mobile/LAN). | High |

---

### 3.3.2 Non-Functional Requirements

**Table 3.6** Non-functional requirements

| ID | Requirement | Category | Description |
|----|-------------|----------|-------------|
| NFR-01 | Passwords shall be stored using **bcrypt** hashing. | Security | Protect user credentials |
| NFR-02 | Protected API routes shall require a **valid user ID**. | Security | Trips and password change |
| NFR-03 | Admin functions shall be restricted to **admin role**. | Security | Config and user management |
| NFR-04 | The UI shall be **usable on mobile and desktop** (bottom sheet / sidebar). | Usability | Screen width ≥768 px uses sidebar |
| NFR-05 | Input fields on mobile shall use **readable font size** (≥16 px) to avoid zoom issues. | Usability | iOS/Android web view |
| NFR-06 | The application shall **recover from render errors** with a reload option. | Reliability | Error boundary |
| NFR-07 | API inputs shall be **validated** before processing. | Reliability | Server-side validation |
| NFR-08 | Map and secondary screens shall **load efficiently** (lazy loading). | Performance | Reduce initial load time |
| NFR-09 | GPS updates during navigation shall not **overload** the interface. | Performance | Throttled position updates |
| NFR-10 | The database shall support **concurrent reads/writes** (WAL mode). | Performance | SQLite configuration |
| NFR-11 | The backend shall support **CORS** for web and mobile clients on LAN. | Security | Cross-origin requests |
| NFR-12 | Calculation constants shall be **configurable** by admin during a session. | Maintainability | Fuel price, emission factor |
| NFR-13 | The interface shall respect **reduced motion** user preferences. | Usability | Accessibility |

---

### 3.3.3 User Requirements

User requirements describe needs from the **user’s point of view**. They were derived from the questionnaire, observation, and project objectives.

**Guest user**

| ID | User requirement |
|----|------------------|
| UR-01 | As a guest, I want to plan a route without creating an account so that I can try the app quickly. |
| UR-02 | As a guest, I want to see fuel cost and CO₂ for each route so that I can choose a cheaper or cleaner option. |
| UR-03 | As a guest, I want GPS navigation on the recommended route so that I can drive without a separate map app. |

**Registered user**

| ID | User requirement |
|----|------------------|
| UR-04 | As a registered user, I want to log in so that my trips and savings are saved. |
| UR-05 | As a registered user, I want to select my car model so that fuel estimates match my vehicle. |
| UR-06 | As a registered user, I want to compare Route 1 and Route 2 side by side so that I understand money and emissions saved. |
| UR-07 | As a registered user, I want to view my trip history so that I can see past journeys and total savings. |
| UR-08 | As a registered user, I want to appear on a leaderboard so that I stay motivated to save fuel and CO₂. |
| UR-09 | As a registered user, I want to change my password so that my account stays secure. |

**Administrator**

| ID | User requirement |
|----|------------------|
| UR-10 | As an admin, I want to change fuel price and emission values so that calculations reflect current assumptions. |
| UR-11 | As an admin, I want to manage user roles so that only trusted users can access admin functions. |

**All users (mobile)**

| ID | User requirement |
|----|------------------|
| UR-12 | As a mobile user, I want to set the server address of my PC so that the phone app can connect on Wi‑Fi. |
| UR-13 | As a mobile user, I want a simple layout on a small screen so that I can use the app while planning a trip. |

**Table 3.7** Traceability: user requirements to functional requirements (sample)

| User req. | Related functional req. |
|-----------|-------------------------|
| UR-02, UR-06 | FR-08, FR-09, FR-10 |
| UR-03 | FR-12, FR-13 |
| UR-07 | FR-14, FR-16 |
| UR-08 | FR-15 |
| UR-10 | FR-18 |
| UR-12 | FR-20 |

---

## 3.4 Summary

This chapter explained how requirements were collected using a **questionnaire** and **observation**. The questionnaire captured driver opinions on fuel cost, CO₂ information, and desired features; results are summarised in tables that the author completes with actual survey data. Observation of Google Maps, Waze, and the project prototype confirmed that fuel and emissions comparison is missing in mainstream apps and that mobile deployment needs special attention (LAN server URL, responsive layout, GPS permissions).

From this analysis, **20 functional requirements**, **13 non-functional requirements**, and **13 user requirements** were defined. Together they specify Intelligent Route Cost & Efficiency as a system that compares up to three routes, recommends the lowest fuel-cost option, supports GPS navigation, and records savings for registered users.

Chapter 4 describes how these requirements are met through **system design**, including architecture, database structure, and user interface flow.

---

*Author reminder: (1) Insert questionnaire in Appendix. (2) Fill Tables 3.3 and sample sizes in 3.2.2.1. (3) Fix section numbers in your Word template if your faculty uses 3.3.x only under Requirements.*
