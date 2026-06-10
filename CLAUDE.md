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

### Cross-dataset comparison table
Required in Phase 7 §1 (both notebooks) and in the Phase 7 LaTeX intro. Columns:
  Source | Location | Height | Period | Primary variables | Advantages | Limitations
Rows:
  Dataset A — OpenWeatherMap API (beniamino98/greenfin-project) | Bologna, Italy
              10 m | 2015–2018 (4 yr, 1,462 obs) | wind_speed (m/s)
              Advantages: freely available, easy to download, sufficient for methodology
              Limitations: proxy-quality, 10 m height, low-wind site, 4-year window only
  Dataset B — Open-Meteo ERA5 reanalysis | Nordfriesland, Germany (lat=54.77, lon=8.85)
              100 m | 2015–2024 (10 yr, 3,652 obs) | wind_speed_100m, wind_speed_10m,
              wind_direction_100m
              Advantages: hub-height, geographically aligned with SMARD generation,
              10-year window enables OOS validation, ERA5 reanalysis quality
              Limitations: model-based (not anemometer), no sub-daily resolution here
Purpose: motivates the A→B upgrade; provides reference for future studies.

---

## Phase completion status
- Phase 1–4 Dataset A:              COMPLETE. Notebooks validated.
- Phase 5 Dataset A (SV theory):    COMPLETE. benchmark_notebooks/phase5/PHASE_5.ipynb
- Phase 1–4 Dataset B:              COMPLETE. Notebooks validated.
- Phase 5 Dataset B (SV theory):    COMPLETE. reference_nb/Phase_5/Phase_5.ipynb
- Phase 6 Dataset A (Carr-Lee):     PENDING
- Phase 6 Dataset B (Carr-Lee):     PENDING
- Phase 7 Dataset A (Validation):   PENDING
- Phase 7 Dataset B (Validation):   PENDING

---

## Numerical anchors from Dataset A (Phases 1–4)
NEVER re-estimate these values — they are fixed results of completed work:
Box-Cox λ = 0.1 | AR(4): β₁=0.6299, β₂=−0.1443, β₃=0.0926, β₄=0.0310
CAR(4): α₁=3.3701, α₂=4.2546, α₃=2.3063, α₄=0.3908
GARCH: ω=0.01004, α=0.0311, β=0.9524, α+β=0.9835
Eigenvalues of A: {−1.200, −0.936±0.465i, −0.298}
h(t₀)=0.4137 | σ²_total(t₀)=0.0926 | NIG: α̂=4.374, δ̂=2.087
RMSE: Persistence=0.6856, AR(4)=0.6017 (12.2% improvement)
Phase 5 (Dataset A): Feller margin=27.9× (HOLDS) | Round-trip max|Δ|=4.44e-16 (PASS)
CIR proxy (Dataset A): κ_V=0.0165, v̄=0.608, ξ²≈0.000048
Dataset B will produce its own numerical anchors — document separately.

---

## Phase 5 — Stochastic Volatility: Theoretical Chapter (both datasets)

Inputs and output paths differ by dataset; section structure is identical.
See Dataset A sub-block below for benchmark_notebooks paths and figure prefix.

### Scope rules (CRITICAL)
THEORETICAL CHAPTER ONLY. Do NOT attempt Heston MLE — identification
fails at daily frequency. One empirical computation is allowed: substitute
GARCH proxy values into the Feller condition to check it numerically.
Defer all SV estimation to ECMWF hourly data (future upgrade).

### Inputs consumed — Dataset B (reference_nb paths)
  ../Phase_3/garch_parameters_phase3.csv   → ω, α, β for Feller proxy
  ../Phase_4/car4_parameters_phase4.csv    → α₁, h_t0, C0, sigma2_seas_t0
  ../Phase_4/car4_conditional_forecast_phase4.csv → benchmark for term structure plot

### Inputs consumed — Dataset A (benchmark_notebooks paths)
  ../phase3/garch_parameters_phase3.csv    → ω, α, β for Feller proxy
  ../phase3/garch_h_series_phase3.csv      → h_t series (h_t0 = h_series.iloc[-1])
  ../phase4/car4_parameters_phase4.csv     → α₁–α₄ only (C0 hardcoded: 0.13138)
  ../phase4/car4_conditional_forecast_phase4.csv → benchmark for term structure plot
  ../phase1/W_tilde_phase1.csv             → last 4 obs for state vector X(t₀)

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

  ## 4. REMARK ON CHANGE OF MEASURE
  Formally, Girsanov would shift drifts: κ_V^Q = κ_V + θ₂ξ.
  In this application θ₁ = θ₂ = 0 is adopted as a benchmark assumption:
    (a) SMARD G_t is a physical quantity, not a traded asset — no clean
        replication argument pins down the equilibrium market price of risk.
    (b) The only liquid traded wind derivatives (Nordix futures) cover
        Norway, not Nordfriesland — geographic mismatch prevents
        calibration of θ even if option data were accessible.
  Framing rule (IMPORTANT): present θ = 0 explicitly as a reasonable
  benchmark assumption that makes Track B analytically tractable in the
  absence of option-implied data — NOT as the standard or equilibrium MPR.
  Acknowledge this simplification clearly in both notebooks and LaTeX.
  Do NOT write "θ = 0 is standard" or "the market price of risk is zero";
  write "we adopt θ = 0 as a benchmark in the absence of EEX option data".
  Future extension (designated upgrade path): option-implied calibration
  of θ via EEX German Power options and the full Carr-Lee (2009) replication
  framework. Present this as a possible extension, not a necessary component.
  Consequence under θ = 0: P = Q. Track B reduces to h × C0.
  No Girsanov computation is needed in Phases 5 or 6 under this benchmark.

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

### Outputs — Dataset B
  Figures only — no CSV outputs.
  phase5_B_sv_termstructure.png  → reference_tex/Plots/

### Outputs — Dataset A
  Figures only — no CSV outputs.
  phase5_A_sv_termstructure.png  → benchmark_latex/figures/

---

## Phase 6 Dataset B — Carr-Lee Variance Swap Pricing (reference_nb)

### Scope and core reference
Carr & Lee (2009), "Volatility Derivatives",
Annual Review of Financial Economics 1:1–21. PDF in papers/.
Synthetic instrument: wind electricity variance swap on German
onshore wind production. θ = 0 adopted as benchmark assumption
(see Phase 5 §4 framing rule): present explicitly as a tractable
simplification in the absence of EEX option-implied data, not as
the equilibrium market price of risk. Option-implied calibration
via EEX German Power options is the designated future extension.

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
  Definitions (print at top of section):
    Track A = 252 × rolling-mean(r_t²), SMARD generation log-returns.
    Track B = h × C0, GARCH conditional variance × Fourier integral.
    These measure variance of different processes; reconcile before comparison.

  Static scenarios: load h_series. Compute h_winter (DJF mean), h_summer (JJA mean).
  h_t0 from car4_parameters_phase4.csv. Four scenarios: h_t0, h_winter, h_summer, h=1.0.
  K_var^B(h) = h × C0 for each scenario.
  Note: θ = 0 → Track B reduces to the piecewise-constant Tol (1997) formula.
  Print comparison table: K_var^A vs K_var^B for each scenario.

  Unit reconciliation via delta-method (G = c × W³ power curve):
    K_var^B_scaled = (9 / W̄²) × h × C0
    W̄ = mean(wind_speed_100m) daily from germany_wind.csv (full 2015–2024 sample).
    c from OLS slope (Section 6) provides empirical check on the 9/W̄² factor.
    Compute K_var^B_scaled for each h scenario alongside raw K_var^B and K_var^A.

  Time-varying Track B (rolling series):
    K_varB_rolling_t = h_t × C0   (element-wise over full h_series)
    Align with K_var^A rolling series. Compute RMSE(K_varA_rolling, K_varB_scaled_rolling).
    This is the empirical distance between the two tracks — the core VoI metric.

  Out-of-sample validation (Dataset B only — 10-year window allows this):
    In-sample: 2015–2019 (use h_t0 and C0 calibrated on this window only).
    Out-of-sample: 2020–2024 (hold-out).
    Evaluate K_var^B_OOS prediction vs K_var^A on 2020–2024. Report OOS RMSE.
    Interpretation: does the model have genuine predictive content beyond in-sample fit?

  ## 6. POWER CURVE LINKAGE
  Conceptual: G(t) ∝ W(t)³ (Betz law, 100m hub height).
  Delta-method: Var[log G] ≈ (9/W̄²) × Var[W̃].
  Empirical: scatter G_t vs W_t³ (load germany_wind.csv, wind_speed_100m column),
  fit OLS, report R² and proportionality constant c.
  W̄ used in Section 5 unit reconciliation = mean(wind_speed_100m daily).
  Motivates why CAR(4) wind speed variance drives generation variance.

  ## 7. MERIT-ORDER EFFECT
  Scatter: daily G_t vs P_t. Rolling 90-day Pearson ρ(G, P).
  Expected: negative correlation (high wind → low price).
  Sub-period robustness check:
    rho_pre  = corr(G, P) for dates < 2018-10-01
    rho_post = corr(G, P) for dates >= 2018-10-01
    Print both. Confirms structural break (DE/AT price split) does not materially
    distort the merit-order finding.
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
             K_varB_ht0, K_varB_hwinter, K_varB_hsummer, K_varB_h1,
             K_varB_rolling,   ← time-varying: h_t × C0 aligned to daily dates
             K_varB_scaled_ht0 ← unit-reconciled: (9/W̄²) × h_t0 × C0
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

## Phase 6 Dataset A — Carr-Lee Variance Swap Pricing (benchmark_notebooks)

### Scope
Same synthetic instrument as Dataset B: wind electricity variance swap on German
onshore wind production (SMARD G_t). Track A (empirical) is identical — it derives
from the market, not the wind model. Track B uses Dataset A GARCH/Fourier parameters
(Bologna proxy), making K_var^B much lower than Dataset B and demonstrating the
Value of Information argument developed in Phase 7.
θ = 0 adopted as benchmark assumption (P = Q). Present explicitly as a
tractable simplification in the absence of option-implied data, not as
the equilibrium MPR. See Phase 5 §4 framing rule. Option-implied
calibration via EEX German Power options is the designated future extension.

### Hard constraints
- C0 = 0.13138  (Phase 1 Fourier constant, Dataset A — DO NOT re-estimate, DO NOT
  use Dataset B value 1.163). Hardcode as a named constant; do NOT load from CSV.
- h_t0 = h_series.iloc[-1]  (end of Dataset A: 31 Dec 2018).
  DO NOT hardcode the value 0.4137 — always derive from the loaded h_series.
- Four h scenarios: h_t0, h_winter (DJF mean of h_series), h_summer (JJA mean),
  h = 1.0 (Gaussian baseline). All derived from benchmark Phase 3 h_series.
- Power curve: use data.csv 'wind_speed' column (Bologna 10 m) vs SMARD G_t.
  Geographic mismatch is intentional. Caption must flag it explicitly.
  No height profile correction in the power curve scatter itself.
- SMARD file names use a HYPHEN: Day-ahead_prices_1.csv (not underscore).

### Key formulas
  K_var^B(h) = h × C0,  C0 = 0.13138
  Track A:  r_t = log(G_t / G_{t-1}),  RV_t = r_t²
  K_var^A(t) = 252 × mean(RV, 252-day rolling window)

### Inputs consumed (relative paths from benchmark_notebooks/phase6/)
  ../phase3/garch_h_series_phase3.csv     → h_t0, h_winter, h_summer
  ../phase4/car4_parameters_phase4.csv    → alpha1 only (C0 is a hardcoded constant)
  ../../data/Actual_generation_1.csv      → SMARD wind onshore MWh
  ../../data/Actual_generation_2.csv
  ../../data/Day-ahead_prices_1.csv       → German day-ahead €/MWh
  ../../data/Day-ahead_prices_2.csv
  ../../data/data.csv                     → Bologna wind speed (power curve section only)
    data.csv wind column:  'wind_speed'  (m/s, hourly)
    data.csv date column:  'date'        (ISO 8601 e.g. "2015-01-01T00:00:00Z")
    Resample hourly → daily mean.
    Valid range: 2015-01-01 to 2018-12-31 (4 years, ~1 462 daily values after resampling).
    Align with SMARD G_t on the overlapping 2015-2018 window for power curve scatter.

### SMARD parsing notes (identical to Dataset B)
  Separator: semicolon. Encoding: UTF-8 BOM. Numbers: comma as thousands separator.
  Dates: "Jan 1, 2015 12:00 AM" → parse with pd.to_datetime(dayfirst=False).
  Wind onshore column: "Wind onshore [MWh] Calculated resolutions"
  Price merge rule: Germany/Luxembourg [€/MWh] from 01/10/2018, DE/AT/LU before.
  Daily aggregation: generation = sum of 24 hourly values per day.
                     price      = mean of 24 hourly values per day.
  Drop days with >20% missing hourly observations.

### Notebook structure (benchmark_notebooks/phase6/PHASE_6.ipynb)

  ## 1. CONFIGURATION AND DATA LOADING
  Named constants at top: C0 = 0.13138.
  Load all seven input files. Print shape and date range of each.

  ## 2. SMARD WIND GENERATION — DAILY SERIES
  Parse, clean, resample hourly → daily. Handle zeros and missing.
  Print: total obs, NaN count, mean/std of G_t [MWh/day].

  ## 3. DAY-AHEAD ELECTRICITY PRICES — DAILY SERIES
  Parse + merge DE/AT/LU | Germany/Luxembourg per the split date.
  Align date index with generation series.
  Plot G_t and P_t dual-axis. Save: phase6_A_smard_overview.png

  ## 4. LOG-RETURNS AND REALISED VARIANCE (TRACK A)
  r_t = log(G_t / G_{t-1}), NaN if G_t = 0 or G_{t-1} = 0.
  RV_t = r_t².  K_var^A(t) = 252 × mean(RV, 252-day window).
  Seasonal analysis: DJF / MAM / JJA / SON means.
  Plot RV_t + rolling K_var^A. Save: phase6_A_rv_tracka.png

  ## 5. MODEL-IMPLIED FAIR STRIKE (TRACK B)
  Definitions (print at top of section):
    Track A = 252 × rolling-mean(r_t²), where r_t = log(G_t/G_{t-1}), SMARD generation.
    Track B = h × C0, where h is GARCH conditional variance, C0 is Fourier integral of σ²_seas.
    These measure variance of different processes and must be reconciled before comparison.

  Static scenarios: load h_series. Compute h_winter (DJF), h_summer (JJA),
  h_t0 = h_series.iloc[-1]. K_var^B(h) = h × C0 for each of the four scenarios.
  Print table: K_var^A (full-period mean) vs K_var^B for each scenario.

  Unit reconciliation via delta-method (G = c × W³ power curve):
    Var[log(G_t/G_{t-1})] ≈ (9/W̄²) × Var[W̃]
    K_var^B_scaled = (9 / W̄²) × h × C0
    W̄ = daily mean wind speed from data.csv (Bologna, 2015–2018, resampled to daily mean)
    c from OLS slope of Phase 6 Section 6 provides empirical check on the 9/W̄² scaling.
    Compute K_var^B_scaled for each h scenario and print alongside K_var^B and K_var^A.

  Time-varying Track B (rolling series):
    K_varB_rolling_t = h_t × C0   (element-wise over full h_series)
    Align K_varB_rolling with K_var^A rolling series on common dates.
    Compute: RMSE_A = RMSE(K_varA_rolling, K_varB_scaled_rolling) over overlapping window.
    Print RMSE_A — this is the empirical distance between the two tracks.

  Note: K_var^B Dataset A ≈ 0.054 vs Dataset B ≈ 0.87 — factor ~16× difference
  driven by C0_A = 0.131 << C0_B = 1.163. This is the VoI gap quantified in Phase 7.
  The RMSE between tracks answers whether the model is close to the market,
  not just whether the point estimates differ.

  ## 6. POWER CURVE LINKAGE
  Load data.csv. Resample 'wind_speed' hourly → daily mean. Restrict to 2015-2018.
  Align date index with SMARD G_t on the same 2015-2018 window.
  Scatter: SMARD G_t vs (daily_wind_bologna_ms)³.
  Fit OLS, report R² and proportionality constant.
  Expected: weak R² (geographic mismatch: Bologna 10 m ≠ German hub-height wind).
  Plot title must include: "Cross-geographic proxy — Bologna 10 m vs SMARD Germany".
  This plot motivates why Dataset B (geographically aligned) is required for pricing.
  Save: phase6_A_power_curve.png

  ## 7. MERIT-ORDER EFFECT
  Scatter daily G_t vs P_t. Rolling 90-day Pearson ρ(G, P).
  Expected: negative correlation (same market data as Dataset B, same result).
  Sub-period robustness check (October 2018 DE/AT price split):
    rho_pre  = corr(G, P) for dates < 2018-10-01
    rho_post = corr(G, P) for dates >= 2018-10-01
    Print both. If similar, the structural break has no material effect on the merit-order finding.
  Save: phase6_A_merit_order.png

  ## 8. SYNTHETIC VARIANCE SWAP — PAYOFF SIMULATION
  For each calendar year in 2015-2024:
    RV^annual = 252 × mean(RV_t over that year)
    Payoff_A  = RV^annual − K_var^A
    Payoff_B(h) = RV^annual − K_var^B(h)  for each h scenario
  Histogram of payoffs. Left-tail 5th percentile = worst-case producer.
  Fan chart: K_var^B fan vs annual RV time series.
  Save: phase6_A_payoff_fan.png

  ## 9. COMPLETE SUMMARY TABLE
  Rows: Dataset A Track A | Dataset A Track B × 4 h scenarios.
  (Cross-dataset K_var^B comparison deferred to Phase 7.)
  Columns: K_var, payoff mean, payoff std, VaR₅%.
  Save: variance_swap_summary_phase6.csv

  ## 10. SAVE OUTPUTS
  variance_swap_phase6.csv:
    columns: date, G_daily_MWh, price_EUR_MWh, log_return_G,
             RV_daily, RV_rolling252, K_varA,
             K_varB_ht0, K_varB_hwinter, K_varB_hsummer, K_varB_h1,
             K_varB_rolling,   ← time-varying: h_t × C0 aligned to daily dates
             K_varB_scaled_ht0 ← unit-reconciled: (9/W̄²) × h_t0 × C0
  smard_daily_phase6.csv:
    columns: date, G_daily_MWh, price_EUR_MWh
  variance_swap_summary_phase6.csv: summary table above.

### Outputs — figures (benchmark_latex/figures/, prefix phase6_A_)
  phase6_A_smard_overview.png       → G_t and P_t dual-axis time series
  phase6_A_rv_tracka.png            → daily RV + rolling K_var^A
  phase6_A_power_curve.png          → scatter G_t vs W_t³ + OLS fit (mismatch)
  phase6_A_merit_order.png          → scatter G_t vs P_t + rolling ρ
  phase6_A_payoff_fan.png           → K_var^B fan + annual RV time series

### Outputs — CSVs (benchmark_notebooks/phase6/)
  variance_swap_phase6.csv
  smard_daily_phase6.csv
  variance_swap_summary_phase6.csv

---

## Phase 7 — Validation: Value of Information & Model Hierarchy

### Scope
Cross-dataset validation: Dataset A (proxy, Bologna 10 m, 4 yr) vs
Dataset B (reference, Nordfriesland 100 m, 10 yr). Requires Phase 6 complete
for both datasets before implementation.

Do NOT apply 1.74× height correction to Dataset B (already at 100 m).
Apply the power-law profile correction ONLY in Section 4 (VoI comparison) when
directly placing A and B K_var^B values on the same axis.
Height correction factor: (100/10)^(2α), α = 1/7 (Hellmann exponent).
→ variance scales as (h₂/h₁)^(2/7): factor ≈ 1.741 on variance.
Vertical wind profile correction before any RMSE comparison with
ECMWF hub-height data (future Phase 2 data upgrade).

### Notebook paths
  benchmark_notebooks/phase7/PHASE_7.ipynb   → Dataset A DM tests + VoI
  reference_nb/Phase_7/Phase_7.ipynb          → Dataset B DM tests + full synthesis

### Inputs consumed (benchmark_notebooks/phase7/, relative paths)
  ../phase1/W_tilde_phase1.csv                    → de-seasonalised wind for DM tests
  ../phase2/ar4_residuals_phase2.csv              → AR(4) residuals
  ../phase3/garch_h_series_phase3.csv             → GARCH h(t) for AR(4)+GARCH forecasts
  ../phase3/garch_z_series_phase3.csv             → standardised residuals for diagnostics
  ../phase4/car4_conditional_forecast_phase4.csv  → CAR(4) multi-step forecasts
  ../phase6/variance_swap_phase6.csv              → Track A and Track B series
  ../phase6/variance_swap_summary_phase6.csv      → fair-strike summary
  ../../data/data.csv                             → wind_speed column (CAWS construction)
  NIG parameters hardcoded from Phase 4 anchors (NEVER re-estimate):
    alpha_NIG = 4.374, delta_NIG = 2.087  (Dataset A)

### Inputs consumed (reference_nb/Phase_7/, relative paths)
  ../Phase_1/W_tilde_phase1.csv
  ../Phase_2/ar4_residuals_phase2.csv
  ../Phase_3/garch_h_series_phase3.csv
  ../Phase_3/garch_z_series_phase3.csv
  ../Phase_4/car4_conditional_forecast_phase4.csv
  ../Phase_6/variance_swap_phase6.csv
  ../Phase_6/variance_swap_summary_phase6.csv
  ../../data/germany_wind.csv                      → wind_speed_100m (CAWS)
  ../../benchmark_notebooks/phase6/variance_swap_summary_phase6.csv  → cross-dataset VoI

### Notebook structure (both datasets — adapt paths/labels)

  ## 1. DATA LOADING AND ALIGNMENT
  Load all Phase 1–6 outputs. Print shapes and date ranges.
  Report: Dataset A N=1 462, Dataset B N=3 652.
  Print cross-dataset comparison table (see DATASET STATUS section):
    Source | Location | Height | Period | Primary variables | Advantages | Limitations
  Benchmark notebook (Dataset A): Dataset A row only; note Dataset B values for context.
  Reference notebook (Dataset B): both rows side-by-side as a formatted table.

  ## 2. MODEL HIERARCHY — DM FORECAST TESTS
  Models tested on W̃(t) (1-step-ahead forecast errors):
    (a) Persistence: Ŵ(t) = W̃(t−1)
    (b) AR(4): Ŵ(t) = β₁W̃(t−1)+…+β₄W̃(t−4)  (Phase 2 coefficients, fixed)
    (c) AR(4)+GARCH: AR(4) mean ± sqrt(h_t) scaling  (Phase 3 h_t, fixed)
    (d) CAR(4)+Gaussian: Phase 4 conditional mean forecast
    (e) CAR(4)+NIG: CAR(4) mean with NIG standardisation
  Metric: 1-step-ahead RMSE on W̃(t). No re-estimation — use saved parameters.
  DM test (Diebold-Mariano 1995, Harvey et al. 1997 small-sample correction):
    H₀ = equal predictive accuracy vs Persistence benchmark.
    Report: DM statistic, p-value, rejection at 5% significance.
  Table: rows = models, columns = RMSE, DM stat, p-value, Reject H₀.
  Anchor check: AR(4) RMSE must equal 0.6017 for Dataset A (Phase 2 fixed result).

  ## 3. GARCH VOLATILITY DIAGNOSTICS
  Load garch_z_series_phase3.csv (standardised residuals z_t).
  Ljung-Box test on z_t and z_t² at lags 5, 10, 20.
  ARCH-LM test (lag 5). ACF plot of |z_t| and z_t².
  Verdict: if LB p-value > 0.05 for z_t² at lag 20, GARCH(1,1) is adequate.
  Save: phase7_A_garch_diagnostics.png / phase7_B_garch_diagnostics.png

  ## 4. VALUE OF INFORMATION
  K_var^B_A = h_t0_A × C0_A ≈ 0.4137 × 0.13138 ≈ 0.054  (Bologna proxy)
  K_var^B_B = h_t0_B × C0_B ≈ 0.7511 × 1.163   ≈ 0.874  (Nordfriesland)
  Height-corrected comparison (ONLY in this section):
    K_var^B_A_corrected = K_var^B_A × (100/10)^(2/7) ≈ K_var^B_A × 1.741
  VoI metric: |K_var^B_B − K_var^B_A_corrected| / K_var^B_B (relative mispricing).
  Bar chart: raw and corrected K_var^B side-by-side.

  Empirical distance (direct comparison — Renard council fix):
    Load K_var^A_mean (full-sample mean of rolling K_var^A from Phase 6 CSV).
    Load K_varB_scaled_ht0 from Phase 6 CSV (unit-reconciled Track B).
    Compute: dist_A = |K_var^A_mean − K_varB_scaled_ht0| (absolute distance).
    Print: "Track B_scaled(h_t0) is X units from Track A."
    Reference notebook adds dist_B (Dataset B) and ratio dist_A/dist_B = VoI improvement.

  Delta sensitivity (risk management):
    ∂K_var^B / ∂h = C0   (sensitivity of fair strike to one-unit shift in GARCH state)
    Stress scenario: h_stress = 2 × h_t0  (doubling of conditional variance)
    K_var^B_stress = h_stress × C0; print change ΔK_var^B = K_var^B_stress − K_var^B(h_t0).
    Interpretation: a wind drought that doubles GARCH variance shifts the fair strike by ΔK.

  Save VoI table CSV: phase7_A_voi_table.csv
    columns: Scenario, C0, h_t0, K_varB_raw, K_varB_corrected, K_varB_scaled,
             K_varA_mean, dist_to_KvarA, mispricing_pct
  Benchmark notebook (Dataset A): show Dataset A values only; note Dataset B anchor.
  Reference notebook (Dataset B): full cross-dataset bar chart, VoI table, OOS RMSE.
  Save: phase7_A_voi_comparison.png / phase7_B_voi_comparison.png

  ## 5. CAWS HEDGING SIMULATION
  CAWS (Cumulative Accumulated Wind Speed — normalised rolling annual mean):
    CAWS_t = (1/252) × Σ_{s=t-252}^{t} W(s),  W(s) = daily mean wind speed (m/s)
    Dataset A: data.csv 'wind_speed', resample hourly → daily mean.
    Dataset B: germany_wind.csv 'wind_speed_100m'.
  Normalisation by 252 ensures comparability across heights:
    Dataset A mean CAWS ≈ 2.47 m/s (10 m, Bologna)
    Dataset B mean CAWS ≈ 8+ m/s (100 m, Nordfriesland)
  K_CAWS = mean(CAWS_t) over the full in-sample period (fair strike).
  Synthetic derivative payoff for wind-power producer (N = 1 notional):
    Payoff_t = N × (K_CAWS − CAWS_t)  → positive when wind is low (left-tail hedge)

  Distribution metrics — use NON-OVERLAPPING annual observations to avoid
  overlapping-window serial correlation bias (Tanaka council fix):
    annual_CAWS = [mean(CAWS over calendar year y) for y in sample years]
    annual_payoff = K_CAWS − annual_CAWS   (one obs per year)
    Dataset A: 4 annual obs (2015-2018); Dataset B: 10 annual obs (2015-2024).
    Metrics: mean payoff, std payoff, Sharpe ratio, CVaR₅% on these annual obs.
    Note: rolling-window CAWS_t is still used for plotting the time series.
    Add footnote: "Distribution metrics computed on non-overlapping annual windows
    to avoid serial correlation induced by the 252-day rolling construction."

  Left-tail focus: histogram of annual payoffs with 5th percentile marked in red.
  Save: phase7_A_caws_hedge_pnl.png / phase7_B_caws_hedge_pnl.png

  ## 6. FORECAST ERROR DISTRIBUTION
  Forecast residuals at horizons h = 1, 7, 14 days for each model.
  Q-Q plots vs Normal and NIG (scipy.stats.norminvgauss, Dataset A anchors).
  Jarque-Bera test on 1-day-ahead residuals per model.
  Does NIG offer statistically significant improvement over Gaussian?
  Save: phase7_A_forecast_dist.png / phase7_B_forecast_dist.png

  ## 7. SUMMARY TABLE AND SYNTHESIS
  Table rows: Model × Dataset. Columns: RMSE, DM stat, JB p-value, Payoff VaR₅%.
  Reference notebook adds synthesis: does upgrading A→B improve (a) forecast accuracy,
  (b) pricing accuracy (VoI gap), (c) hedging efficiency (CVaR₅% ratio)?
  Save: phase7_A_dm_results.csv, phase7_A_hedging_summary.csv.

### Outputs — figures (benchmark_latex/figures/, prefix phase7_A_)
  phase7_A_dm_tests.png           → RMSE bar chart + DM test table
  phase7_A_garch_diagnostics.png  → ACF + LB test results
  phase7_A_voi_comparison.png     → K_var^B bar chart with height correction
  phase7_A_caws_hedge_pnl.png     → P&L histogram, left-tail emphasis
  phase7_A_forecast_dist.png      → Q-Q plots

### Outputs — figures (reference_tex/Plots/, prefix phase7_B_)
  Same five plots, plus phase7_B_synthesis.png (cross-dataset summary).

### Outputs — CSVs
  benchmark_notebooks/phase7/:
    phase7_A_dm_results.csv       → Model, RMSE, DM_stat, p_value, Reject_5pct
    phase7_A_hedging_summary.csv  → metric, value (K_CAWS, mean/std/Sharpe/VaR5/CVaR5 on annual obs)
    phase7_A_voi_table.csv        → Scenario, C0, h_t0, K_varB_raw, K_varB_corrected,
                                     K_varB_scaled, K_varA_mean, dist_to_KvarA, mispricing_pct
  reference_nb/Phase_7/:
    phase7_B_dm_results.csv
    phase7_B_hedging_summary.csv
    phase7_B_voi_table.csv        → same columns as A, plus OOS_RMSE_insample, OOS_RMSE_outsample

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