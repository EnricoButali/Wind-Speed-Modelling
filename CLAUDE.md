# Wind-Speed-Modelling — RAship Project

## Role and context
Junior Quantitative Researcher in Energy Markets, Mathematical Finance
RAship. Goal: wind speed modelling → derivatives pricing and risk
management. Academic English, precise and formal. Every explanation
must be robust enough for a Master's Thesis or research meeting.

---

## Repository structure
- data/              → all raw and processed CSV datasets
- benchmark_notebooks/         → one independent Jupyter notebook per phase (dataset A)
- benchmark_latex/             → LaTeX analysis documents, one per phase (dataset A)
- benchmark_latex/figures/     → all saved matplotlib figures (.png, dpi=150) (dataset A)
- reference_nb/                → one independent Jupyter notebook per phase (dataset B)
- reference_tex/               → LaTeX analysis documents, one per phase (dataset B)
- reference_tex/plots/         → all plots and figures (dataset B)
- papers/            → general reference PDFs
- reports/           → ROADMAP.docx and council_specs.md

---

## DATASET STATUS — TWO DATASETS IN USE

### Dataset A — Proxy dataset (Phases 1–4, completed)
Source: OpenWeatherMap API via GitHub (beniamino98/greenfin-project)
File: data/data.csv
Location: Bologna area, Italy (~lat 44.29, lon 11.2)
Height: 10m anemometer
Period: 2015–2018 (4 years, 1,462 daily observations)
Status: Phases 1–4 COMPLETE. Notebooks validated.
Note: Low wind site (mean 2.47 m/s), lognormal distribution,
      proxy-quality data. Used to build and validate methodology.

### Dataset B — Reference dataset (to be used for all the phases)
Source: Open-Meteo Historical Weather API, ERA5 reanalysis model
File: data/germany_wind.csv
Location: Nordfriesland, Schleswig-Holstein, Germany (lat=54.77, lon=8.85)
Height: 100m (direct ERA5 variable — no profile correction needed)
Period: 2015–2024 (10 full calendar years, ~3,652 daily values)
Variables: wind_speed_100m (primary), wind_speed_10m (validation),
           wind_direction_100m (regime analysis)
Motivation: Germany's highest-density onshore wind corridor.
            EEX German Power options are the most liquid in Europe.
            Centroid of Nordfriesland district, representative of
            the SMARD aggregate national wind generation fleet.

### SMARD electricity data (Phases 6–7)
Source: Bundesnetzagentur SMARD platform (smard.de/en)
Files:
  data/Actual_generation_1.csv   → wind onshore generation, hourly (batch 1)
  data/Actual_generation_2.csv   → wind onshore generation, hourly (batch 2)
  data/Day_ahead_prices_1.csv    → German day-ahead electricity price €/MWh (batch 1)
  data/Day_ahead_prices_2.csv    → German day-ahead electricity price €/MWh (batch 2)
Coverage: 2015–2024, hourly resolution, Country: Germany
Price note: Day_ahead_prices files contain two columns:
  - Column C  (Germany/Luxembourg [€/MWh]): valid from 01/10/2018 onwards
  - Column O  (DE/AT/LU [€/MWh]):           valid until 30/09/2018
  Merge rule: price = Column C where available, else Column O
  This reflects the DE/AT market split on 1 October 2018.

---

## Phase completion status
- Phase 1 (Box-Cox, seasonal decomposition): COMPLETE on Dataset A
- Phase 2 (AR/ARMA/ARFIMA, AR(4)):           COMPLETE on Dataset A
- Phase 3 (GARCH(1,1)):                      COMPLETE on Dataset A
- Phase 4 (CAR(4) embedding):                COMPLETE on Dataset A
- Phase 1–4 re-run on Dataset B:             NEXT — must be done first
- Phase 5 (Stochastic Volatility):           PENDING
- Phase 6 (Derivatives pricing — Carr-Lee):  PENDING
- Phase 7 (Validation):                      PENDING

---

## Numerical anchors from Dataset A (Phases 1–4)
NEVER re-estimate these values — they are fixed results of completed work:
Box-Cox λ = 0.1 | AR(4): β₁=0.6299, β₂=−0.1443, β₃=0.0926, β₄=0.0310
CAR(4): α₁=3.3701, α₂=4.2546, α₃=2.3063, α₄=0.3908
GARCH: ω=0.01004, α=0.0311, β=0.9524, α+β=0.9835
Eigenvalues of A: {−1.200, −0.936±0.465i, −0.298}
h(t₀)=0.4137 | σ²_total(t₀)=0.0926 | NIG: α̂=4.374, δ̂=2.087
RMSE: Persistence=0.6856, AR(4)=0.6017 (12.2% improvement)
Dataset B will produce its own numerical anchors — document separately.

---

## Phase 5 — scope rules (CRITICAL)
THEORETICAL CHAPTER ONLY on both datasets.
Do NOT attempt Heston MLE — identification fails at any sample available.
Deliverables: two-SDE system, Feller condition 2κ_V v̄ > ξ²,
semi-analytic futures price via characteristic function.
Defer empirical estimation to ECMWF Phase 2 data.

---

## Phase 6 — Carr-Lee pricing framework
Core reference: Carr & Lee (2009), "Volatility Derivatives",
Annual Review of Financial Economics 1:1–21. PDF in papers/.

Synthetic instrument: wind electricity variance swap on German
onshore wind production, with:
  Floating leg = realised variance of daily wind production returns
                 computed from Actual_generation_1/2.csv (SMARD)
  Fair strike  = Track A (historical mean RV, actuarial, no θ needed)
                 Track B (Carr-Lee Section 4 or Section 6 ATM approx,
                 requires EEX options if available)

Power curve: P(v) ∝ v³ at 100m — no profile correction.
Piecewise-constant h(t₀) for all closed-form pricing (Tol 1997).
Three h scenarios per price (fan, not single number):
  h = 0.414 (end-of-sample) | ≈0.52 winter / ≈0.76 summer | h = 1.0
Synthetic producer: Nordfriesland, Schleswig-Holstein, Germany.
Do NOT calibrate θ to real Nordix prices (geographic mismatch).

---

## Phase 7 — Validation rules
Value of Information: Dataset A (10m proxy, 4yr Bologna) vs
Dataset B (100m ERA5, 10yr Nordfriesland).
Do NOT apply 1.74× height correction to Dataset B (already at 100m).
Apply correction only when directly comparing Dataset A vs B output.
Model hierarchy for DM tests:
  Persistence → AR(4) → AR(4)+GARCH → CAR(4)+Gaussian → CAR(4)+NIG
Hedging simulation on synthetic CAWS index, left-tail focus.
Vertical wind profile correction before any RMSE comparison with
ECMWF hub-height data (future Phase 2 data upgrade).

---

## Citation rules — available papers (all PDFs in papers/)

## Code standards
Python: pandas, numpy, scipy, statsmodels, arch, matplotlib.
Each notebook fully independent — loads CSVs, no cross-notebook imports.
File I/O uses relative paths from the notebook's location:
  Dataset A wind:          ../data/data.csv
  Dataset B wind:          ../data/germany_wind.csv
  SMARD generation (raw):  ../data/Actual_generation_1.csv
                           ../data/Actual_generation_2.csv
  SMARD prices (raw):      ../data/Day_ahead_prices_1.csv
                           ../data/Day_ahead_prices_2.csv
No magic numbers — use named constants at top of each cell.
Figures: PNG, dpi=150, saved to ../latex/figures/.
LaTeX style: match Phases 1–4 exactly (graybox, litbox, darkblue/midblue).