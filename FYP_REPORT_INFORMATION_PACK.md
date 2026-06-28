# CHAPTER 1: INTRODUCTION

## 1.1 Overview

Most navigation apps show the fastest or shortest route. They do not usually show **how much petrol a trip will cost**, **how many litres will be used**, or **how much CO₂ will be produced** for each possible route.

This project builds **Intelligent Route Cost & Efficiency** (Android app name: **Route Estimator**). It is a web and mobile application that:

- Finds up to **3 driving routes** between two places
- Calculates **fuel cost (RM)**, **fuel used (L)**, and **CO₂ (kg)** for each route
- Recommends the route with the **lowest fuel cost**
- Lets the user **navigate with GPS**
- Saves **trip history** and **leaderboard scores** for logged-in users

The system has three parts:

| Part | Technology |
|------|------------|
| Frontend | React (web + Android via Capacitor) |
| Backend | Node.js + Express API |
| Database | SQLite |

Default values used in calculations: **RM 2.50/L**, **2.31 kg CO₂/L**, and **14 km/L** if the user skips vehicle setup. Users can also pick a car from **Toyota, Honda, Proton, or Perodua** (`frontend/src/data/vehicles.js`).

This is an **application-based** FYP. The main output is working software, not a research experiment.

---

## 1.2 Problem Statement

### How the problem started

When planning a drive, users often choose a route based on **time** or **distance** only. They may not know which route is **cheaper in petrol** or **better for the environment**. Manual calculation (distance ÷ km/L × fuel price) is slow and easy to get wrong.

### The problem in simple terms

1. Normal map apps do not compare routes by **fuel cost** and **CO₂**.
2. Drivers cannot easily see **Route A vs Route B** savings in RM, litres, and kg CO₂.
3. There is no simple way to **track savings over many trips**.
4. A custom app on a PC cannot be tested on a phone without **network setup** (LAN + server URL).

### Who is affected

- **Drivers in Malaysia** who want to save petrol money
- **Users** who want to track CO₂ savings
- **Admins** who need to change fuel price or emission settings for demos

### Why build a software system?

The app needs to connect many steps in one place: search places, get routes from OSRM, calculate costs, show maps, use GPS, and save user data. This cannot be done well by hand. The project joins these steps in one workflow (`RouteForm` → API → results → navigation → save trip).

---

## 1.3 Project Objectives

1. Build a route planner that gets **up to 3 routes** using OSRM and Nominatim.
2. Calculate **fuel (L), cost (RM), and CO₂ (kg)** for each route.
3. **Recommend the cheapest route** and show comparison stats vs another route.
4. Add **GPS navigation** with turn-by-turn instructions.
5. Support **login, trip history, leaderboard**, and **password change**.
6. Run on **web and Android** (Capacitor).
7. Allow **admin** to edit fuel/emission settings and user roles.

### Deliverables

- Web app (React + Vite)
- Android app (Capacitor)
- Backend API + SQLite database
- Auth, route comparison, navigation, trip history, leaderboard, admin panel
- Technical documentation (`docs/`)

### Expected findings

- Different routes can have **different fuel costs** for the same trip.
- Picking the **lowest-cost route** gives a clear fuel-saving choice.
- Users can see **RM, litres, CO₂, and time** side by side.
- GPS navigation on the chosen route works on web and phone.
- Logged-in users can **track total savings** over time.

---

## 1.4 Project Scope

### Included

- Petrol cars only
- Malaysia-focused place search
- Up to 3 route alternatives
- Vehicle pick or default 14 km/L
- Guest, user, and admin roles
- GPS navigation
- Trip history and leaderboard
- Admin config (fuel price, emissions)
- Mobile connection to PC backend on same Wi‑Fi

### Not included

- iOS app
- Diesel / electric / hybrid models
- Live fuel price from external API
- Offline maps or routing
- Payment, email, or AI features
- Cloud hosting (project uses local/LAN setup)
- Admin settings saved after server restart (in-memory only)

---

## 1.5 Project Limitations

1. Uses **public OSRM and Nominatim** — fine for FYP demo, not for heavy commercial use.
2. Only **petrol** is supported.
3. Login uses a simple **user ID header**, not full token security.
4. Admin settings **reset when the server restarts**.
5. Testing is mostly **manual** — no automated test suite in the project source.
6. Focus is **Malaysia** only.
7. Fuel figures are **estimates** (distance ÷ km/L), not exact real-world consumption.

---

## 1.6 Methodology

The project was built using **iterative development** — build one feature, test it, then add the next.

| Step | Work done |
|------|-----------|
| 1 | Backend, database, route API, calculations |
| 2 | Login, car setup, route search, results screen |
| 3 | Planning map, Android app, GPS |
| 4 | Navigation map, save trips and leaderboard |
| 5 | Admin panel, profile, password change |
| 6 | Bug fixes, performance, documentation |

This approach fits an application FYP because the goal is a **working app**, not a long research-only study.

---

## 1.7 Target Audience

| Audience | Who | What they use |
|----------|-----|----------------|
| **Primary** | Malaysian car drivers | Route planning, cost comparison, navigation |
| **Registered users** | Same as above, with account | Trip history, leaderboard, profile |
| **Admin** | Project owner / demo operator | Change settings, manage roles |
| **Secondary** | Supervisor / examiner | Review design, testing, and results |

On **phones**, the app uses a bottom sheet. On **wide web screens** (768px+), it uses a sidebar.

---

## 1.8 Summary

This project builds a route app that helps drivers choose a path based on **fuel cost and CO₂**, not just travel time. It uses React, Capacitor, Node.js, Express, and SQLite. Users can plan routes, compare up to 3 options, navigate with GPS, and track savings if logged in.

The report continues as follows:

- **Chapter 2** — Literature review  
- **Chapter 3** — Requirements  
- **Chapter 4** — System design  
- **Chapter 5** — Implementation  
- **Chapter 6** — Testing and evaluation  
- **Chapter 7** — Conclusion  
- **References** and **Appendices**

---

*Add your name, ID, supervisor, and faculty on the title page before submission.*
