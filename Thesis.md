# Thesis Brief — Wind-Speed Modelling for Wind Derivatives

> Working brief for drafting the Master's thesis in LaTeX (via Claude Code).
> Master's in Greening Energy Market and Finance (LM-16 R — Finance),
> University of Bologna, Dept. of Statistical Sciences "Paolo Fortunati".
> Supervisor: Prof.ssa Silvia Romagnoli.
>
> Source of truth for all facts, formulas and frozen numbers: `CLAUDE.md`
> at the repo root, plus the phase notebooks and their output CSVs.
> **Never invent, approximate, or recompute a "more precise" version of any
> anchor** — quote it from `CLAUDE.md` or the notebook outputs.

---

## Title

**Full (frontespizio):**
Continuous-Time Modelling of Wind Speed for Derivatives Pricing and Risk Management

**Short (running header / slides):**
Continuous-Time Wind-Speed Modelling for Pricing and Risk Management

*Approved by supervisor (3-chapter structure). She noted the title is slightly
long; the short version is the trimmed form. An alternative that foregrounds the
instrument, if preferred: "Continuous-Time Wind-Speed Modelling for Variance-Swap
Pricing and Risk Management".*

---

## Global decisions (apply throughout)

**Structure:** 3 chapters (approved). Conclusions are the closing section of
Chapter 3, not a standalone chapter.

**Deliverable cadence:** draft and send to the supervisor **one complete chapter
at a time**.

**Datasets and their roles:**
- **Dataset A** — OpenWeatherMap, Bologna, Italy, 10 m, 2015–2018 (4 yr,
  1,462 daily obs). Role: **proxy** on which the methodology is built and
  validated. Low-wind site (mean ≈ 2.47 m/s).
- **Dataset B** — Open-Meteo ERA5 reanalysis, Nordfriesland, Germany, 100 m hub
  height, 2015–2024 (10 yr, ≈ 3,652 daily obs). Role: **reference / main result**,
  geographically aligned with SMARD generation and EEX power markets. All headline
  empirical claims rest on Dataset B.
- **SMARD generation + EEX day-ahead prices** (Germany, 2015–2024) enter in the
  pricing/risk chapter.

**Citation style:** author–year (Harvard/APA-like), via `natbib`
(`\citet` / `\citep`). Chosen because (a) it is the Dept. of Statistical Sciences /
quantitative-finance norm and (b) the existing chapters are already written in it,
so the phase chapters merge without re-keying. The programme does not mandate a
specific style — confirm with supervisor if she expresses a preference.

---

## Editorial rules (from the official GrEnFIn "writing and grading" page — mandatory)

- **Font:** Times, Courier, or Helvetica → set the thesis in **Times**
  (`newtxtext` + `newtxmath`, or `mathptmx`). If existing chapter preambles use
  Latin Modern / Computer Modern, switch to Times for the merged document.
- **Page density (Italian *cartella* spec):** 32–35 lines per page, 65–70
  characters per line. ⇒ ≈ 2,200 characters ≈ **~370 words per page**.
  Planning yardstick: a ~70–90 page main text ≈ 26,000–33,000 words.
- **Printing:** double-sided (fronte/retro) is mandatory.
- **Figures/tables:** UNI A4/A3 format; numbered progressively; table/figure notes
  at the foot of the table/figure, not the page.
- **Frontespizio:** follow the official University title-page + trademark/logo template.

**Grading criteria to write toward** (thesis discussion adds 0–6 points to the
starting grade): originality of topic · methodological correctness · level of
detail · quality of writing/drafting · presentation. Our originality hook is the
Value-of-Information / aligned-data argument; methodological correctness is
protected by the frozen anchors and the framing rules below.

---

## Framing rules / landmines (non-negotiable — carried from the RAship)

1. **θ = 0** is "adopted as a benchmark assumption in the absence of EEX
   option-implied data." Never "θ = 0 is standard" or "the market price of risk is
   zero." Under this benchmark P = Q and Track B reduces to `K_var = h × C0`.
2. **Carr & Lee (2009)** = theoretical motivation for variance-swap replication
   **only**. The implemented formula is **Tol (1997)** `K_var^B = h × C0`. Never
   title an implemented section "Carr-Lee." *(The Dataset B Phase 6 `.tex` violates
   this at the **document `\title{}` level**, not just a section heading — see
   NB below. Track the title fix and the section-heading fix as one edit, but
   verify both, since fixing only the visible heading leaves the PDF/running-header
   title wrong.)*
3. **1.931× height correction** `((100/10)^(2/7))` appears **only** in the
   Value-of-Information comparison (Ch 3), nowhere else.
4. **Track A vs Track B units:** Track A = 252×-annualised realised variance;
   Track B = `h × C0`, left un-annualised. Their numeric gap is a **units
   convention**, not model error. State this explicitly wherever they are compared.
5. **Frozen anchors** (Box–Cox λ, AR/CAR/GARCH coefficients, NIG params, RMSE, VoI
   percentages, Feller margins, etc.) are fixed, validated results. Quote them;
   do not re-derive.

**Notation (fix once, in a nomenclature list; match existing chapters):**
`W̃(t)`, `Λ(t)`, `σ̃²(t)`, `h(t)`, `CAR(4)`, matrix `A`, `exp(Aτ)`, `K_var^A`,
`K_var^B`, `C0`. Do not introduce new symbols for existing objects.

---

## Repository map (where material lives)

```
CLAUDE.md                          ← canonical spec, formulas, frozen anchors
data/                              ← raw + processed CSVs (Bologna, germany_wind, SMARD)
papers/                            ← reference PDFs (bibliography source)
benchmark_notebooks/phase1..7/     ← Dataset A notebooks (all executed)
benchmark_latex/phase1..7/         ← Dataset A LaTeX / PDFs   (see NB)
reference_nb/Phase_1..7/           ← Dataset B notebooks (all executed) + output CSVs
reference_tex/Phase_1..7/          ← Dataset B LaTeX          (see NB)
reference_tex/Plots/               ← Dataset B figures
benchmark_latex/figures/           ← Dataset A figures
```

Phase → topic: 1 deseasonalisation · 2 AR(4) mean · 3 GARCH(1,1) volatility ·
4 CAR(4) + NIG tails + Fourier seasonal variance · 5 stochastic-volatility theory
(Heston/CIR) · 6 synthetic wind variance swap (Tol 1997, θ=0) + CAWS hedging ·
7 cross-dataset validation (Diebold–Mariano, Value of Information).

---

# Chapter structure (3 chapters)

Status tags used below:
- **[READY]** — draftable now from committed material.
- **[CREATE]** — blocked until a missing chapter is written from the notebook.
- **[EXPAND]** — a compact `.tex` exists but must be built to full depth.

---

## Chapter 1 — Introduction, Literature Review and Data  (~18–22 pp) — [READY]

**1.1 Motivation and background.** Wind as a non-tradable, weather-driven
underlying; joint volumetric and price risk for German wind producers; the case for
weather/energy derivatives; economic stakes (≈100 MW farm, ≈€50 M annual revenue —
framed from the Phase 7 synthesis). Nordfriesland = densest German onshore corridor;
EEX German power options are Europe's most liquid.
*Sources:* `CLAUDE.md` (dataset motivation); PH7_A / PH7_B syntheses; ROADMAP objectives.

**1.2 Research questions and contribution.**
- RQ1: can a CAR(4)+GARCH continuous-time framework, calibrated on aligned
  hub-height data, price a synthetic wind variance swap?
- RQ2: what is the Value of Information from upgrading a geographic proxy
  (Bologna 10 m) to an aligned reference (Nordfriesland 100 m)?
- RQ3: how effective is the CAWS instrument as a left-tail hedge?
Contribution: methodology built and validated on A, applied on B; explicit VoI
quantification; CAWS construction.

**1.3 Literature review** (by ROADMAP cluster):
1.3.1 Statistical properties of wind (Weibull, Box–Cox) — Tuller & Brett 1984,
Garcia et al. 1998, Šaltytė Benth & Benth.
1.3.2 Time-series & long memory — Torres et al. 2005, Kavasseri & Seetharaman 2009,
Ailliot–Monbet–Prevosto 2006, Haslett & Raftery 1989.
1.3.3 Conditional volatility — Engle 1982, Bollerslev 1986, Tol 1997.
1.3.4 Continuous-time & Lévy — Brockwell & Marquardt 2005, Schwartz 1997,
Barndorff-Nielsen & Shephard 2001.
1.3.5 Stochastic volatility — Heston 1993.
1.3.6 Weather/energy derivatives & market price of risk — Alaton–Djehiche–Stillberg
2002, Benth & Šaltytė Benth 2009, Härdle & Cabrera 2011, Dorfleitner & Wimmer 2010,
Alexandridis & Zapranis 2013; Carr & Lee 2009 (variance-swap replication —
motivation only).
1.3.7 Gap statement: aligned-data wind variance-swap pricing with explicit VoI.
*Sources:* `papers/` corpus; ROADMAP "Role in Research" column.

**1.4 Data.**
- 1.4.1 Dataset A (proxy) — provenance, 10 m, 2015–2018, descriptive stats.
- 1.4.2 Dataset B (reference) — ERA5, 100 m, 2015–2024, `wind_speed_100m` primary.
- 1.4.3 SMARD generation + EEX day-ahead prices — file inventory; hourly→daily
  (generation = sum, price = mean); DE/AT/LU vs Germany/Luxembourg merge at 01/10/2018.
- 1.4.4 **Cross-dataset comparison table** (Source | Location | Height | Period |
  Variables | Advantages | Limitations) — presented once here, not per phase.
*Sources:* `CLAUDE.md` DATASET STATUS + SMARD notes; PH1_A / PH1_B descriptive stats;
`smard_daily_phase6.csv`.

**1.5 Thesis outline.**

---

## Chapter 2 — Methodology and Empirical Wind-Speed Modelling  (~35–40 pp, core)

Method and results interleaved, phase by phase (P1–P5). Both datasets, **Dataset B
primary**; each subsection presents A and B side by side.

**2.1 Deseasonalisation & transformation** (Phase 1) — Box–Cox λ, Fourier seasonal
mean Λ(t), seasonal variance σ̃²(t), standardised W̃(t). Results: Weibull (k,c), λ,
variance-modulation %. — [READY]
*Sources:* PH1_A (PDF), PH1_B (`.tex`); phase-1 figures.

**2.2 Conditional mean — AR(4)** (Phase 2) — PACF cutoff at lag 4, OLS, link to the
continuous-time OU/CAR representation. Results: β₁…β₄, RMSE vs persistence
(A: 0.6017 vs 0.6856 ≈ 12.2 %), long-memory verdict. — [READY]
*Sources:* `ar4_coefficients.csv`, `ar4_residuals_phase2.csv`; PH2_A, PH2_B.

**2.3 Conditional volatility — GARCH(1,1)** (Phase 3) — two-component
σ²_total = σ̃²·h. Results: ω, α, β, persistence α+β, half-life, A-vs-B comparison,
standardised-residual diagnostics. — [READY]
*Sources:* `garch_parameters_phase3.csv`; PH3_A, PH3_B.

**2.4 Continuous-time embedding — CAR(4)** (Phase 4) — companion matrix A,
exp(Aτ), conditional moments under P, NIG tails, Fourier seasonal variance. Results:
eigenvalues & stationarity, conditional-forecast term structure, NIG α̂/δ̂,
Samuelson-effect violation for p>1. — [READY]
*Sources:* `car4_parameters_phase4.csv`, `car4_matrix_A_phase4.csv`,
`car4_eigenvalues_phase4.csv`, `car4_conditional_forecast_phase4.csv`;
PH4_B (`.tex`). *(PH4_A is PDF-only — draw the A column from the PDF + CSVs.)*

**2.5 Stochastic-volatility framework** (Phase 5) — Heston/CIR two-SDE embedding,
Feller condition, characteristic function / Riccati system, **θ=0 benchmark**, why
MLE is deferred to hourly data. Results: Feller margin (A 27.9×, B 28.1×),
round-trip consistency (max|Δ| = 4.44e−16), variance term structure (piecewise-h vs
Heston/CAR(1)).
- Dataset B column — [READY] (`reference_tex/Phase_5/PH5_Analysis.tex`).
- **Dataset A column — [CREATE]** (see NB): write `benchmark_latex/phase5` from
  `benchmark_notebooks/phase5/PHASE_5.ipynb`.
*Framing watchpoints:* θ=0 wording; MLE-deferral rationale.

---

## Chapter 3 — Derivatives Pricing, Risk Management and Conclusions  (~25–30 pp, headline)

**3.1 The synthetic wind variance swap** (Phase 6) — instrument definition;
Track A empirical strike (252 × rolling realised variance from SMARD); Track B model
strike `K_var^B = h × C0` across four h-scenarios (h_t0, h_winter, h_summer, h=1);
unit reconciliation via delta-method `(9/W̄²)`; power-curve linkage G ∝ W³ (OLS R²,
constant c); merit-order effect (negative G–P correlation, pre/post-2018 robustness);
payoff fan / 10-year simulation.
- Dataset A — [READY] (`benchmark_latex/phase6/PHASE_6_Analysis.tex`).
- **Dataset B — [EXPAND]** (see NB) + **retitle the "Carr-Lee" section**.
*Sources:* `variance_swap_phase6.csv`, `variance_swap_summary_phase6.csv`,
`smard_daily_phase6.csv`; phase-6 figures.

**3.2 Cross-dataset forecast validation** (Phase 7) — Diebold–Mariano
(Harvey–Leybourne–Newbold small-sample correction); GARCH volatility diagnostics
(Ljung–Box, ARCH-LM); forecast-error distribution (Normal vs NIG, Jarque–Bera).
- Dataset A — [READY]; **Dataset B — [EXPAND]**.
*Sources:* `phase7_A_dm_results.csv`, `phase7_B_dm_results.csv`; phase-7 figures.

**3.3 Value of Information** (Phase 7) — K_var^B_A ≈ 0.054 vs K_var^B_B ≈ 0.874;
93.8 % raw mispricing; 88 % after height correction (**1.931× factor only here**);
finding: geographic mismatch (Bologna's ≈8.8× lower C0) dominates the error, not
model misspecification; economic magnitude.
- Dataset A — [READY]; **Dataset B — [EXPAND]**.
*Sources:* `phase7_A_voi_table.csv`, `phase7_B_voi_table.csv`.
*Three landmines concentrate here: 1.931× scope, θ=0, Track A/B units.*

**3.4 Risk management — CAWS hedging** (Phase 7) — K_CAWS; distribution metrics on
**non-overlapping annual** observations (A: 4 obs 2015–2018; B: 10 obs 2015–2024) to
avoid overlapping-window bias; CVaR₅% (A ≈ −0.121 m/s; B from CSV); left-tail
histogram; Sharpe; delta sensitivity ∂K_var^B/∂h = C0 and the h = 2×h_t0 stress
scenario.
- Dataset A — [READY]; **Dataset B — [EXPAND]**.
*Sources:* `phase7_A_hedging_summary.csv`, `phase7_B_hedging_summary.csv`;
`germany_wind.csv` / `data.csv` (CAWS series).

**3.5 Conclusions.** Summary (forecast hierarchy validated — AR(4) reliable one-step
benchmark, CAR(4) preserves accuracy while enabling pricing; GARCH(1,1) sufficient;
geographic mismatch dominates pricing error; A→B upgrade economically justified).
Limitations (θ=0 benchmark; daily-frequency non-identification of SV params; proxy
nature of A; SMARD national aggregation vs single grid point). Future work (EEX
option-implied θ + full Carr–Lee replication; ECMWF/ERA5 hourly SV estimation;
NIG/Lévy-driven pricing). Implications for producers/risk managers. — [READY] as
skeleton; finalise once 3.1–3.4 Dataset B is expanded.

---

## Appendices & bibliography

- **A.** Full coefficient tables (AR, CAR, GARCH, NIG) with SE, t-stat, p-value.
- **B.** Derivations: matrix exponential exp(Aτ); Riccati ODEs for the characteristic
  function; Feller condition.
- **C.** Extended diagnostics (Ljung–Box, ARCH-LM, ACF/PACF).
- **D.** SMARD/EEX processing detail (merge rule, missing-data handling).
- **E.** Elementary "Technical Note" boxes relocated out of the main text.
- **Bibliography:** `papers/` corpus, author–year. See bibliography NB below.

---

# NB — LaTeX chapters to CREATE / EXPAND from the RAship workflow

The RAship produced 14 phase documents (7 phases × 2 datasets). Most are complete;
**three writing jobs** remain, and they are the critical path. Do these in the
LaTeX style of the existing phase chapters (graybox / litbox / darkblue-midblue),
now switched to the Times / cartella editorial spec above.

0. **FIX — Bibliography, Phase 1 & Phase 2 (do this before drafting Chapter 1).**
   Small, mechanical, but not optional — see NB below for the full diagnosis. Two
   `\bibitem` blocks currently contain the wrong paper's content; the correct
   content already exists verbatim in four sibling files. This is a ~10-minute
   copy-paste fix with zero new research required, and it directly repairs the
   citation underpinning the AR(4)↔CAR(4) Euler-discretisation claim planned for
   **Ch 2 §2.4** — treat it as a correctness fix, not bibliography hygiene.
   Track separately from job 2's Carr-Lee title/heading fix (different files).

1. **CREATE — Dataset A, Phase 5.** `benchmark_latex/phase5/` is empty (only a
   `.gitkeep`); no `.tex`, no PDF. Write it from
   `benchmark_notebooks/phase5/PHASE_5.ipynb`. Anchors already exist (Feller 27.9×,
   CIR κ_V=0.0165, v̄=0.608, ξ²≈4.8e−5). → unblocks **Ch 2 §2.5 Dataset A column**.
   *This is the only true void.* Note: this is the single **CREATE** job (write
   from nothing) versus jobs 2–3 which are **EXPAND** (grow an existing skeleton);
   it gates Chapter 2, the largest single chapter (~35–40 pp) — if drafting runs
   long, decide the fallback (e.g. ship Ch 2 without §2.5 and backfill) rather than
   blocking the whole chapter's delivery.

2. **EXPAND — Dataset B, Phase 6.** `reference_tex/Phase_6/PHASE_6_Analysis.tex`
   is a compact draft (~220 lines vs 297–425 for sibling B chapters). Build it to
   full depth. **Also retitle** — at **both** the document `\title{}` ("Phase 6:
   Carr-Lee Variance Swap Pricing") **and** the section heading currently named
   "Carr-Lee Variance Swap Framework (Dataset B)" — → e.g. "Synthetic Wind Variance
   Swap Framework (Dataset B)" (framing rule 2). There is also a `\bibitem{CarrLee:2009}`
   in this file; keep it (Carr-Lee remains a valid citation for the motivation
   section) but make sure no heading implies it is the implemented model.
   Notebook + CSVs are present. → unblocks **Ch 3 §3.1 Dataset B**.

3. **EXPAND — Dataset B, Phase 7.** `reference_tex/Phase_7/PHASE_7_Analysis.tex`
   is a compact draft (~244 lines). Build to full depth. CSVs present
   (`phase7_B_dm_results.csv`, `_voi_table.csv`, `_hedging_summary.csv`).
   → unblocks **Ch 3 §§3.2–3.4 Dataset B**.

**Also note:** Dataset A Phases 1–4 exist only as **compiled PDFs** (no `.tex`
committed in `benchmark_latex/phase1..4`). When writing the Dataset A columns of
Ch 2, draw content from those PDFs + the CSVs — do not silently re-round a number
that differs from a frozen `CLAUDE.md` anchor; if a PDF figure and a CSV/anchor
disagree, the anchor wins and the discrepancy should be flagged, not merged away.
Dataset A Phases 6–7 have full `.tex`.

**Sequencing:** job 0 (bibliography fix) comes **before** Chapter 1, since Ch 1
§1.3 is the literature review — the first place a wrongly-bound citation would
surface in submitted text. Chapter 1 and Chapter 2 §§2.1–2.4 are otherwise fully
unblocked → draft first. Only §2.5 (A) is gated by job 1. Chapter 3's Dataset B
half is gated by jobs 2–3. Recommended order: **job 0 (bibliography fix) →
Chapter 1 → job 1 (create A-Phase 5) → Chapter 2 → jobs 2–3 (expand B-Phase 6/7,
incl. Carr-Lee title/heading fix) → Chapter 3.**

---

# NB — Bibliography cleanup

**Verified (via `pdftotext` header extraction of the actual PDF, not just the
filename) — the wind-futures source paper is present and correctly identified:**
`papers/Benth (2008) - Dynamic pricing of wind future.pdf` opens with
*"Energy Economics 31 (2009) 16–24 … Dynamic pricing of wind futures … Fred Espen
Benth, Jūratė Šaltytė Benth."* The "(2008)" in the filename is the
acceptance/submission date; the journal publication date is **2009** — cite as
Benth & Šaltytė Benth (2009). This is the core CAR(4)/Euler / Samuelson-effect
reference. **No source paper is missing.**

**The actual bug — wrong content under the citation key, not a missing file:**
`reference_tex/Phase_1/PH1_Analysis.tex:325-326` (key `SaltyteBenth2009`) and
`reference_tex/Phase_2/PH2_Analysis.tex:287-288` (key `BenthSaltyteBenth2009`)
both contain the **wrong paper's** `\bibitem` content — the title, journal, and
author initial of a *different*, temperature-focused paper ("Šaltytė Benth, **A.**
… *Stochastic modelling of temperature variations with applications to weather
derivatives and risk management*"), not the wind-futures paper their surrounding
prose actually cites. `reference_tex/Phase_4/PH4_Analysis.tex:348`,
`Phase_5/PH5_Analysis.tex:421`, `Phase_6/PHASE_6_Analysis.tex:210`, and
`Phase_7/PHASE_7_Analysis.tex:234` all already have the **correct** entry
("Benth, F. E., & Šaltytė Benth, J. (2009). *Dynamic pricing of wind futures*.
*Energy Economics*, 31(1), 16–24.").

**Fix (job 0 above — do before Chapter 1):** replace the two wrong `\bibitem`
blocks in Phase 1 and Phase 2 with the correct entry, copied verbatim from
Phase 4/5/6/7. This is a content swap, not a "merge two duplicate keys" cleanup —
each independent phase bibliography must be checked by content, not assumed
equivalent because the keys are similar. This also matters beyond the reference
list: Phase 2's citation under this key backs the AR(4)→CAR(4) Euler-discretisation
claim planned for **Ch 2 §2.4** — a wrong citation there is a repairable but real
gap in that claim's evidentiary basis, not cosmetic.

**Separate, still-open item (unaffected by the above):** a genuinely different
paper — **Šaltytė Benth & Benth (2008)**, *Stochastic modelling of temperature
variations…*, Applied Mathematical Finance 15 — is cited (correctly, as its own
entry) in `Phase_3/PH3_Analysis.tex:287` and `Phase_4/PH4_Analysis.tex:360`, but
has **no PDF in `papers/`**. Decide before finalising Chapter 1: source the PDF,
or drop the citation.

---

# Numerical anchors (quote — do not recompute; source: CLAUDE.md + CSVs)

**Dataset A (Bologna 10 m):**
Box–Cox λ = 0.1 · AR(4) β = (0.6299, −0.1443, 0.0926, 0.0310) ·
CAR(4) α = (3.3701, 4.2546, 2.3063, 0.3908) · eigenvalues {−1.200, −0.936±0.465i,
−0.298} · GARCH ω=0.01004, α=0.0311, β=0.9524 (α+β=0.9835) · h(t₀)=0.4137 ·
σ²_total(t₀)=0.0926 · NIG α̂=4.374, δ̂=2.087 · RMSE persistence=0.6856,
AR(4)=0.6017 · Feller 27.9× · round-trip max|Δ|=4.44e−16 · CIR κ_V=0.0165,
v̄=0.608, ξ²≈4.8e−5 · C0=0.13138 · K_var^B_A≈0.054.

**Dataset B (Nordfriesland 100 m):**
Box–Cox λ=0.5 · Weibull k=2.67, c=9.09 · σ²(t) range [0.944, 1.463] (≈55 % modulation) ·
GARCH ω=0.0135, α=0.0059, β=0.9762 (α+β=0.9821), half-life ≈ 38.3 days ·
Feller 28.1× · CIR κ_V=0.0179, v̄=0.7522, ξ²=0.000959 · h(t₀)=0.7511 ·
C0≈1.163 · K_var^B_B≈0.874 · VoI 93.8 % raw / 88 % height-corrected · C0 ratio ≈ 8.8×.

*(Dataset B anchors are documented separately in its own phase chapters; verify
each against `CLAUDE.md` / the reference CSVs before quoting.)*
