# Bigger Firms, Better Productivity?

## Overview

This project examines the relationship between firm size and labor productivity among firms in Uzbekistan, using the 2024 World Bank Enterprise Survey (WBES).

The initial regressions suggested that large firms are far *less* productive than small firms. Closer inspection showed that this result was driven largely by firms whose reported sales contradict their own reported labor costs. After removing these inconsistent observations, sales grow roughly in proportion to employment, and any remaining large-firm disadvantage is weak.

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

For these firms, reported labor costs are normal (median about 18 million UZS per employee, the same as other firms), so the inconsistency lies in the sales figure. To identify these cases without selecting on the outcome, firms are flagged when:

- labor cost exceeds sales (120 firms), or
- labor cost is below 0.1% of sales (15 firms).

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

The large-firm penalty shrinks steadily as extreme values are removed. Trimming the tails of the outcome variable also removes valid observations, so the labor-cost consistency rule is the preferred cleaning method. On that sample, large firms have about 37% lower sales per worker than small firms (exp(-0.46) - 1), with moderate statistical evidence.

### Elasticity of sales with respect to employment

| Sample | n | Elasticity | Std. error | p-value (vs. 1) |
|---|---|---|---|---|
| All firms with valid sales | 887 | 0.748 | 0.074 | 0.0006 |
| Inconsistent firms removed | 752 | 0.896 | 0.068 | 0.128 |
| Inconsistent firms removed, survey-weighted | 752 | 0.851 | 0.129 | 0.250 |

In the cleaned sample the elasticity is not distinguishable from 1: sales grow roughly in proportion to headcount, so productivity per worker is about the same across firm sizes.

## Interpretation

The data do not support the claim that larger firms are more productive. They also do not show a clear large-firm disadvantage:

- In the raw data, large firms appear about 55% less productive, but this is mostly a data-quality artifact.
- After cleaning, the large-firm gap is smaller (about 30-37% at the median and in OLS) and only moderately significant.
- The elasticity of sales with respect to employment is close to 1 and not statistically different from it.

These are associations in observational, cross-sectional data and do not show that growing a firm changes its productivity.

## Limitations

- Cross-sectional data; results are associations, not causal effects.
- Productivity is sales per employee, not value added. It depends on input intensity, so it is not directly comparable across sectors (sector fixed effects only partly address this).
- Employment counts may be measured with error, which biases the sales-per-employee comparison against larger firms. The elasticity specification reduces this problem but does not remove it; the true elasticity may be somewhat higher than 0.90.
- The consistency rule for removing firms depends on its thresholds and on the labor-cost variable being reliable. It is not fully independent of the outcome, since labor cost above sales implies low measured productivity.
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

The raw data appear to show that large firms are much less productive, but that result is largely driven by inconsistent sales reports. Once firms with contradictory sales and labor-cost figures are removed, sales scale roughly one-for-one with employment. Larger firms are not more productive than smaller ones in this sample, and any disadvantage is modest and imprecisely estimated.
