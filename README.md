# Cross-Asset Correlation & Systemic Risk Monitor

A Python analysis that tracks how relationships between major asset classes shift between calm and stressed markets, and distills that into a single early-warning measure of systemic risk — the **absorption ratio** — with an interactive, regime-labeled correlation heatmap.

---

## The problem

Diversification is supposed to protect a portfolio: hold assets that don't move together, and a shock to one is cushioned by the others. The catch — and the reason 2008 and every crisis since hurt so much — is that **correlations are not constant**. In a panic, assets that look independent in calm markets suddenly sell off together, and diversification evaporates *exactly when it's needed most*.

A static correlation table can't show this; it averages the calm and the chaos into one misleading number. This project measures correlation **dynamically** and compresses the full cross-asset picture into one interpretable signal that rises before and during stress.

## What it does

1. **Pulls 2 years of daily data** for 8 instruments spanning four asset classes:
   - **Equities:** S&P 500 (`^GSPC`), Euro Stoxx 50 (`^STOXX50E`), Nikkei 225 (`^N225`)
   - **Rates:** 7–10yr US Treasuries (`IEF`)
   - **FX:** USD/JPY (`JPY=X`)
   - **Commodities:** Gold (`GLD`), WTI Crude (`CL=F`), Copper (`CPER`)
2. **Cleans** for mismatched international market holidays (forward/back fill) and converts prices to **log returns**.
3. **Computes rolling correlations** (and compares short vs long windows) to show how coupling changes through time.
4. **Reduces the basket to one number** via PCA: the **absorption ratio**, the share of total variance absorbed by the leading principal components — a proxy for how tightly coupled and fragile the system is.
5. **Detects regimes** (risk-on / neutral / risk-off) from the absorption ratio and **annotates** them on the timeline.
6. **Visualizes everything interactively:** a date-slider heatmap that shows the correlation matrix tightening while the absorption ratio spikes and the regime label flips.

## Key result

Plotting the absorption ratio against the S&P 500 reproduces the textbook systemic-risk signature: during the **April 2025 selloff**, the absorption ratio spiked to ~0.70 (its highest in the sample) at the exact moment equities cratered — confirming that coupling surges in stress. In calm rallies the ratio relaxes back toward its diversified baseline (~0.40–0.50). This is the same behavior documented by Kritzman et al., recovered here on live cross-asset data.

## Tech stack

- **Data:** `yfinance`
- **Compute:** `numpy`, `pandas`, `scikit-learn` (StandardScaler, PCA via eigendecomposition / SVD)
- **Visualization:** `matplotlib`, `seaborn`
- **Interactivity:** `ipywidgets` (slider-driven heatmap in Jupyter)

## Method in brief

| Stage | Technique |
|---|---|
| Returns | Log returns, `ln(Pₜ / Pₜ₋₁)` (time-additive) |
| Cleaning | Forward-fill (closed-market gaps) then back-fill (leading edge) |
| Dynamic correlation | Rolling-window correlation matrices |
| Dimensionality reduction | PCA / eigendecomposition of the correlation matrix |
| Systemic-risk metric | Absorption ratio = Σ(top-k eigenvalues) / Σ(all eigenvalues), k ≈ N/5 |
| Regimes | Percentile thresholds on the absorption ratio + standardized shift signal |

## Getting started

```bash
pip install yfinance pandas numpy matplotlib seaborn scikit-learn ipywidgets
jupyter notebook
```

Open the notebook and run the cells top to bottom. The final cell renders the interactive heatmap — drag the **Timeline** slider to scrub through the two-year window and watch the correlation matrix, absorption ratio, and regime label move together.

> **Note:** the interactive widget requires a live Jupyter kernel; it will not render in a static export on GitHub.

## What this demonstrates

Portfolio thinking, diversification logic, and systemic-risk awareness — translating a real institutional risk concept (the absorption ratio) into a working, validated pipeline on multi-asset market data.

## Reference

Kritzman, M., Li, Y., Page, S., & Rigobon, R. (2010). *Principal Components as a Measure of Systemic Risk.* MIT Sloan Research Paper No. 4785-10 / Journal of Portfolio Management, 37(4).

## Disclaimer

Educational project. Not investment advice.
