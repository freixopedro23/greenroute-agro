# GreenRoute Agro: when rain changes the tractor's route

[Português](README.md) · **English**

Project for **FIAP's Global Solution 2026.1** (Data Science, class 1TSCOR), on the theme *space industry and data*. It was chosen as **the best project in the class**.

The proposal is a platform that recalculates farm machinery routes using open satellite and weather data. This repository contains the analytical part that was built: a study on real INMET (Brazil's national weather institute) data showing how many days of a growing season carry operational risk for machinery, and how those days behave.

---

## The problem

Large farms usually run tractors and harvesters on fixed routes. After heavy rain, waterlogged soil increases wheel slip and rolling resistance: the tractor burns more diesel, wears faster and compacts the soil. The question behind the analysis is simple: **how often does this happen in a real season, and can public data flag those days?**

## The proposal (what the pitch presented)

GreenRoute Agro would combine elevation (Copernicus DEM), vegetation (MapBiomas), weather (INMET) and property boundaries (CAR, Brazil's rural land registry) into a terrain graph. An A\* algorithm would compute the lowest-cost route, and a Streamlit dashboard with a Folium map would show the route and the estimated CO₂ avoided.

**This part is a proposal, not an implementation.** The A\* routing, the dashboard and the cloud architecture were designed and presented in the pitch, but they are not in this repository. The savings figures in the pitch (such as 34% less diesel) are projections based on industry references, not measured results.

## The analysis (what was built)

**Data:** INMET automatic weather station A904 in Sorriso, Mato Grosso, from 01/01 to 12/31/2025: 8,760 hourly records of rainfall and temperature, aggregated into 365 days.

**Notebook steps:**
1. **Loading and cleaning:** INMET CSV with `;` separators, comma decimals and sensor gaps; conversion to numbers and daily aggregation (rainfall summed, temperature averaged).
2. **Operational risk rule:** a day with more than 5 mm of rain = high risk of wheel slip.
3. **Descriptive statistics and time series** of rainfall, with the risk threshold.
4. **Correlation and hypothesis test:** is it cooler on risk days? (Pearson and a one-sided t-test)
5. **Cumulative season rainfall** (running sum, the "discrete integral" of daily rainfall).

**Results:**
- **80 of 365 days** (22%) exceeded 5 mm of rain, concentrated from November to March: December and January had 14 and 15 risk days; July and August, none.
- **1,787 mm** of cumulative rainfall in 2025.
- Risk days were **about 2 °C cooler** (25.0 °C vs 27.1 °C; one-sided t-test, p < 0.001), and the correlation between rainfall and temperature was weak and negative (r = −0.28).

**What it means for the proposal:** for almost a quarter of the year, a route planned for dry conditions stops being the best one. That window, which runs from the end of soybean planting through harvest, is what justifies a route that adapts to the weather.

## Limitations

- **One station for a whole farm.** Rainfall measured at one point in the municipality doesn't represent a property of thousands of hectares.
- **The 5 mm threshold is a simplified rule.** How rain affects traction depends on soil type and on moisture built up over previous days.
- **Missing hours count as zero.** 633 hours (7%) have no rainfall reading; in the daily sum they count as zero, so rainfall may be underestimated.
- **Days in UTC.** INMET timestamps are in UTC, and the daily aggregation uses the UTC date, not Mato Grosso local time (UTC−4).
- **The test shows association, not causation.** And temperature doesn't burn diesel; it only helps characterize rainy days.
- **Diesel consumption was not measured.** The 12.8 L/ha figure cited in the notebook's conclusion comes from industry references, not from this data. Validating savings would require real machinery telemetry.

## How to run

```bash
pip install -r requirements.txt
jupyter notebook greenroute_agro_analise_climatica.ipynb
```

The notebook reads `dados/dados_inmet_sorriso_2025.csv` and writes `dados/greenroute_diario_final.csv` (a daily table with the risk flag and cumulative rainfall). Code and comments are in Portuguese.

## Structure

```
├── dados/
│   ├── dados_inmet_sorriso_2025.csv   raw hourly INMET data (station A904)
│   └── greenroute_diario_final.csv    daily table produced by the notebook
├── greenroute_agro_analise_climatica.ipynb
└── requirements.txt
```

## Team

Group project by **Bruno Gomide** (team lead), **Leonardo Lacerda**, **Pedro Freixo** and **Vitor Coppe**. My main contribution was the analysis notebook.

Data source: [INMET – Instituto Nacional de Meteorologia](https://tempo.inmet.gov.br/), open data.

---

Published by **Pedro Freixo** · [GitHub](https://github.com/freixopedro23)
