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
  data/Day-ahead_prices_1.csv    → German day-ahead electricity price €/MWh (batch 1)
  data/Day-ahead_prices_2.csv    → German day-ahead electricity price €/MWh (batch 2)
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

## Phase 5 — Stochastic Volatility: Theoretical Chapter

### Scope rules (CRITICAL)
THEORETICAL CHAPTER ONLY. Do NOT attempt Heston MLE — identification
fails at daily frequency. One empirical computation is allowed: substitute
GARCH proxy values into the Feller condition to check it numerically.
Defer all SV estimation to ECMWF hourly data (future upgrade).

### Inputs consumed (reference_nb paths)
  ../Phase_3/garch_parameters_phase3.csv   → ω, α, β for Feller proxy
  ../Phase_4/car4_parameters_phase4.csv    → α₁ for OU comparison
  ../Phase_4/car4_conditional_forecast_phase4.csv → benchmark for term structure plot

### Notebook structure (reference_nb/Phase_5/Phase_5.ipynb)

  ## 1. MOTIVATION
  Why GARCH h(t) is insufficient for continuous-time pricing: discrete filter
  cannot be embedded in Feynman-Kač. Need V(t) as a semimartingale.

  ## 2. TWO-SDE SYSTEM UNDER P
  Wind speed (CAR(4) residual layer):
    dW̃(t) = −α₁ W̃(t) dt + √V(t) dB₁(t)   [simplified to CAR(1) for tractability]
  Variance process (CIR / square-root):
    dV(t) = κ_V(v̄ − V(t)) dt + ξ √V(t) dB₂(t)
    ⟨dB₁, dB₂⟩_t = ρ dt
  State vector form for full CAR(4): replace scalar α₁ with matrix A.
  Reference: Heston (1993), Schwartz (1997).

  ## 3. FELLER CONDITION
  Condition: 2 κ_V v̄ > ξ²  (ensures V(t) > 0 a.s.)
  GARCH proxy mapping:
    v̄  ≈ ω / (1 − α − β)         (unconditional variance)
    κ_V ≈ 1 − (α + β)             (mean-reversion speed)
    ξ²  ≈ Var of h(t) series      (proxy: rolling std of h_series)
  Load garch_parameters_phase3.csv and h_series_phase3.csv.
  Compute 2κ_V v̄ and ξ² numerically. State whether Feller holds.

  ## 4. CHANGE OF MEASURE (P → Q)
  Girsanov: add wind-speed risk premium θ₁ and variance risk premium θ₂.
  Under Q:  κ_V^Q = κ_V + θ₂ξ,   v̄^Q = κ_V v̄ / (κ_V + θ₂ξ)
  Working assumption: θ₁ = θ₂ = 0  (no calibration, geographic mismatch
  with Nordix per CLAUDE.md). P = Q for all computations below.

  ## 5. CHARACTERISTIC FUNCTION
  Log-char. function of W̃(T) given F_t (Heston 1993):
    log φ(u; t,T) = iu·E + C(τ; u)·v̄ + D(τ; u)·V(t),   τ = T − t
  where C, D satisfy Riccati ODEs. Write the ODEs and their closed-form
  solution. Note: under θ = 0 the first moment recovers e₁′·exp(Aτ)·X(t).

  ## 6. SEMI-ANALYTIC WIND FUTURES PRICE
  F(t, T) = Λ(T) + σ_seasonal(T) · E^Q[W̃(T) | F_t]
  E^Q[W̃(T)|F_t] = Im[φ(−i; t,T)] / 1   (first moment from char. fn.)
  Under θ = 0: this equals e₁′·exp(Aτ)·X(t) — verify round-trip
  consistency against Phase 4 conditional forecast table.

  ## 7. VARIANCE TERM STRUCTURE
  Var^Q[W̃(T)|F_t] as function of horizon τ. Compare:
    (a) Piecewise-constant h approximation (Phase 4, Tol 1997):
        Var = h(t₀)·σ²_seasonal(t₀) · ∫₀^τ g(s)² ds
    (b) Full Heston term structure (numerical integration of Riccati)
  Plot both. Save figure: phase5_B_sv_termstructure.png

  ## 8. WHY MLE IS DEFERRED
  Identification of (κ_V, v̄, ξ, ρ): requires intraday data (≥5-min) or
  N >> 3,000 daily obs. At daily frequency, ξ/κ_V ratio is not separately
  identified from the GARCH persistence α+β. Reference: ECMWF ERA5
  hourly data as the designated upgrade path.

  ## 9. SUMMARY
  Formal SDE system, parameter correspondence table (GARCH proxy ↔ Heston),
  Feller condition numerical result, round-trip consistency check.

### Outputs
  Figures only — no CSV outputs.
  phase5_B_sv_termstructure.png  → reference_tex/Plots/

---

## Phase 6 — Carr-Lee Variance Swap Pricing

### Scope and core reference
Carr & Lee (2009), "Volatility Derivatives",
Annual Review of Financial Economics 1:1–21. PDF in papers/.
Synthetic instrument: wind electricity variance swap on German
onshore wind production. θ = 0 throughout (no calibration to Nordix).

### Hard constraints
- Do NOT calibrate θ to real Nordix prices (geographic mismatch).
- Piecewise-constant h(t₀) for all closed-form pricing (Tol 1997).
- Three h scenarios per price (fan): h_t0 (Dataset B end-of-sample),
  h_winter (DJF mean from Phase 3), h_summer (JJA mean from Phase 3),
  h = 1.0 (Gaussian baseline). Do NOT hardcode Dataset A values (0.414).
- Power curve: P(v) ∝ v³ at 100m — no profile correction.
- SMARD file names use a HYPHEN: Day-ahead_prices_1.csv (not underscore).

### Key formula (Track B under piecewise-constant h, Tol 1997)
  K_var^B(h) = h × (1/T) ∫₀ᵀ σ²_seasonal(t) dt
             = h × C0
  because C1 and C2 Fourier terms integrate to zero over a full year.
  C0 loaded from car4_parameters_phase4.csv.

### Inputs consumed (reference_nb paths)
  ../Phase_3/garch_h_series_phase3.csv       → h_winter, h_summer, h_t0
  ../Phase_4/car4_parameters_phase4.csv      → h_t0, C0, NIG parameters
  ../../data/Actual_generation_1.csv         → SMARD wind onshore MWh
  ../../data/Actual_generation_2.csv
  ../../data/Day-ahead_prices_1.csv          → German day-ahead €/MWh
  ../../data/Day-ahead_prices_2.csv

### SMARD parsing notes
  Separator: semicolon. Encoding: UTF-8 BOM. Numbers: comma as thousands sep.
  Dates: "Jan 1, 2015 12:00 AM" → parse with pd.to_datetime(dayfirst=False).
  Wind onshore column: "Wind onshore [MWh] Calculated resolutions"
  Price column merge rule: Germany/Luxembourg post 01/10/2018,
                           DE/AT/LU pre 01/10/2018 (see SMARD section above).
  Daily aggregation: generation = sum of 24 hourly values per day.
                     price      = mean of 24 hourly values per day.
  Drop days with >20% missing hourly observations.

### Notebook structure (reference_nb/Phase_6/Phase_6.ipynb)

  ## 1. CONFIGURATION AND DATA LOADING
  Load all six input files. Print shape and date range of each.

  ## 2. SMARD WIND GENERATION — DAILY SERIES
  Parse, clean, resample hourly → daily. Handle zeros and missing.
  Print: total obs, NaN count, mean/std of G_t [MWh/day].

  ## 3. DAY-AHEAD ELECTRICITY PRICES — DAILY SERIES
  Parse + merge DE/AT/LU | Germany/Luxembourg per the split date.
  Align date index with generation series.
  Plot: G_t and P_t time series (dual-axis). Save: phase6_B_smard_overview.png

  ## 4. LOG-RETURNS AND REALISED VARIANCE (TRACK A)
  r_t = log(G_t / G_{t-1}), set r_t = NaN if G_t = 0 or G_{t-1} = 0.
  Daily RV: RV_t = r_t²
  Rolling annual fair strike: K_var^A(t) = 252 × mean(RV, 252-day window)
  Seasonal analysis: print DJF / MAM / JJA / SON means.
  Plot: RV_t + rolling K_var^A. Save: phase6_B_rv_tracka.png

  ## 5. MODEL-IMPLIED FAIR STRIKE (TRACK B)
  Load h_series → compute h_winter (DJF mean), h_summer (JJA mean).
  h_t0 from car4_parameters_phase4.csv.
  Four h scenarios: h_t0, h_winter, h_summer, h=1.0.
  K_var^B(h) = h × C0 for each scenario.
  Note: θ = 0 → risk-neutral NIG adjustment equals 1; Track B reduces
  to the piecewise-constant Tol (1997) formula.
  Print comparison table: K_var^A vs K_var^B for each scenario.

  ## 6. POWER CURVE LINKAGE
  Conceptual: G(t) ∝ W(t)³ (Betz law, 100m hub height).
  Delta-method: Var[G] ≈ (3c W̄²)² × Var[W].
  Empirical: scatter G_t vs W_t³ (load germany_wind.csv), fit OLS,
  report R² and proportionality constant. Motivates why CAR(4) wind
  speed variance drives generation variance.

  ## 7. MERIT-ORDER EFFECT
  Scatter: daily G_t vs P_t. Rolling 90-day Pearson ρ(G, P).
  Expected: negative correlation (high wind → low price).
  Interpretation: wind variance swap as natural hedge for producers.

  ## 8. SYNTHETIC VARIANCE SWAP — PAYOFF SIMULATION
  For each year t in sample:
    RV^annual_t = 252 × mean(RV_t over that calendar year)
    Payoff_A = RV^annual_t − K_var^A
    Payoff_B(h) = RV^annual_t − K_var^B(h)  for each h scenario
  Distribution: histogram of payoffs. Left-tail (5th pct) = worst-case producer.
  Fan chart: K_var^B fan vs time series of annual RV.
  Save: phase6_B_payoff_fan.png

  ## 9. COMPLETE SUMMARY TABLE
  Rows: Dataset A (Track A only) | Dataset B (Track A + Track B × 4 scenarios)
  Columns: K_var, payoff mean, payoff std, VaR₅%.
  Print and save as variance_swap_summary_phase6.csv.

  ## 10. SAVE OUTPUTS
  variance_swap_phase6.csv:
    columns: date, G_daily_MWh, price_EUR_MWh, log_return_G,
             RV_daily, RV_rolling252, K_varA,
             K_varB_ht0, K_varB_hwinter, K_varB_hsummer, K_varB_h1
  smard_daily_phase6.csv:
    columns: date, G_daily_MWh, price_EUR_MWh
  variance_swap_summary_phase6.csv: summary table above.

### Outputs — figures (reference_tex/Plots/, prefix phase6_B_)
  phase6_B_smard_overview.png       → G_t and P_t dual-axis time series
  phase6_B_rv_tracka.png            → daily RV + rolling K_var^A
  phase6_B_power_curve.png          → scatter G_t vs W_t³ + OLS fit
  phase6_B_merit_order.png          → scatter G_t vs P_t + rolling ρ
  phase6_B_payoff_fan.png           → K_var^B fan + annual RV time series

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
  SMARD prices (raw):      ../data/Day-ahead_prices_1.csv
                           ../data/Day-ahead_prices_2.csv
No magic numbers — use named constants at top of each cell.
Figures: PNG, dpi=150.
  benchmark_notebooks: FIG_PATH = "../../benchmark_latex/figures/"
  reference_nb:        FIG_PATH = "../../reference_tex/Plots/"
LaTeX style: match Phases 1–4 exactly (graybox, litbox, darkblue/midblue).