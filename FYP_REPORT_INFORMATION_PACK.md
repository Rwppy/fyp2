# CHAPTER 2: LITERATURE REVIEW

## 2.1 Overview

This chapter reviews existing applications, technologies, and related work connected to the project **Intelligent Route Cost & Efficiency**. The purpose of the literature review is to show that the problem addressed in Chapter 1 is recognised in practice and in research, and to explain how other systems handle route planning, fuel use, and emissions.

The review covers four areas: (1) commercial navigation applications, (2) eco-routing and fuel-efficient travel, (3) fuel consumption and CO₂ estimation methods, and (4) open-source mapping and routing tools used in this project. A short section on mobile location-based services is also included because the final system is deployed as a web and Android application.

This chapter is based on a background study of published articles, official product documentation, and open-source project resources. It focuses on **application-based** work—real systems and tools that users can access—not only theoretical models.

---

## 2.2 Navigation and Route Planning Applications

Navigation applications are widely used for daily travel. They help drivers find locations, compare paths, and receive turn-by-turn directions. Most successful products in this area prioritise **travel time**, **traffic conditions**, or **shortest distance**. Fuel cost and carbon emissions are usually secondary features, if they appear at all.

### 2.2.1 Mainstream Commercial Applications

**Google Maps** is one of the most used navigation platforms worldwide. It provides driving directions, live traffic, alternative routes, and estimated time of arrival. Users can choose among several paths, but the app generally recommends the fastest or most practical route. Fuel price or CO₂ comparison between alternatives is not shown as a main decision metric in the standard driving interface (Google, 2024).

**Waze** is a community-based navigation app now owned by Google. It focuses on real-time traffic, road hazards, and fastest arrival time through user reports. Like Google Maps, it helps drivers avoid congestion but does not present a detailed fuel-cost or emissions comparison for each route option (Google, 2024).

**Apple Maps** and other built-in phone map services follow a similar model: strong routing and guidance, limited support for economic or environmental comparison between multiple driving paths (Apple, 2024).

These applications confirm that **route planning is a solved problem at the navigation level**. Drivers can obtain directions easily. However, they also show a **gap** relevant to this project: the user is not guided to choose a route based on **lowest fuel cost** or **lowest CO₂** using personal vehicle efficiency.

Other related apps address **fuel tracking** rather than route comparison. For example, **Fuelio** and similar tools let users log refuelling events and monitor consumption over time. They support awareness of fuel use but do not integrate multi-route planning, live navigation, and side-by-side cost comparison in one workflow. This project aims to combine **planning, comparison, navigation, and savings history** in a single system.

**Table 2.1** compares mainstream navigation apps with the proposed system.

**Table 2.1** Comparison of related navigation applications

| Application | Main focus | Multiple routes | Fuel cost per route | CO₂ estimate | Savings history |
|-------------|------------|-----------------|---------------------|--------------|-----------------|
| Google Maps | Time, traffic | Yes | No | No | No |
| Waze | Fastest time, crowdsourced traffic | Yes | No | No | No |
| Fuel logging apps | Refuel tracking | No | Partial (logs only) | No | Partial |
| This project | Lowest fuel cost | Yes (up to 3) | Yes | Yes | Yes (registered users) |

---

## 2.3 Eco-Routing and Fuel-Efficient Route Selection

**Eco-routing** (also called green routing or energy-efficient routing) refers to selecting a path that reduces fuel use, emissions, or energy consumption—not only travel time or distance. Transport researchers have studied this topic because road vehicles contribute a large share of greenhouse gas emissions, and small changes in route choice can reduce fuel consumption (Barth et al., 2013; Demir et al., 2014).

Studies in eco-routing often compare a **fastest-time route** with an **eco-friendly route**. Results show that the eco-route may take slightly longer but can use less fuel and produce less CO₂, especially in urban areas with frequent stops or heavy traffic (Demir et al., 2014). Some work uses detailed vehicle models; other work uses simpler models based on distance and average efficiency—the approach used in this project.

Commercial interest in eco-routing has grown. Some vehicle manufacturers and fleet systems offer “eco drive” scores or fuel-saving tips. However, **free consumer navigation apps rarely expose eco-routing as the primary recommendation rule**. This project applies a clear and transparent rule: **recommend the route with the lowest estimated fuel cost (RM)**, using distance and km/L, which is easy for users to understand.

The backend attempts to favour fuel-efficient paths by requesting routes from OSRM with **motorway exclusion** where supported, encouraging alternatives that may use less fuel on certain trips. This follows the general idea in eco-routing literature that highway speeds and driving patterns affect consumption, even though the project uses a simplified cost model rather than a physical engine simulation.

---

## 2.4 Fuel Consumption and CO₂ Emission Estimation

To compare routes, the system must estimate **fuel used** and **CO₂ emitted**. In transport studies, two common approaches are used:

1. **Microscopic models** — Detailed simulation of speed, acceleration, and engine behaviour. These are accurate but complex and need large amounts of data.
2. **Macroscopic models** — Estimates based on trip distance, average speed, and fuel economy (km/L or L/100 km). These are simpler and suitable for route-level comparison (Barth et al., 2013).

This project uses a **macroscopic model**, which matches the level of detail available from standard routing APIs:

- **Fuel (litres)** = distance (km) ÷ efficiency (km/L)
- **Cost (RM)** = fuel (L) × price per litre (RM/L)
- **CO₂ (kg)** = fuel (L) × emission factor (kg CO₂/L)

Government and environmental agencies publish **emission factors** for petrol and diesel. For example, the Intergovernmental Panel on Climate Change (IPCC) provides guidelines for calculating transport emissions from fuel consumption (IPCC, 2006). National energy and environment reports also publish values for road transport. The project default of **2.31 kg CO₂ per litre** of petrol and **RM 2.50 per litre** are configurable constants that an administrator can adjust during a session.

Vehicle **fuel efficiency** varies by model, engine, load, and driving style. Consumer databases and manufacturer figures often report mixed-driving km/L values. This project includes a small catalogue of Malaysian market models (Toyota, Honda, Proton, Perodua) and a default of **14 km/L** when the user skips vehicle setup. This approach is consistent with practical apps that trade perfect accuracy for **useful comparison between routes** using the same assumptions.

---

## 2.5 Open Source Mapping and Routing Technologies

Because this is a student-developed application, it relies on **open-source** map and routing services instead of paid commercial map APIs. Three tools are central to the project.

### 2.5.1 OpenStreetMap, Nominatim, and OSRM

**OpenStreetMap (OSM)** is a collaborative map of the world. Volunteers and organisations contribute road networks and place data. OSM is widely used in research and in routing engines because the data is open and updatable (OpenStreetMap Foundation, 2024).

**Nominatim** is a geocoding service built on OSM data. It converts place names into coordinates (forward geocoding) and coordinates into addresses (reverse geocoding). This project uses Nominatim for place search and for displaying readable location names in trip history. Search is biased to **Malaysia** to improve local results (OpenStreetMap Foundation, 2024).

**Open Source Routing Machine (OSRM)** is a high-performance routing engine for OSM road networks. It supports driving profiles, alternative routes, turn-by-turn steps, and encoded polylines for map display (Luxen & Vetter, 2011; Project OSRM, 2024). This project uses the public OSRM driving service to obtain up to **three** route alternatives between an origin and a destination.

Using OSRM and Nominatim allows the project to demonstrate full routing functionality **without proprietary map licensing fees**. The trade-off is dependence on public servers, usage policies, and network availability—limitations noted in Chapter 1.

### 2.5.2 Map Libraries and Mobile Location Services

For map display, the project uses **Leaflet** for route preview during planning and **MapLibre GL** for GPS navigation with a tilted, driver-facing view. Both libraries are open source and commonly used in web mapping projects (Leaflet, 2024; MapLibre, 2024).

For mobile deployment, **Capacitor** wraps the web application as a native Android shell and provides access to device **GPS** through a geolocation plugin (Ionic, 2024). This hybrid approach is common in modern application development because one codebase can serve web and mobile users.

**Table 2.2** summarises the technologies reviewed and their role in this project.

**Table 2.2** Open-source technologies used in the project

| Technology | Purpose in literature / industry | Use in this project |
|------------|----------------------------------|---------------------|
| OpenStreetMap | Open road and place data | Base map data for routing and tiles |
| Nominatim | Geocoding | Origin/destination search and place labels |
| OSRM | Driving routes and steps | Up to 3 alternatives, navigation instructions |
| Leaflet | Web map display | Planning-phase route map |
| MapLibre GL | Vector map, navigation UI | GPS navigation view |
| Capacitor | Hybrid mobile apps | Android app and GPS access |

---

## 2.6 Summary

The literature review and background study show that **navigation applications are mature**, but **fuel-cost and emissions-based route comparison remains limited** in mainstream consumer products. Google Maps and Waze solve direction finding and traffic avoidance well; they do not typically recommend the route with the lowest fuel cost or show CO₂ differences between alternatives using the driver’s vehicle efficiency.

Research on **eco-routing** supports the idea that alternative paths can save fuel and reduce emissions, sometimes with a small time penalty. Simpler **macroscopic models** (distance ÷ km/L, fuel × emission factor) are widely used when detailed engine data is unavailable and are appropriate for this project’s comparison goal.

**OpenStreetMap**, **Nominatim**, and **OSRM** provide a practical foundation for building a route planner without commercial map fees. **Leaflet**, **MapLibre GL**, and **Capacitor** support web and Android delivery with GPS navigation.

Together, these findings justify the project design: a **Malaysia-focused**, **application-based** system that (1) fetches multiple routes, (2) estimates cost and CO₂ transparently, (3) recommends the lowest-cost option, (4) supports GPS navigation, and (5) records savings for registered users. The next chapter defines the requirements of this system in detail.

---

## References (Chapter 2)

*Note: Verify all entries against your university referencing style (APA / IEEE). Add 2–3 journal or conference papers from your own library search where marked.*

Apple. (2024). *Apple Maps*. Apple Inc. https://www.apple.com/maps/

Barth, M., Boriboonsomsin, K., & Scora, G. (2013). Energy and emissions impacts of roadway traffic management strategies. *Transportation Research Record*, 2340(1), 47–56. https://doi.org/10.3141/2340-06

Demir, E., Bektaş, T., & Laporte, G. (2014). A review of recent research on green road freight transportation. *European Journal of Operational Research*, 237(3), 775–793. https://doi.org/10.1016/j.ejor.2013.12.033

Google. (2024). *Google Maps Platform documentation*. Google LLC. https://developers.google.com/maps

Intergovernmental Panel on Climate Change. (2006). *2006 IPCC guidelines for national greenhouse gas inventories, Volume 2: Energy*. IPCC. https://www.ipcc-nggip.iges.or.jp/

Ionic. (2024). *Capacitor documentation*. Ionic. https://capacitorjs.com/docs

Leaflet. (2024). *Leaflet — An open-source JavaScript library for mobile-friendly interactive maps*. https://leafletjs.com/

Luxen, D., & Vetter, C. (2011). Real-time routing with OpenStreetMap data. In *Proceedings of the 19th ACM SIGSPATIAL International Conference on Advances in Geographic Information Systems* (pp. 513–516). ACM. https://doi.org/10.1145/2093973.2094062

MapLibre. (2024). *MapLibre GL JS*. MapLibre. https://maplibre.org/

OpenStreetMap Foundation. (2024). *OpenStreetMap*. https://www.openstreetmap.org/

OpenStreetMap Foundation. (2024). *Nominatim*. https://nominatim.org/

Project OSRM. (2024). *OSRM — Open Source Routing Machine*. https://project-osrm.org/

---

*Author reminder: Search Google Scholar for “eco-routing navigation,” “fuel-efficient route mobile application,” and “CO₂ emission factor petrol Malaysia” to add local or recent papers to this list.*
