# Jer Salamat

**Adapting Crop Rotations in the Chuy Valley with 40 Years of NASA Climate Data**

- **Challenge:** NASA Space Apps Challenge 2026 — *Field Shift: Adapting Farms with NASA Data*
- **Live App:** https://jer-salamat.vercel.app
- **Languages:** English (default) and Russian (`EN` / `RU`)

## Portfolio & Quick Links

This is a **team-built hackathon prototype**. This repository preserves the project in Nurlan Toigonbekov's GitHub account for future reference and portfolio use.

- [Open the live demo](https://jer-salamat.vercel.app/)
- [Browse the source code](https://github.com/Nurchik228/jer-salamat/tree/main/src)
- [View the presentation (PDF)](docs/Jer_Salamat_Pitch.pdf)
- [Инструкция на русском: открыть сайт, код и запустить проект](docs/getting-started-ru.md)

**Nurlan's contribution:** research on local crops and crop rotations, coordination of team tasks, user-flow testing and feedback, and preparation and delivery of the English pitch. Software development and the final product are credited to the team; AI assistance is disclosed below.

**Project status:** demonstration prototype, not a validated agricultural advisory service. See the data sources and limitations below.

---

## What the Project Does

**Jer Salamat** (*"Healthy Land"* in Kyrgyz) is a mobile-first web decision-support tool for farmers in the Chuy Valley (focused on Sokuluk District), Kyrgyzstan. In about two minutes, a farmer can place a pin on their field, answer 5 plain-language questions about their current crops, soil, irrigation, summer water shortages, and priority, inspect 40 years (1986–2025) of local NASA climate records, and compare their current crop sequence against three multi-year rotation plans (**Water saving**, **Soil health**, and **Market crops**).

Every number in the app carries a clear provenance badge (`Historical NASA data`, `Your answers`, `General guidance`, `Reference (FAO / published)`, or `Hypothetical scenario`), and the app never invents yields, prices, profits, or water-saving percentages.

---

## The Problem

1. **A warming valley with tight summer water:** Most of Kyrgyzstan is mountainous; the Chuy Valley in the north is one of the country's main irrigated farming regions, growing winter wheat, barley, grain maize, sugar beet, potatoes, vegetables, and forage crops watered by mountain rivers and canals. NASA POWER records for the Sokuluk area (`42.86° N, 74.30° E`) show that yearly mean air temperature has risen by **+1.3 °C along the 1986–2025 linear trend line** (**+0.32 °C per decade**, statistically significant; **+1.06 °C** comparing the 1986–1995 and 2016–2025 decade averages), while annual precipitation varies widely (**197–599 mm/year**, 40-year mean **347 mm/year**) with 2025 being both the hottest (**15.69 °C**) and driest (**197 mm**) year in the 40-year record. Warmer air increases evaporative demand when canal water turns are tightest in July and August.
2. **Repeated monoculture and high-water sequences:** Growing winter wheat year after year or repeating water-intensive row crops (sugar beet, grain maize, potatoes) without nitrogen-fixing legumes wears out soil fertility, builds up crop-specific pests and diseases, and exposes farmers to summer water shortages.
3. **Inaccessible data:** Satellite and reanalysis climate archives are published as raw API tables and scientific parameters (`T2M`, `PRECTOTCORR`, `GWETROOT`) rather than actionable, field-ready rotation comparisons on a farmer's phone.

---

## How It Works

1. **Step 1 — Pick your field (`/field`):** Search a village in the Chuy Valley via OpenStreetMap Nominatim, tap a demo point (`Sokuluk`, `Shopokov`, `Kyzyl-Tuu`), use browser GPS (never stored on a server), or drag the pin onto the field.
2. **Step 2 — Answer 5 questions (`/questions`):**
   - What do you usually grow here over 2–3 years? (*Wheat every year*, *Wheat and grain maize*, *Sugar beet, wheat, maize*, *Alfalfa then wheat*, *Potatoes then wheat*, *Vegetables then wheat*, or *I don't know*)
   - What is the soil like? (*Light/sandy*, *Medium/loam*, *Heavy/clay*, *Stony*, or *I don't know*)
   - Can you irrigate this field? (*Canal*, *Well/pump*, *No — rain only*, or *I don't know*)
   - Is water short in summer? (*Often*, *Sometimes*, *Rarely*, or *I don't know*)
   - What matters most to you? (*Save water*, *Keep soil healthy*, *Grow crops to sell*, or *I don't know*)
3. **Step 3 — See 40 years of NASA climate (`/results#climate`):**
   - Fetches 1986–2025 monthly data from the NASA POWER API for the selected coordinates (with instant cached fallback for the Sokuluk grid cell if offline or slow).
   - Computes day-weighted annual means for temperature and root-zone soil wetness, annual precipitation totals, ordinary least squares (OLS) linear trends per decade with *t*-test significance ($|t| > 2.02$, $p < 0.05$ for $n = 40$), plain-language takeaways, and an optional **+2 °C hypothetical scenario** toggle (explicitly labeled *Hypothetical — not a forecast*).
4. **Step 4 — Compare rotation plans (`/results#recommendation`):**
   - Compares the farmer's current rotation (or the published 6-field regional reference rotation when the farmer selects *"I don't know"*) against three 4-year alternatives:
     - **Water saving:** `Winter wheat → Beans or peas → Barley → Winter wheat` (FAO range: 425–613 mm/season)
     - **Soil health:** `Alfalfa (forage) → Alfalfa (forage) → Winter wheat → Beans or peas` (FAO range: 600–1088 mm/season)
     - **Market crops:** `Sugar beet → Winter wheat → Potato → Beans or peas` (FAO range: 463–650 mm/season; income not estimated)
   - Shows a *"What makes your plan yours"* breakdown that re-runs the recommendation engine without each answer to show which answers changed the top plan, which already matched the farmer's priority, and what is shared regional NASA climate.

---

## NASA Data Used (Primary Environmental Source)

NASA is the **only** source of environmental and climate numbers in Jer Salamat.

| Dataset / API | Parameters & Units | Temporal Scope & Community | Spatial Resolution | Links |
| :--- | :--- | :--- | :--- | :--- |
| **NASA POWER** (*Prediction Of Worldwide Energy Resources*, NASA Langley Research Center, derived from satellite observations and MERRA-2 assimilation) | • `T2M` — Air temperature at 2 metres, **°C** (monthly mean → day-weighted yearly mean)<br>• `PRECTOTCORR` — Precipitation corrected, **mm/day** (multiplied by days in each month and summed to **mm/year**)<br>• `GWETROOT` — Root-zone soil wetness (top ~1 m), dimensionless **0–1** from completely dry (0) to saturated (1) (monthly mean → day-weighted yearly mean) | **1986–2025** (40 complete years), **Monthly** temporal API, Agroclimatology community (`AG`) | **0.5° × 0.625°** (~50 km regional grid) | • Portal: https://power.larc.nasa.gov/<br>• API endpoint used: `https://power.larc.nasa.gov/api/temporal/monthly/point?parameters=T2M,PRECTOTCORR,GWETROOT&community=AG&longitude=74.30&latitude=42.86&start=1986&end=2025&format=JSON` |

---

## Reference Sources (Clearly Labeled as Reference / Context Only)

FAO and Kyrgyz publications are used strictly as **labeled reference or context** (`src/data/sources.json`, `src/data/rotations.json`), never presented as NASA measurements or field-level irrigation calculations:

1. **Volkov & Denisov — *Land Management in the Kyrgyz Republic* (Ch. 21, 2025 upload):**
   - **Role:** Published 6-field regional reference rotation for irrigated land in the Chuy, Kemin, and Talas valleys (`Cereals with perennial grasses → Alfalfa → Alfalfa → Winter wheat → Sugar beet → Barley`), used as the baseline comparison when a farmer selects *"I don't know"* for their current rotation.
   - **Unit:** Crop sequence (6 fields).
   - **Link:** https://taek.edu.kg/wp-content/uploads/2025/01/Землеустройство-КР-Волков.pdf
2. **FAO — *Irrigation Water Management, Training Manual 3: Crop Water Needs* (Ch. 2, 1986):**
   - **Role:** General reference water-need ranges per total growing period used only to compare relative water demand between crop sequences: winter wheat / barley **450–650 mm**, grain maize **500–800 mm**, sugar beet **550–750 mm**, potato **500–700 mm**, peas/beans **350–500 mm**, alfalfa **800–1600 mm** (mixed vegetables, oilseeds, and fallow have no single FAO range and are explicitly noted when omitted). Not Sokuluk field measurements and not irrigation requirements.
   - **Unit:** mm of water per growing period.
   - **Link:** https://www.fao.org/4/s2022e/s2022e02.htm (and irrigation context: https://www.fao.org/4/s2022e/s2022e04.htm)
3. **FAO — *Conservation Agriculture: Species Diversification* & *Soil Biology*:**
   - **Role:** Qualitative agronomic guidance on crop rotation benefits (nitrogen fixation by legume root-nodule bacteria, differing root depths, breaking crop-specific pest and disease cycles).
   - **Unit:** Qualitative guidance.
   - **Links:** https://www.fao.org/conservation-agriculture/in-practice/species-diversification/en/ and https://www.fao.org/4/y5146e/y5146e0a.htm
4. **Government of the Kyrgyz Republic — *Environmental and Social Management Framework (ESMF), WSSUAP* (Oct 2024):**
   - **Role:** Reference list of crops grown in Sokuluk District (wheat, barley, grain maize, sugar beet, potatoes, vegetables, forage crops, plus small areas of grain legumes and oilseeds), used for question options and crop icons.
   - **Links:** https://tunuksuu.kg/wp-content/uploads/2024/11/KG_ESMF_WSSUAP_PIU_SIDDWSWD_RUS1.pdf and https://economist.kg/agriculture/2026/02/06/v-sokulukie-k-viesiennim-rabotam-podghotovili-38-tysiach-gha-torobaiev-provieril/
5. **National Statistical Committee of the Kyrgyz Republic (`stat.gov.kg`):**
   - **Role:** Listed as a potential future source for verified local yield and price statistics. Not used in calculations today.
   - **Link:** https://www.stat.gov.kg/ru/statistics/selskoe-hozyajstvo/

---

## Limitations

- **Regional ~50 km grid:** NASA POWER provides regional climate on a `0.5° × 0.625°` (~50 km) grid. It is not a sensor measurement of an individual parcel, and neighboring farms in Sokuluk District share the same climate cell.
- **No verified income data:** Income (`yield × price − cost`) is **never** estimated and always displays *"Insufficient verified data"*, because verified parcel-level yields, farm-gate prices, and input costs for Sokuluk are not available.
- **General crop water ranges:** FAO water-need ranges (`mm per season`) are general climatic reference ranges, not local evapotranspiration measurements or canal irrigation schedules.
- **No soil lab or pest diagnostics:** Recommendations use farmer-reported soil texture and general rotation rules, not laboratory soil tests or field pest surveys.
- **Hypothetical "+2 °C" scenario:** The `+2 °C` chart overlay is a clearly labeled *"what if"* sensitivity line, not a weather or climate model forecast.
- **Consult a local agronomist:** Farmers should always verify any rotation change with a local agronomist before planting.

---

## Tech Stack

- **Frontend & Routing:** React 19, TypeScript, Vite 8, React Router 8
- **Styling & Design System:** Tailwind CSS 4 + custom CSS design tokens (`src/styles/tokens.css`, light & dark themes, WCAG AA contrast, 44×44 px minimum tap targets)
- **Recommendation & Climate Engine:** Pure framework-free TypeScript (`src/core/climate.ts`, `src/core/rotation.ts`)
- **Maps & Geospatial:** Leaflet + React-Leaflet (OpenStreetMap tiles with Esri World Street Map fallback, Nominatim village search)
- **Data Visualization & 3D:** Chart.js + `react-chartjs-2` (line, bar, and area charts), Three.js + React Three Fiber (`OrbitScene` 3D globe and `ValleyTerrain` on the landing page, with SVG static fallback `public/globe-fallback.svg`), Motion (`motion/react`), Lucide icons
- **Internationalization:** Built-in `EN` / `RU` dictionary system (`src/i18n/en.ts`, `src/i18n/ru.ts`) verified by `npm run i18n:check`

---

## How to Run Locally

**Prerequisites:** Node.js compatible with the versions locked in `package-lock.json`, and npm. No API keys or `.env` files are required (NASA POWER and OpenStreetMap endpoints are open).

```bash
# 1. Download the repository with Git, then open its folder
git clone https://github.com/Nurchik228/jer-salamat.git
cd jer-salamat

# 2. Install the locked dependencies
npm ci

# 3. Start the local development server
npm run dev

# 4. Run typecheck, linter, i18n parity check, and production build
npm run typecheck
npm run lint
npm run i18n:check
npm run build

# Optional utilities
node scripts/fetch-demo-data.mjs   # Refresh cached NASA POWER JSON in public/data/
npm run build:globe  # Regenerate public/globe-fallback.svg
```

---

## Team

- **Aiman Erkinbekov**
- **Nurlan Toigonbekov**
- **Kuttubai Iskakov**
- **Viacheslav Yakovlev**

---

## Use of AI

AI coding assistants helped write code and text; the idea, research, data verification, and product decisions are the team's.
