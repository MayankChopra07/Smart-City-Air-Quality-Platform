# AirWise — Hyperlocal Air Quality & Activity Planner

> **"Don't just check the air. Find a comparatively better place and time for your outdoor activity."**

AirWise is a Smart City urban innovation web application that retrieves real air-quality forecast data, lets you compare up to three locations, visualises pollutant concentrations and hourly trends, and ranks location-and-time combinations to help you plan outdoor activities.

---

## Problem Statement

Air pollution varies across geographic areas and throughout the day. Available air-quality dashboards typically show a single AQI number and do not help people decide *when* and *where* to go outdoors. AirWise turns modelled forecast data into understandable visualisations and explainable, comparative recommendations.

---

## The USP

**Multi-location comparison + time-window analysis + explainable ranking** for outdoor activity planning.

Unlike a generic AQI dashboard, AirWise:
- Compares up to 3 cities or locations simultaneously
- Evaluates every hourly forecast slot in your chosen planning window (6, 12, or 24 hours)
- Ranks candidates by a transparent **Comparative Pollution Score (CPS)**
- Shows the actual pollutant values behind every recommendation
- Never fabricates data or silently replaces missing values

---

## Technology Stack

| Layer | Technology |
|-------|-----------|
| UI Framework | React 19 |
| Build Tool | Vite 8 |
| Routing | React Router v7 |
| Charts | Recharts |
| Icons | Lucide React |
| Testing | Vitest + Testing Library |
| Styling | Vanilla CSS (custom design system) |

---

## API Integrations

### Open-Meteo Air Quality API
- Endpoint: `https://air-quality-api.open-meteo.com/v1/air-quality`
- Provides hourly modelled forecasts for PM2.5, PM10, NO₂, O₃, SO₂, CO
- Sourced from **CAMS ENSEMBLE** (Copernicus Atmosphere Monitoring Service)
- No API key required for basic access
- Documentation: https://open-meteo.com/en/docs/air-quality-api

### Open-Meteo Geocoding API
- Endpoint: `https://geocoding-api.open-meteo.com/v1/search`
- Powers city/place search with name suggestions
- Based on GeoNames data
- Documentation: https://open-meteo.com/en/docs/geocoding-api

### Browser Geolocation API
- Used when user clicks "Use my location"
- Permission is only requested on user action
- Graceful fallback to manual search on denial or error

---

## Key Features

1. **Dashboard** — Pollutant cards, hourly forecast chart, location search
2. **Location Comparison** — Side-by-side cards for up to 3 locations with timezone mismatch detection
3. **Activity Planner** — Select activity + window → ranked recommendations with inspectable data
4. **Methodology Page** — Full transparency on data sources, scoring algorithm, and limitations
5. **Error Handling** — Network failures, missing data, geolocation denial — all handled gracefully

---

## Setup (Windows)

### Prerequisites
- Node.js 18+ installed (https://nodejs.org/)
- No API keys needed

### Installation

```powershell
# 1. Open this folder in your terminal
cd "C:\Users\mayan\.gemini\antigravity\scratch\airquality-platform"

# 2. Install dependencies
npm install

# 3. Start the development server
npm run dev
```

Open the URL printed by Vite (typically http://localhost:5173) in your browser.

### Production Build

```powershell
npm run build
npm run preview
```

### Run Unit Tests

```powershell
npm test
```

---

## Recommendation Algorithm

The **Comparative Pollution Score (CPS)** is calculated as follows:

1. **Collect candidates** — All future hourly slots in the planning window, from all selected locations
2. **Validate** — Require valid PM2.5 + at least 2 additional pollutants; reject insufficient slots
3. **Relative rank** — For each pollutant, rank all candidates from 0 (lowest concentration = best) to 1 (highest = worst)
4. **Weighted composite** — Average ranks using configured weights, normalised to available pollutants only
5. **Scale to 0–100** — Lower CPS = comparatively less polluted in this candidate set
6. **Sort & return** — Ascending by CPS; ties broken by earlier time then location order

### Weights

| Pollutant | Weight |
|-----------|--------|
| PM2.5 | 35% |
| PM10 | 20% |
| NO₂ | 20% |
| O₃ | 15% |
| SO₂ | 5% |
| CO | 5% |

**Important:** CPS is NOT an official AQI. It is a relative comparison within your selected set only. Rankings change when you add or remove locations. This is not medical advice.

---

## Attribution & Licences

- **Air-quality data**: Open-Meteo (https://open-meteo.com/), sourced from CAMS ENSEMBLE. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
- **Location search**: Open-Meteo Geocoding API, based on GeoNames.
- **Icons**: Lucide (https://lucide.dev/), MIT licence.
- **Charts**: Recharts (https://recharts.org/), MIT licence.
- **CPS**: Calculated by this project — not from any official data provider.

Check [Open-Meteo terms](https://open-meteo.com/en/terms) before production or commercial deployment.

---

## Testing

Tests cover the recommendation engine in `src/tests/scoring.test.js`:

- Valid data ranking
- Missing PM2.5 rejection
- Insufficient pollutant rejection
- Null not substituted as zero
- Past time exclusion
- Planning window enforcement
- Deterministic tie-breaking
- CPS within 0–100 bounds
- Cross-location comparison

Run with: `npm test`

---

## Forecast Limitations

- Values are **modelled grid-cell averages** — not readings from a sensor at your exact location
- Grid cells are typically several kilometres across
- Forecast accuracy degrades beyond 24 hours
- Coverage and pollutant availability vary by region
- This is not an official city monitoring system

---

## 2-Minute Demo for Judges

1. Open the app and go to **Dashboard**
2. Search for **London** — see pollutant cards and hourly chart
3. Click **Compare** → add **Tokyo** and **Delhi**
4. View side-by-side pollutant data and the comparison chart
5. Click **Activity Planner** → select **Cycling** → choose **Next 12 hours**
6. Ranked results appear with CPS scores
7. Click **Inspect forecast data** on the top result — see exact values used
8. Scroll down to the **Methodology** page (nav link) to view data source attributions

---

## Project Structure

```
src/
├── components/        # Navbar, Footer, LocationSearch, PollutantCards, ForecastChart, etc.
├── pages/             # Dashboard, ComparePage, PlannerPage, MethodologyPage
├── services/          # api.js — geocoding and air-quality API calls
├── utils/             # scoring.js, formatting.js
├── config/            # app.js (APP_NAME, weights, activities), pollutants.js
├── context/           # AppContext.jsx — global state management
├── styles/            # global.css — design tokens + all CSS
└── tests/             # scoring.test.js — unit tests
```

---

## Future Improvements

- Activity-specific pollutant weighting (higher PM2.5 weight for jogging vs. walking)
- Saved favourite locations
- Map visualisation of comparison locations
- Share recommendation via URL
- Pollen and UV index integration
- CSV export of forecast data
- Progressive Web App (PWA) support
