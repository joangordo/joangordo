# Hi, I'm Joan Gordó

**Statistician and data scientist in Barcelona.** I build forecasting models, biostatistical
analyses and machine-learning systems, and I care most about the part that usually gets
skipped: checking whether the result holds up.

I'm a research assistant in the Statistics section of the University of Barcelona, where I
work on machine-learning flood forecasting, and I'm doing the MSc in Data Science at the UOC.
Before that I spent seven years as a financial data and tax advisor, so I'm used to working
with messy real-world data, deadlines and people who need a clear answer rather than a model.

[jgordoguardia@gmail.com](mailto:jgordoguardia@gmail.com) ·
[LinkedIn](https://www.linkedin.com/in/jgordoguardia/) ·
[Hugging Face](https://huggingface.co/joangordo) ·
Barcelona, Spain · open to relocation

---

## How I work

- **The baseline is the null hypothesis.** A model only counts if it beats a simple
  alternative out of sample, by a margin chosen in advance, with a test that survives
  multiple comparisons.
- **Uncertainty is part of the result.** Confidence intervals, seed variability, simulation
  studies and sensitivity analyses come with every headline number.
- **Negative results get reported.** Several of my projects conclude that the method does
  not work, or that the data cannot answer the question. That is a finding, not a failure.
- **Reproducible by default.** Tests, continuous integration, pinned environments and
  reports generated from code.

---

## Featured projects

### Machine learning for flood forecasting on the Llobregat river · *main project*

**Hourly streamflow forecasts and flood early warning, 1 to 6 hours ahead, for a
4,948 km² Mediterranean basin**, running live and published on a
**[public web map](https://alexrodriguezmolla.github.io/llobregat-prediccio/)**.
First-author paper in preparation for *Ingeniería del Agua*; code repository going public
soon.

The Llobregat floods suddenly: in the November 2024 DANA its flow at Sant Joan Despí rose
from about 25 to 536 m³/s, just upstream of the plant that supplies drinking water to much
of Barcelona. The system forecasts the flow at two control points,
Sant Joan Despí and Martorell, and translates it into four alert levels.

**Data.** 13 river gauges and 3 reservoirs from the Catalan Water Agency (ACA), 30 Meteocat
rain gauges and ERA5-Land reanalysis (soil moisture at four depths, temperature, snow,
evapotranspiration), 2019–2026, about 127,000 half-hourly records. Gauge data are resampled
by taking the nearest reading rather than the mean, so flood peaks are not smoothed away.

**Modelling.** 341 candidate predictors (lags, rolling maxima, accumulated basin rainfall,
antecedent precipitation indices, reservoir volumes) reduced to 5–20 per horizon by backward
elimination on flood-period error. Eight models compared, from persistence and an
autoregressive baseline to direct and hybrid (residual) random forests and LightGBM, and a
LightGBM with a Tweedie loss whose power parameter is tuned per horizon.

**Validation.** Five-fold expanding-window temporal cross-validation, then a fully held-out
final year (June 2025 – May 2026) that the models never saw.

**Results at Sant Joan Despí (held-out year)**

| Lead time | +1 h | +2 h | +3 h | +4 h | +5 h | +6 h |
|---|---:|---:|---:|---:|---:|---:|
| Nash–Sutcliffe efficiency | 0.85 | 0.84 | 0.80 | 0.75 | 0.67 | 0.62 |
| Flood detection (CSI, flow > 95th percentile) | 0.93 | 0.81 | 0.80 | 0.75 | 0.70 | 0.72 |
| Skill vs persistence | −0.62 | 0.08 | 0.24 | 0.27 | 0.25 | 0.25 |

What I learned from it:

- **The loss function mattered more than the algorithm.** The same LightGBM trained with a
  squared loss was the worst model in the comparison; with a Tweedie loss, which fits a flow
  that is low most of the year and extreme on a few days, it was the best at almost every
  horizon.
- **Persistence is hard to beat, and I report it.** One hour ahead, nothing beats repeating
  the current flow. From three hours on, the model cuts the error of persistence by about a
  quarter, which is where an early warning is actually useful.
- **The model learns the hydrology.** The current flow carries 75% of the importance at +2 h
  and 43% at +6 h; accumulated basin rainfall and antecedent wetness take its place.
- **Better weather forecasts would not fix it.** Feeding the model the rain that actually
  fell (a perfect-forecast oracle) did not consistently reduce the error, so the remaining
  error is not mainly a weather-forecast problem.

**Operational system.** Every 30 minutes, a scheduled pipeline downloads the latest ACA and
Meteocat data, builds the features, forecasts the six horizons and publishes them to the web
map. Built with Àlex Rodríguez Molla, who developed the operational pipeline and the web
platform; I led the statistical modelling and validation.

**Now.** As a research assistant I'm leading its extension to basin-wide, multi-station
prediction in Python (a model, features and hyperparameters per station, the full ACA
historical record) and co-supervising an undergraduate thesis on the GIS side.

### [Contaminant removal in low-biomass 16S data](https://github.com/joangordo/decontam-low-biomass) · R

How well do contaminant-removal methods work when the right answer is known? A benchmark on
a public mock-community dilution series and a simulation study (48 scenarios × 20 studies),
ending in a decision guide and a reusable cleaning workflow. Rebuilt from a workflow I first
wrote during a biostatistics internship.

- DNA quantification matters more than the choice of method: 76% vs 8% of contamination
  removed in very-low-biomass designs.
- High thresholds remove real biology; prevalence methods break down with leaky controls.
- Quarto reports, `testthat` tests, GitHub Actions, `renv`.
  **[Read the reports →](https://joangordo.github.io/decontam-low-biomass/)**

### [Can econometric forecasts beat buy & hold?](https://github.com/joangordo/can-forecasts-beat-buy-and-hold) · Python

A walk-forward backtest on four ETFs (SPY, QQQ, GLD, TLT), 2010–2026, with buy & hold as the
null hypothesis: ARIMA, GARCH and VAR forecasts, direction classifiers and volatility-managed
portfolios, all after costs.

- None of 16 model–asset pairs forecast returns better than a random walk (Diebold-Mariano).
- No strategy beats buy & hold's Sharpe ratio after Holm correction, on the test period or
  across five consecutive test windows.
- `statsmodels`, `arch`, `scikit-learn`, HAC and bootstrap tests, a test suite that checks
  for look-ahead bias.

### [Toxicity detection in game chat](https://github.com/joangordo/game-chat-toxicity) · Python, PyTorch

Six-class toxicity detection on multilingual game chat, from a TF-IDF baseline to
character-level networks written from scratch and a fine-tuned XLM-RoBERTa, then applied
without retraining to another game.

- Models trained on game chat reach 0.93–0.95 AUROC; general-purpose toxicity models,
  0.80–0.84.
- A data audit showed the public benchmark cannot rank the models: two classes have only 11
  test messages, and 41% of test texts also appear in training.
- Custom training loop, bootstrap CIs, calibration and a human-in-the-loop moderation policy.
  **[Model on Hugging Face →](https://huggingface.co/joangordo/game-chat-toxicity-xlmr)**

### [Closer](https://github.com/joangordo/closer-geo) · JavaScript, Python

A geography game built on open country statistics: is each country closer to A or to B? The
rules are an explicit difficulty model, every game is reproducible from a seed, and the
dataset (195 countries, 10 indicators) comes from a versioned pipeline with a coverage gate.
**[Play it →](https://joangordo.github.io/closer-geo/)**

### [Import opportunity scoring](https://github.com/joangordo/trade-market-analysis) · Python

Which product categories are worth importing from China into Spain? An end-to-end pipeline
on official Eurostat trade data: API ingestion, feature engineering and a configurable
scoring model, with a worked example of why a top-ranked category was a false positive.

### In progress

**Causal inference in games:** analysis of a mobile-game A/B test with a pre-registered
analysis plan, and a regression discontinuity design on Steam data.

---

## Experience

**Research Assistant, Statistics** · Dept. of Genetics, Microbiology and Statistics,
University of Barcelona · Nov 2025 – present
Statistical and machine-learning models on research datasets, with reproducible pipelines in
Python and R. Leading the multi-station extension of the Llobregat flood-forecasting system;
co-supervising an undergraduate on its GIS component; co-authoring the results for
peer-reviewed publication.

**Financial Data and Tax Advisor** · Assessoria 21 · Segurzon · 2019 – 2025
Managed and analysed financial and tax data for 60+ clients; built data models to detect
inconsistencies and support decisions; turned complex regulation into clear reports for
non-technical clients.

**Data Science Intern** · Gastroenterology Research, Vall d'Hebron Research Institute
(VHIR) · 2018
Cleaned, structured and validated clinical datasets, including low-biomass 16S microbiome
data; wrote the lab's contaminant-removal workflow with `decontam`.

## Education

**MSc in Data Science** · Universitat Oberta de Catalunya (UOC) · 2026 – 2027
Electives: Deep Learning, Natural Language Processing.

**BSc in Statistics** · Universitat de Barcelona / Universitat Politècnica de Catalunya ·
graduated June 2026
Thesis: machine learning for flood-risk prediction in the Llobregat basin, detecting extreme
events in highly imbalanced data from heterogeneous hydrological and meteorological sources.

---

## Toolbox

| | |
|---|---|
| **Statistics** | regression and GLMs, time series (ARIMA, GARCH, VAR), hypothesis testing and multiple comparisons, bootstrap, simulation studies, Bayesian methods, A/B testing and experimental design, causal inference |
| **Machine learning** | gradient boosting (LightGBM), random forests, scikit-learn, PyTorch, Hugging Face Transformers, imbalanced data, temporal cross-validation, calibration |
| **Python** | pandas, NumPy, SciPy, statsmodels, matplotlib, pytest |
| **R** | tidyverse, data.table, ggplot2, caret, Quarto, Shiny, testthat, renv, Bioconductor |
| **Data and tools** | SQL, REST APIs, Git, GitHub Actions, GitHub Pages · familiar with SAS, SPSS, C++ |

**Languages:** Catalan and Spanish (native) · English (C1, IELTS 7.0) · Mandarin (HSK 2,
studying toward HSK 4)

---

Open to data science, biostatistics and ML roles in Barcelona or remote, and to relocation.
Get in touch at [jgordoguardia@gmail.com](mailto:jgordoguardia@gmail.com) or on
[LinkedIn](https://www.linkedin.com/in/jgordoguardia/).
