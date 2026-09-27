# Wind Speed Modelling for Derivatives Pricing and Risk Management

**Continuous-time modelling of wind speed, applied to the pricing of a synthetic wind variance swap on German onshore wind generation.**

This repository contains the full research pipeline behind a Master's thesis and research assistantship in Mathematical Finance (Energy Markets). It covers every step from raw wind speed data to a closed-form derivative fair strike, and validates the results across two datasets.

---

## At a glance

| | |
|---|---|
| **Question** | Can a CAR(4) + GARCH(1,1) wind speed model give a tractable fair strike for a wind variance swap, and how much pricing error does a poor-quality proxy dataset introduce? |
| **Underlying** | Daily wind speed → German onshore wind generation (SMARD) |
| **Models** | Box–Cox + Fourier deseasonalisation, AR(4), GARCH(1,1), CAR(4) with Gaussian / NIG innovations, Heston-type stochastic volatility (theory) |
| **Instruments** | Synthetic wind variance swap, CAWS (Cumulative Accumulated Wind Speed) derivative |
| **Validation** | Diebold–Mariano forecast tests, GARCH diagnostics, Value of Information, hedging simulation |
| **Stack** | Python (pandas, numpy, scipy, statsmodels, arch, matplotlib), Jupyter, LaTeX |

---

## Two datasets, one methodology

The same pipeline is run twice, on purpose. **Dataset A** was used to build and test the methodology. **Dataset B** is the realistic dataset used for pricing. Comparing the two measures how much the choice of data matters for pricing (the *Value of Information*).

| | Dataset A — Benchmark (proxy) | Dataset B — Reference |
|---|---|---|
| **Source** | OpenWeatherMap API | Open-Meteo, ERA5 reanalysis |
| **Location** | Bologna, Italy | Nordfriesland, Germany (54.77° N, 8.85° E) |
| **Height** | 10 m | 100 m (turbine hub height) |
| **Period** | 2015–2018 (1,462 days) | 2015–2024 (3,652 days) |
| **File** | `data/data.csv` | `data/germany_wind.csv` |
| **Notebooks** | `benchmark_notebooks/` | `reference_nb/` |
| **Write-ups** | `benchmark_latex/` | `reference_tex/` |
| **Strengths** | Free, easy to obtain, enough to develop the method | Hub height, aligned with German wind fleet, 10 years allows out-of-sample checks |
| **Limitations** | Low-wind site, 10 m, only 4 years, far from the market being hedged | Model-based (not an anemometer), daily resolution only |

Market data for both tracks come from the **SMARD** platform of the Bundesnetzagentur: hourly German onshore wind generation and day-ahead electricity prices, 2015–2024.

---

## Pipeline: the seven phases

Each phase is a self-contained Jupyter notebook. It reads the CSV outputs of earlier phases and writes its own CSVs and figures. Dataset A and Dataset B follow the same seven phases.

| Phase | Topic | What it does |
|---|---|---|
| **1** | Deseasonalisation | Box–Cox transform, Fourier seasonal mean and variance, standardised residual series W̃(t) |
| **2** | AR(4) | Autoregressive mean dynamics, walk-forward forecasts vs a persistence benchmark |
| **3** | GARCH(1,1) | Conditional volatility layer h(t) on the AR(4) residuals |
| **4** | CAR(4) | Continuous-time embedding (matrix A, eigenvalues), conditional forecasts, NIG innovations |
| **5** | Stochastic volatility | Theoretical chapter: two-SDE Heston/CIR system, Feller condition, characteristic function, variance term structure |
| **6** | Synthetic variance swap | SMARD generation and prices, realised variance (Track A) vs model-implied fair strike K = h × C₀ (Track B), power curve, merit-order effect, payoff simulation |
| **7** | Validation | Diebold–Mariano tests, GARCH diagnostics, Value of Information, CAWS hedging simulation, forecast error distributions |

Pricing uses a benchmark assumption of zero market price of risk (θ = 0), because no option-implied data on wind are available for this market. Calibrating θ to EEX German Power options is left as a future extension.

---

## Key results

| Result | Dataset A (Bologna) | Dataset B (Nordfriesland) |
|---|---|---|
| Variance swap fair strike K<sub>var</sub> | 0.054 | 0.873 |
| One-step RMSE: persistence → AR(4) | 0.887 → 0.780 | 1.005 → 0.868 |
| Diebold–Mariano statistic vs persistence | 8.26 (rejects H₀) | 16.76 (rejects H₀) |
| Power-curve fit: SMARD generation vs wind³ | R² ≈ 0.0001 | R² = 0.59 |

- **Value of Information.** Pricing with the Bologna proxy instead of Nordfriesland gives a fair strike that is **93.8% too low**. After correcting for measurement height (×1.931), the error is still **88.0%**. This shows that geography, not height, is the main source of the error.
- **Forecasting.** All fitted models clearly beat persistence, but they are equivalent to each other for one-step point forecasts.
- **Merit-order effect.** Wind generation and day-ahead prices are negatively correlated (ρ ≈ −0.16), a weak natural hedge for producers.

For the full derivations, caveats and discussion, see the thesis in [`Thesis/`](Thesis/).

---

## Repository structure

```
├── data/                   Raw datasets
│   ├── data.csv                  Dataset A: Bologna wind speed (OpenWeatherMap)
│   ├── germany_wind.csv          Dataset B: Nordfriesland wind speed (ERA5)
│   ├── Actual_generation_*.csv   SMARD hourly onshore wind generation
│   └── Day-ahead_prices_*.csv    SMARD hourly day-ahead prices
│
├── benchmark_notebooks/    Dataset A: one notebook per phase (phase1 … phase7)
├── benchmark_latex/        Dataset A: phase write-ups (LaTeX / PDF)
│   └── figures/                All Dataset A figures
│
├── reference_nb/           Dataset B: one notebook per phase (Phase_1 … Phase_7)
├── reference_tex/          Dataset B: phase write-ups (LaTeX)
│   └── Plots/                  All Dataset B figures
│
├── Thesis/                 Thesis chapters (LaTeX + PDF), bibliography, defence slides
├── papers/                 Reference literature
└── reports/                Project roadmap and research map
```

Each phase folder contains the notebook plus the CSVs it produces, for example `garch_h_series_phase3.csv` and `car4_parameters_phase4.csv`. Later phases load these CSVs directly.

---

## Reproducing the results

1. Install the dependencies:
   ```bash
   pip install pandas numpy scipy statsmodels arch matplotlib jupyter
   ```
2. Open a notebook from its own folder. All paths are relative to the notebook's location, e.g. `../../data/germany_wind.csv`.
3. Run the phases in order (1 → 7) for a full rebuild. Every intermediate CSV is committed, so you can also run any single phase on its own.

Figures are saved as PNG (150 dpi) to `benchmark_latex/figures/` (Dataset A) or `reference_tex/Plots/` (Dataset B).

---

## Main references

- Benth, F. E. & Šaltytė Benth, J. (2009). *Dynamic pricing of wind futures.*
- Tol, R. S. J. (1997). *Autoregressive conditional heteroscedasticity in daily wind speed measurements.*
- Bollerslev, T. (1986). *Generalized autoregressive conditional heteroskedasticity.*
- Heston, S. L. (1993). *A closed-form solution for options with stochastic volatility.*
- Carr, P. & Lee, R. (2009). *Volatility derivatives.*
- Diebold, F. X. & Mariano, R. S. (1995). *Comparing predictive accuracy.*
- Brockwell, P. J. & Marquardt, T. (2005). *Lévy-driven and fractionally integrated ARMA processes with continuous time parameter.*

---

## Author

**Enrico Butali**, Master's thesis and research assistantship in Mathematical Finance (Energy Markets).

Data: Open-Meteo / ECMWF ERA5, OpenWeatherMap, and SMARD (Bundesnetzagentur).
