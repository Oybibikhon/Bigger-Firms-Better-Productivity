# Bigger Firms, Better Productivity?

## Overview

This project examines the relationship between firm size and labor productivity among firms in Uzbekistan, using the 2024 World Bank Enterprise Survey (WBES).

The initial regressions suggested that large firms are far *less* productive than small firms. Closer inspection showed that this result was driven largely by firms whose reported sales contradict their own reported labor costs. After removing these inconsistent observations, the estimated sales-employment elasticity is close to 1, although the confidence interval still allows for a modest negative relationship between firm size and sales per worker.

## Research Question

**Do larger firms exhibit higher labor productivity than smaller firms in Uzbekistan?**

## Data

Firm-level data from the **World Bank Enterprise Survey for Uzbekistan (2024)**: 1,008 firms, 340 variables. The raw data file is not included in this repository (see *Reproducing the analysis*).

Key variables:

- Annual sales (`d2`) and number of permanent employees (`l1`)
- Labor costs (`n2a`), used to cross-check the sales figures
- Firm age (survey year minus year of establishment, `b5`)
- Foreign ownership (`b2b`) and exporter status (`d3b` + `d3c`)
- Sector and region strata
- Survey weights (`wmedian`; `wstrict` and `wweak` give similar results)

Productivity is measured as annual sales per employee (in logs). This is a revenue-based measure, not value added.

Firm size categories: Small (1-19 employees), Medium (20-99), Large (100+). The 9 firms with fewer than 5 employees are included in Small.

## Data quality problem

Many sales values are implausible relative to firm size:

- Some small firms report sales equivalent to billions of dollars (for example, 12 employees and 2.5e14 UZS in sales).
- Some large firms report sales far too low for their headcount (for example, 479 employees and 28.8 million UZS in sales).
- Large firms are heavily over-represented in the bottom 5% of productivity: 14.0% of large firms fall there, versus 2.4% of small and 3.2% of medium firms.

For these firms, reported labor costs are normal (median about 18 million UZS per employee, the same as other firms), so the inconsistency lies in the sales figure. To identify these cases without relying only on the productivity outcome, the main cleaning rule flags firms when:

- labor cost exceeds sales (120 firms), or
- labor cost is below 0.1% of sales (15 firms).

These thresholds are judgment calls rather than uniquely determined cutoffs. The analysis therefore includes a sensitivity check that varies the upper threshold to 50% and 200% of sales and the lower threshold to 0.05% and 0.2% of sales, re-estimating both the size-dummy and elasticity specifications under each rule.

In total 135 firms are flagged, about 13-16% of each size group. Firms with missing labor cost cannot be tested and remain in the sample. The flagging rule cannot tell whether sales or labor cost is the wrong number, so the whole firm is dropped.

## Methodology

- Python (pandas, NumPy, statsmodels).
- OLS with heteroskedasticity-robust (HC1) standard errors; weighted least squares with survey weights as a robustness check.
- Controls: firm age, foreign ownership, exporter status, sector, region.
- Small firms are the reference category.
- Two outcome specifications:
  1. Log sales per employee, on firm-size categories.
  2. Log sales on log employment (elasticity). This avoids placing employment in the denominator of the outcome, where measurement error in employment would bias the size coefficient downward. An elasticity of 1 means productivity per worker does not change with size.

## Results

### Large vs. small firms (log sales per employee)

| Specification | n | Large vs. small | p-value |
|---|---|---|---|
| OLS, raw outcome | 887 | -0.88 | 0.001 |
| OLS, winsorized at 1%/99% | 887 | -0.81 | 0.001 |
| OLS, trimmed at 1%/99% | 870 | -0.63 | 0.006 |
| OLS, trimmed at 5%/95% | 798 | +0.01 | 0.96 |
| Median regression | 887 | -0.34 | 0.086 |
| **OLS, inconsistent firms removed** | 752 | **-0.46** | **0.033** |

Medium firms are not statistically different from small firms in any specification.

The large-firm penalty shrinks steadily as extreme values are removed. Trimming the tails of the outcome variable also removes valid observations, so the labor-cost consistency rule is the preferred cleaning method. On that sample, the large-firm coefficient is -0.46, corresponding to about 37% lower sales per worker than the Small reference group (exp(-0.46) - 1). This is a discrete comparison between broad employee-count categories, not an estimate of the slope of productivity over the full employment distribution.

### Elasticity of sales with respect to employment

| Sample | n | Elasticity | Std. error | p-value (vs. 1) |
|---|---|---|---|---|
| All firms with valid sales | 887 | 0.748 | 0.074 | 0.0006 |
| Inconsistent firms removed | 752 | 0.896 | 0.068 | 0.128 |
| Inconsistent firms removed, survey-weighted | 752 | 0.851 | 0.129 | 0.250 |

In the cleaned sample the elasticity is not statistically distinguishable from 1. The point estimate is 0.896 (SE 0.068), with an approximate 95% confidence interval of 0.76–1.03. The survey-weighted estimate is 0.851 (SE 0.129), with an approximate 95% confidence interval of 0.60–1.10. These estimates are consistent with proportional scaling, but the intervals also leave room for a modest negative relationship between firm size and sales per worker.

## Interpretation

The results do not provide evidence that larger firms are more productive. The two main specifications describe the size relationship in different ways:

- In the raw data, large firms appear about 55% less productive, but this is mostly a data-quality artifact.
- After cleaning, the large-firm gap is smaller (about 30-37% at the median and in OLS) and only moderately significant.
- The elasticity estimates are close to 1 and not statistically different from it, but their confidence intervals do not rule out a modest negative relationship.

These are associations in observational, cross-sectional data and do not show that growing a firm changes its productivity.

## Limitations

- Cross-sectional data; results are associations, not causal effects.
- Productivity is sales per employee, not value added. It depends on input intensity, so it is not directly comparable across sectors (sector fixed effects only partly address this).
- Employment counts may be measured with error, which biases the sales-per-employee comparison against larger firms. The elasticity specification reduces this problem but does not remove it; the true elasticity may be somewhat higher than 0.90.
- The consistency rule for removing firms depends on its thresholds and on the labor-cost variable being reliable. It is not fully independent of the outcome, since labor cost above sales implies low measured productivity. Alternative thresholds are therefore reported as a robustness check rather than treating the main cutoffs as uniquely correct.
- About 13-16% of each size group is dropped in the cleaned sample, so the conclusions apply to firms with internally consistent reports.
- Small subgroups (foreign-owned firms: 47; exporters: 98) limit what the control variables can show.

## Reproducing the analysis

1. Obtain the WBES Uzbekistan 2024 data from the World Bank Enterprise Surveys website (subject to their terms of use) and save it as `data/raw/Uzbekistan-2024-full-data.dta`. The `.dta` file is excluded from this repository via `.gitignore`.
2. Install the requirements: Python, pandas, NumPy, statsmodels, Jupyter.
3. Open `notebooks/02_analysis.ipynb` and run all cells from top to bottom.

## Repository Structure

```
Bigger-Firms-Better-Productivity/
│
├── notebooks/
│   ├── 01_data_exploration.ipynb   # original exploratory notebook
│   └── 02_analysis.ipynb           # clean analysis with data-quality checks
│
├── data/raw/                       # raw survey data (not tracked)
├── .gitignore
└── README.md
```

## Key Takeaway

The raw data appear to show that large firms are much less productive, but that result is largely driven by inconsistent sales reports. Once firms with contradictory sales and labor-cost figures are removed, the estimated sales-employment elasticity is close to 1. This is consistent with proportional scaling, but the confidence interval does not rule out a modest negative relationship between firm size and sales per worker. The size-dummy specification separately estimates a sizable negative gap for large firms, so the two specifications should be reported rather than collapsed into a single conclusion.
