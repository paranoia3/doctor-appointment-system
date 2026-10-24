# 5. Technology Comparison

## 5.1 Frontend

Three frontend decisions are compared, each with three alternatives.

| # | Decision | Alternatives | Chosen |
|---|----------|--------------|--------|
| F1 | UI framework | React / Vue / Angular | React 19 + TypeScript + Vite |
| F2 | UI component library | Ant Design / shadcn/ui / MUI | Ant Design 6 |
| F3 | Maps | 2GIS / Leaflet / Yandex Maps | Leaflet (react-leaflet) |

### 5.1.0 Method and requirements

For each decision we took criteria from the project requirements, gave each criterion a weight (the weights sum to 100), scored every alternative from 1 (poor) to 5 (excellent) and calculated the weighted score. Then we checked how the result changes if the weights change. Facts from documentation and surveys are marked with a source number, for example [3]. The 1–5 scores are our own judgment based on those facts.

Frontend requirements that drive the choice:

| ID | Requirement | Consequence |
|----|-------------|-------------|
| FR-1 | Slot selection, booking calendar, doctor schedule | Mature calendar component (FullCalendar) |
| FR-2 | Doctor search with filters, tables of patients and appointments | Strong tables, forms, date pickers |
| FR-3 | Clinic map with distance sorting | Map with markers and clustering |
| FR-4 | Kazakh and Russian interface | i18n and a UI kit with a Kazakh locale |
| FR-5 | Four roles: patient, doctor, clinic admin, super admin | Routing with role guards, reusable layouts |
| FR-6 | Live queue screen | WebSocket integration |
| FR-7 | Analytics dashboard | Charts and statistic widgets |
| NFR-1 | Responsive from 360 px; Chrome, Safari, Firefox, Edge | Mobile-first layout |
| NFR-2 | Vitest, Playwright, MSW | Tooling that works well with the framework |
| NFR-3 | One frontend developer, working MVP by the end of December | Fast delivery, low learning curve |

---

### 5.1.1 F1: React vs Vue vs Angular

| | React | Vue | Angular |
|---|---|---|---|
| Type | UI library; router and state come from the ecosystem | Progressive framework with official router and store | Full framework: router, forms, DI, HTTP built in |
| Language | JS / TS (JSX) | JS / TS (single-file components) | TypeScript |
| Structure | Flexible, needs team conventions | Moderate | Strict |

In the Stack Overflow Developer Survey 2025 React is the most used of the three, at roughly 45–47% of respondents, while Angular and Vue are each at roughly 18% [1]. State of JS is a second source for satisfaction and retention [2].

| Criterion | Weight | React | Vue | Angular |
|-----------|:------:|:-----:|:---:|:-------:|
| Fit with required libraries (UI kit, calendar, charts, maps, i18n) | 25 | 5 | 4 | 3 |
| Learning curve and delivery speed | 20 | 4 | 5 | 2 |
| Community, maturity, hiring pool | 15 | 5 | 4 | 4 |
| TypeScript and testing support (Vitest, Playwright, MSW) | 10 | 5 | 4 | 4 |
| Performance for a SPA of this size | 10 | 4 | 5 | 4 |
| Structure for four role-based cabinets | 10 | 3 | 4 | 5 |
| i18n (KZ/RU) | 10 | 5 | 5 | 4 |
| **Weighted score** | **100** | **4.50** | **4.40** | **3.45** |

Notes on the scores:

- All the UI kits we considered (Ant Design, shadcn/ui, MUI) are React-first, and shadcn/ui exists only for React. FullCalendar, Recharts, TanStack Query, react-leaflet and react-i18next all have React versions. Vue and Angular have equivalents, but fewer.
- Vue is the easiest to learn and Angular the hardest (DI, RxJS, decorators). Angular enforces structure, while in React we have to keep it ourselves.
- Performance is good enough in all three for an application of this size.

Sensitivity: React and Vue are close (4.50 and 4.40). If the learning-curve weight is raised from 20 to 30 at the expense of ecosystem fit, Vue gets slightly ahead. So the choice of React rests mainly on the ecosystem. Angular is last under any reasonable weights for a one-developer, three-month project. It suits large teams with strict architecture rules better.

**Decision: React 19 + TypeScript + Vite.**

1. Best ecosystem for FR-1…FR-7: UI kits, calendar, charts and map bindings.
2. Largest community of the three [1], so documentation and examples are easy to find.
3. Vitest, Testing Library, MSW and Playwright are widely used together with React (NFR-2).
4. The system is a single-page application behind a login, so server-side rendering and a meta-framework such as Next.js are not needed. Vite keeps the setup simple: SPA, one REST API (`/api/v1/`), Django.

Trade-offs: React does not enforce structure, so we use feature-based folders, ESLint with Prettier and pull request review. Router, server state and client state are chosen in advance: React Router, TanStack Query and Zustand.

---

### 5.1.2 F2: Ant Design vs shadcn/ui vs MUI

| | Ant Design | shadcn/ui | MUI |
|---|---|---|---|
| Approach | Complete enterprise component library | Copy-paste components on Radix UI and Tailwind; code lives in our repository | Complete library implementing Material Design |
| Version | antd 6, needs React 18 or newer, React 19 recommended [4] | No single version; components are copied into the project [13] | Material UI plus MUI X for advanced components [5] |
| Styling | CSS variables by default in v6 [4] | Tailwind classes | Emotion (CSS-in-JS) |
| Kazakh locale | `kk_KZ` is in the official locale list [3] | No built-in locale system; texts come from our own i18n | `kkKZ` for core components [5]; date pickers have `kzKZ`, with about 36 of 53 keys translated [6] |

| Criterion | Weight | Ant Design | shadcn/ui | MUI |
|-----------|:------:|:----------:|:---------:|:---:|
| Components we need out of the box (Table, Form, DatePicker, Steps, Timeline, Drawer, Statistic) | 25 | 5 | 3 | 4 |
| Kazakh localization of built-in texts and date pickers | 15 | 5 | 3 | 4 |
| Speed of building data-heavy admin screens | 20 | 5 | 3 | 4 |
| Design control and match with our Figma mockups | 15 | 3 | 5 | 3 |
| Accessibility, bundle size, performance | 15 | 3 | 5 | 4 |
| Maintenance and long-term support | 10 | 4 | 4 | 4 |
| **Weighted score** | **100** | **4.30** | **3.70** | **3.85** |

Notes on the scores:

- Ant Design covers almost all of FR-2 and FR-7 without extra packages. shadcn/ui provides primitives, so a filterable table, steps or statistic cards would have to be assembled by hand. MUI covers most needs, but some advanced MUI X components are in paid tiers.
- Ant Design has a Kazakh locale for the whole library [3]. MUI has it for core components and partly for date pickers [5][6].
- shadcn/ui gives full control over the design and builds on accessible Radix components, which is its main advantage.

Sensitivity: shadcn/ui passes Ant Design only if design control and accessibility together weigh about 50% or more (now 30%). That fits a public site with a strong brand more than a data-heavy booking system with admin cabinets.

**Decision: Ant Design 6**, together with FullCalendar for the slot and schedule calendar (the built-in Calendar of Ant Design is a month/year date view, not a scheduler) and Recharts for the analytics dashboard.

1. It matches our screens: tables, filters, forms, steps for the patient route, statistics.
2. It has a ready Kazakh locale for the whole library (FR-4).
3. It is the fastest option for one frontend developer (NFR-3).

Trade-offs: the default look is recognizable, so we apply our own colors and typography through the theme tokens. The bundle is larger by default, so we import components individually, split code by route and check bundle size in CI. If a screen needs a fully custom component, a Radix-based one can be used next to Ant Design.

---

### 5.1.3 F3: 2GIS vs Leaflet vs Yandex Maps

The map is used to show clinics, group markers, select a clinic and open directions in an external app (FR-3). It is not part of the nearest-slot algorithm: the distance term `D` in `score = w1·T + w2·D + w3·P` is calculated on the backend from stored coordinates of clinics and patients. Clinic coordinates are entered by the clinic administrator and kept in our own database, so the map choice does not affect the booking logic.

| | 2GIS (MapGL JS API) | Leaflet | Yandex Maps (JS API) |
|---|---|---|---|
| Type | Commercial API platform, 3D vector maps (WebGL) | Open-source map library; tiles from a provider of our choice | Commercial API with a free tier |
| Access and cost | Needs an access key: a demo key or a paid subscription [7][8] | Library is BSD-2-Clause, about 42 KB of JS [12]; no key for the library itself | Free version has conditions; the license for the JS API is quoted at 120,000 rubles per year [10] |
| Conditions | Subscription terms to be agreed with 2GIS | Tile provider has its own terms; public OpenStreetMap tiles are meant for light use only | Free version only for sites open to any internet user; storing or changing API data is prohibited; the Yandex logo and "Open in Maps" button must stay visible; limit of 2.5 million requests per year [9][11] |
| React | npm package `@2gis/mapgl`, React example in the docs [8] | react-leaflet | Official React wrapper [11] |

| Criterion | Weight | 2GIS | Leaflet | Yandex |
|-----------|:------:|:----:|:-------:|:------:|
| Quality of map data for Kazakh cities | 25 | 5 | 3 | 4 |
| Licensing, cost, legal fit | 25 | 3 | 4 | 2 |
| React integration and needed features (markers, clusters, popups) | 20 | 4 | 5 | 4 |
| Learning curve and community | 15 | 3 | 5 | 3 |
| Bundle size and mobile performance | 15 | 3 | 5 | 3 |
| **Weighted score** | **100** | **3.70** | **4.25** | **3.20** |

Notes on the scores:

- 2GIS has the most detailed local data, but for the MVP we only need markers with our own coordinates, so this matters less.
- The free Yandex conditions (open public access, no storing of API data) [9] do not fit a system with login and its own database of clinic coordinates.
- Leaflet is small and has the simplest API and the most tutorials, and react-leaflet with a clustering plugin covers FR-3.

Sensitivity: 2GIS passes Leaflet only if map data quality weighs about 45% or more (now 25%), for example if we needed search by organization or 3D buildings. Yandex stays last under all reasonable weights because of its license conditions.

**Decision: Leaflet with react-leaflet**, with the tile source set in the configuration (`MAP_TILE_URL` in `.env`), so the provider can be changed without code changes.

1. It covers FR-3 with the smallest and simplest solution and keeps the map independent from booking logic.
2. No key or paid license is needed to start development.
3. It is lightweight on mobile screens (NFR-1).

Trade-offs: public OpenStreetMap tiles are fine for the demo, but production needs a commercial or self-hosted tile provider. Leaflet has no built-in routing, so the "Get directions" button opens 2GIS or Google Maps with the clinic coordinates. If 2GIS organization data becomes necessary, only the isolated `ClinicMap` component has to be replaced.

---

### 5.1.4 Summary

| Layer | Decision | Score | Alternatives |
|-------|----------|:-----:|--------------|
| Framework | React 19 + TypeScript + Vite | 4.50 | Vue (4.40), Angular (3.45) |
| UI library | Ant Design 6, FullCalendar, Recharts | 4.30 | MUI (3.85), shadcn/ui (3.70) |
| Maps | Leaflet + react-leaflet | 4.25 | 2GIS (3.70), Yandex Maps (3.20) |
| Server / client state | TanStack Query / Zustand | | |
| i18n | react-i18next with antd `kk_KZ` and `ru_RU` locales | | |
| Testing | Vitest, Playwright, MSW | | |