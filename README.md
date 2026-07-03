# Time Series Analysis — TikTok Engagement Pulse
### Discovery-to-Action (DTA) Strategy

## Dataset

`tiktok_views.csv` — daily TikTok view counts for a single account over a ~7-month window
(Nov 2025 – May 2026), 196 logged days out of 210 calendar days (14 missing/unlogged days).
The dataset reflects realistic TikTok engagement dynamics: a gradual account-growth trend,
a strong weekly seasonal pattern (higher engagement Thu–Sun), a handful of viral spike days,
and natural day-to-day noise.

*Note: this is a synthetic dataset generated to reflect realistic TikTok engagement patterns,
used in place of a live TikTok Analytics export for this project.*

## Discovery-to-Action (DTA) Workflow

**1. Discovery Phase**
- Loaded the CSV, converted `date` to `datetime64`, and set it as the index.
- Resampled to daily frequency and linearly interpolated the 14 missing days so the series
  has a consistent, gap-free timestamp spacing (required for decomposition and ADF testing).
- Applied `seasonal_decompose` (additive model, `period=7`) to split the series into Trend,
  Seasonality, and Residual components, then visualized each.

**2. Technical Phase**
- Ran the Augmented Dickey-Fuller (ADF) test on the original series:
  **H₀: non-stationary vs. Hₐ: stationary.**
  Result: **p = 0.9013 → fail to reject H₀ → non-stationary** (expected, given the visible
  upward growth trend).
- Applied first-order differencing and re-ran the ADF test:
  **p ≈ 0.0000 → reject H₀ → stationary.**
- Conclusion: **d = 1** is the minimum differencing order needed before the series is ready
  for ARIMA/SARIMA-style forecasting.

**3. Action Phase**
- Extracted the average seasonal effect per weekday from the seasonal component.
- **Peak engagement days:** Saturday (+5,888 vs. trend), Friday (+5,452), Sunday (+4,587).
- **Weakest engagement day:** Monday (−6,996 vs. trend).

## Business Recommendation

- **Primary upload window:** Friday evening through Sunday, to ride the natural weekly
  traffic wave.
- **Secondary window:** Thursday, to build momentum ahead of the weekend peak.
- **Avoid launching flagship content on Monday/Tuesday** — reserve these lower-traffic days
  for lightweight content (behind-the-scenes, community replies) instead.
- **Maintenance window:** Monday — the lowest-traffic day is the safest time for algorithm
  A/B tests, account settings changes, or analytics review with minimal disruption to live
  engagement.

## Path to Forecasting (Next Steps)

This analysis establishes the two structural parameters needed for a formal forecasting
model:
- **d = 1** (order of differencing), confirmed by the ADF test.
- **Seasonal period m = 7** (weekly cycle), confirmed by the decomposition.

Next steps: use ACF/PACF plots on the differenced series to select `p`/`q` (and seasonal
`P`/`Q`), fit candidate **SARIMA(p, 1, q)(P, D, Q)₇** models, validate with a train/test
split, and generate a forward-looking view-count forecast.

## Files

- `tiktok_time_series.ipynb` — full analysis notebook (all cells executed, plots embedded)
- `tiktok_views.csv` — dataset
- `README.md` — this file
