# Model-Assisted Online Learning for Rates Volatility & Tail Risk

A **Model-Assisted Online Learning (MAOL)** framework for sequential volatility forecasting
and Value-at-Risk calibration in government-bond markets. It wraps any parametric volatility
model in three adaptive layers driven by a **parameter-free** optimizer (Continuous Coin
Betting, COCOB) — no learning rate to tune — and comes with **finite-time theoretical
guarantees** on both regret and calibration.

Research seminar project (MSc Quantitative Finance, Erasmus University Rotterdam), supervised
by Dr. Annika Camehl, on a topic proposed by Robeco. **Group work** — see
[Authors](#authors).

> The idea: parametric volatility models (GARCH, HAR-RV) are misspecified in ways that drift
> over time. Instead of re-fitting on a rolling window, *learn the correction online.* Three
> stacked online learners adapt the model, correct its bias, and recalibrate its tail — each
> with a provable bound, and none needing a tuned step size.

## The three-layer framework

- **Layer 1 — online parameter updates.** Base-model parameters are updated each step via the
  QLIKE gradient, using COCOB (parameter-free, no learning rate).
- **Layer 2 — multiplicative bias correction.** A correction `b_t = exp(ρ_t)` is learned
  online on the same loss, absorbing systematic misspecification.
- **Layer 3 — online VaR recalibration.** Value-at-Risk is recalibrated in PIT
  (probability-integral-transform) space by online gradient descent on the pinball loss.

Instantiated with four base models: **RV-GARCH, HAR-RV, Log-GARCH, Log-HAR**.

## Theoretical guarantees

- A **finite-time regret bound** for Layers 1–2.
- An **explicit finite-time unconditional calibration bound** for Layer 3 that holds
  *pathwise* for any deterministic sequence of PIT values — **no distributional assumptions**.

Proofs are in the [paper](paper/paper.pdf), Appendix A.

## Empirical results

Evaluated on **40 years of 10-year US Treasury futures (1983–2024)**:

- **MAOL-Log-GARCH** achieves the lowest average regret against a full-sample HAR-RV oracle
  (**+0.0027** QLIKE per step).
- **MAOL-GARCH** significantly beats EWMA (Diebold-Mariano **−34.5**, `p < 0.001`) and is
  competitive with rolling/expanding HAR benchmarks.
- The recalibrated **5% VaR passes all four** standard backtests — Kupiec, Christoffersen,
  Dynamic Quantile, and Acerbi-Székely — at a **4.82%** violation rate.
- **Cross-country validation** on German Bund, UK Gilt, and Japanese JGB futures shows
  consistent outperformance over EWMA **without any market-specific tuning**.
- A **sample-length study** (2/5/10/15-year windows) shows performance does not need a long
  warm-up; a **regime analysis** shows the multiplier reacting within days to sharp spikes
  (COVID-19) while adapting gradually to prolonged stress (GFC).

## Repository structure

```text
paper/
  main.tex, preamble.tex, bibliography.bib   LaTeX source
  sections/                                  Modular sections + result figures & tables
  paper.pdf                                  Compiled paper
code/
  pipeline.ipynb                             Full MAOL pipeline (models, layers, evaluation)
```

## Data availability

The underlying intraday rates-futures and swaption-volatility data were **provided by Robeco
for the research seminar and are proprietary; they are not included in this repository.** The
code reads local CSVs (e.g. `TY.csv` for 10-year Treasury futures) that a user must supply
from their own data source. All committed figures and tables are aggregate results, not raw
data.

## Authors

A four-person MSc Quantitative Finance team at Erasmus University Rotterdam
(listed alphabetically by surname):

- Mindaugas Butkus
- Antonis Konstantinidis
- Marina Ostojić
- Sanjay Vonk

Supervised by Dr. Annika Camehl. Topic proposed by Robeco.

## License

Code and paper text © 2026 the authors, released under the [MIT License](LICENSE).
Proprietary data is not included.
