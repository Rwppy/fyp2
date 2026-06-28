# CHAPTER 1: INTRODUCTION

## 1.1 Overview

Many drivers in Malaysia use navigation applications to plan their journeys. These tools are useful for showing travel time and directions. However, they do not usually show **fuel cost in ringgit**, **fuel used in litres**, or **CO₂ emissions** for each possible route. Because of this, drivers may choose a faster route that is more expensive to drive.

This project develops **Intelligent Route Cost & Efficiency** (Android app name: **Route Estimator**), an application-based system that compares driving routes using fuel cost, fuel volume, CO₂, and travel time. The system has three parts: a **React** web and mobile client, a **Node.js and Express** backend API, and a **SQLite** database for users, trips, and leaderboard data.

The main workflow is as follows. The user logs in, registers, or continues as a guest. The user selects a vehicle from four Malaysian brands (Toyota, Honda, Proton, and Perodua) or skips setup to use a default value of **14 km/L**. The user enters an origin and destination. The system finds up to **three** route options using OSRM and calculates cost using default values of **RM 2.50 per litre** and **2.31 kg CO₂ per litre**. The route with the **lowest fuel cost** is recommended. The user can view the results on a map, start **GPS navigation**, and—if logged in—save trip history and join a **leaderboard** with seven rank levels (Bronze to Legend).

This is an **application-based** Final Year Project. The main output is working software: a web app and an Android app built with **Capacitor**. The system is designed to run on a **local network**, with the backend on port **4000** and the phone connecting through a configurable server address.

---

## 1.2 Problem Statement

### How the Problem Was Identified

When planning a trip, drivers often compare two or more roads—for example, a highway that is faster but longer, and a local road that may use less fuel. Common navigation apps focus on **fastest time** or **shortest distance**. They do not show the **petrol cost**, **litres used**, and **CO₂ produced** for each option in one clear view. Drivers must guess or calculate these values manually, which is slow and unreliable.

In Malaysia, fuel cost is a major part of transport spending. Small savings on each trip can add up over time. Users who want to reduce emissions also lack a simple way to see CO₂ differences between routes and track their savings over many trips.

### Nature of the Problem

The problem has four main points:

1. **Missing information** — Standard map apps do not show fuel cost and CO₂ for each route using the driver’s vehicle efficiency.
2. **Hard to compare** — Without software, it is difficult to get several routes and apply the same fuel price and emission values to each.
3. **No personal records** — Drivers cannot easily keep a history of money, fuel, and emissions saved compared with other routes.
4. **Mobile testing needs** — A system running on a computer cannot be tested on a phone unless the network and server address are set up correctly.

### Who Is Affected

- Private car owners and commuters who want to save fuel;
- Users who want to track CO₂ savings;
- Administrators who need to adjust fuel price and emission settings for testing or demonstration.

### Why a Software Solution Is Needed

The solution requires geocoding, routing, cost calculation, maps, GPS, and data storage to work together. These tasks must be done automatically when the user plans or drives a trip. A single software application can connect all these steps in one workflow.

---

## 1.3 Project Objectives

The objectives of this project are:

1. To build a route planning module that finds up to **three** driving routes between two locations in Malaysia.
2. To calculate **fuel used (L)**, **trip cost (RM)**, and **CO₂ (kg)** for each route based on distance and vehicle efficiency.
3. To **recommend the route with the lowest fuel cost** and show comparison values (money, fuel, CO₂, and time) against another route.
4. To provide **GPS navigation** with turn-by-turn guidance, route progress, off-route warning (**80 m**), and arrival detection.
5. To support **user accounts**, trip history, analytics, and a **leaderboard** for registered users.
6. To deliver the system on **web and Android**, with a layout suitable for both phone and desktop use.
7. To provide an **admin module** to edit calculation settings and manage user roles.

### Expected Deliverables

| No. | Deliverable |
|-----|-------------|
| 1 | Web application (React) |
| 2 | Android mobile application (Capacitor) |
| 3 | Backend REST API (Express) |
| 4 | SQLite database |
| 5 | Authentication (register, login, guest, change password) |
| 6 | Route comparison and GPS navigation |
| 7 | Trip history and analytics |
| 8 | Leaderboard and rank system |
| 9 | Admin module |
| 10 | Technical documentation |

### Expected Findings

After implementation and testing, the project should show that:

- Different routes can have different fuel costs and emissions under the same calculation rules;
- Choosing the **lowest-cost route** gives a clear basis for fuel-efficient driving;
- Side-by-side results help users balance **cost and travel time**;
- GPS navigation works on web and Android using the recommended route;
- Registered users can **record savings** over time through trip history and leaderboard ranks.

---

## 1.4 Project Scope

### What Is Included

- User registration, login, guest mode, and password change;
- Vehicle selection (four brands) or default **14 km/L**;
- Route search with place autocomplete and optional GPS origin;
- Up to three route alternatives with fuel cost and CO₂ estimates;
- Interactive planning map and results dashboard;
- GPS navigation on the recommended route;
- Trip history, analytics, and leaderboard for logged-in users;
- Admin settings for fuel price, emission factor, and user roles;
- Server URL setup for mobile testing on a local network.

### What Is Not Included

- iOS application;
- Diesel, electric, or hybrid vehicle models;
- Live fuel price from external APIs;
- Offline maps or offline routing;
- Payment, email, or push notifications;
- Cloud hosting or production deployment;
- Artificial intelligence or machine learning routing.

### Assumptions

- Users have internet access to the backend and to OSRM/Nominatim services.
- For mobile testing, the phone and computer are on the same Wi‑Fi network.
- Default values (RM 2.50/L, 2.31 kg CO₂/L, 14 km/L) are used for demonstration and can be changed by an admin during a session.

---

## 1.5 Project Limitations

1. The system depends on **public OSRM and Nominatim services**, which have usage limits and are not suitable for large-scale commercial use without self-hosting.
2. Only **petrol** vehicles are supported in the calculation model.
3. **Authentication is simplified** for a prototype and is not the same as production systems that use secure tokens.
4. **Admin settings** are stored in memory and return to default values after the server restarts.
5. The project is designed for **local network deployment**, not cloud hosting.
6. Testing is mainly **manual**; there is no automated test suite in the project source code.
7. Place search is focused on **Malaysia** only.
8. Fuel use is **estimated** from distance and km/L; actual consumption may differ due to traffic and driving style.

---

## 1.6 Methodology

This project used an **iterative and incremental** development approach. Features were built step by step, tested, and improved rather than completed all at once.

The main phases were:

1. **Backend** — API, database, routing, and cost calculation;
2. **Frontend** — Login, vehicle setup, route search, and results;
3. **Maps and mobile** — Planning map, Android app, and GPS support;
4. **Navigation** — Turn-by-turn guidance and trip completion;
5. **Admin and profile** — Settings, user roles, and password change;
6. **Refinement** — Bug fixes, performance improvements, and documentation.

This approach suits an **application-based** FYP because it focuses on delivering a working system that can be tested at each stage.

---

## 1.7 Target Audience

The main users are **private car drivers in Malaysia** who use petrol vehicles.

| User type | Description |
|-----------|-------------|
| **Guest** | Can plan routes, compare costs, and navigate; cannot save trip history |
| **Registered user** | Can use trip history, analytics, leaderboard, and profile settings |
| **Administrator** | Can change calculation settings and user roles |

The interface uses a **bottom sheet** on phones and a **sidebar** on wider web screens (768 px and above).

Secondary readers include **project evaluators** and **future developers** who may extend the system.

---

## 1.8 Summary

This project developed **Intelligent Route Cost & Efficiency**, a web and Android application that helps drivers compare routes by **fuel cost**, **fuel use**, **CO₂**, and **time**. The system recommends the lowest-cost route among up to three options, supports GPS navigation, and allows registered users to track savings through trip history and a leaderboard.

The problem is that common navigation tools do not provide integrated fuel and emissions comparison. Seven objectives guide the work, from route calculation to admin control. The scope covers petrol routes in Malaysia, three user roles, and local network deployment. Some limitations include reliance on public map services, simplified security, and estimated fuel values.

The rest of this report is organised as follows. **Chapter 2** reviews related literature. **Chapter 3** presents requirements analysis. **Chapter 4** describes system design. **Chapter 5** explains implementation. **Chapter 6** covers testing and evaluation. **Chapter 7** presents the conclusion. **References** and **Appendices** provide supporting material.
