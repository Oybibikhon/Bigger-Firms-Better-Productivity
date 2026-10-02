# Bigger Firms, Better Productivity?

## Overview

This project examines the relationship between firm size and labor productivity among firms in Uzbekistan using the **2024 World Bank Enterprise Survey (WBES)**.

The raw data show a large negative difference in sales per worker between large and small firms. That relationship is sensitive to extreme observations and to how potentially inconsistent sales and labor-cost reports are handled. The project therefore compares several specifications and data-quality checks.

## Research Question

**What is the relationship between firm size and labor productivity among firms in Uzbekistan?**

## Data

The analysis uses the **2024 World Bank Enterprise Survey for Uzbekistan**:

- 1,008 firms
- 340 variables
- Annual sales (`d2`)
- Permanent employees (`l1`)
- Labor costs (`n2a`)
- Firm age (`b5`)
- Foreign ownership (`b2b`)
- Exporter status (`d3b` + `d3c`)
- Sector and region strata
- Survey weights (`wmedian`)

The raw WBES data are not included in this repository.

### Firm size

Firm size is defined from reported permanent employees:

- **Small:** 1–19 employees
- **Medium:** 20–99 employees
- **Large:** 100+ employees

Firms with fewer than 5 employees are included in Small. The employee-based categories are also cross-checked against the survey's separate `a6a` size variable.

### Productivity

Labor productivity is measured as **annual sales per employee**, in logs. This is a revenue-based measure rather than value added.

## Data Quality

Several observations have sales figures that are difficult to reconcile with firm size and reported labor costs. For example:

- A small firm reports 12 employees and approximately 2.5 × 10^14 UZS in sales.
- A large firm reports 479 employees and only 28.8 million UZS in sales.
- Large firms are over-represented in the bottom 5% of measured productivity: 14.0% of large firms versus 2.4% of small and 3.2% of medium firms.

The main consistency screen flags firms when:

- labor cost exceeds sales, **or**
- labor cost is below 0.1% of sales.

This flags **135 firms**. The rule is a consistency check, not proof that either sales or labor costs are incorrect. Because the thresholds are judgment calls, the notebook also tests alternative thresholds.

## Methodology

The analysis uses **Python** with pandas, NumPy, and statsmodels.

Two main specifications are reported:

1. **Size-dummy specification**
   - Outcome: log sales per employee
   - Small firms are the reference group
   - Controls: firm age, foreign ownership, exporter status, sector, and region

2. **Employment elasticity specification**
   - Outcome: log sales
   - Main explanatory variable: log permanent employment
   - An elasticity of 1 means sales scale proportionally with employment, so sales per worker is constant with employment.

The notebook also reports robustness checks using winsorization, trimming, median regression, alternative labor-share thresholds, a log-scale labor-share diagnostic, a missing-labor-cost robustness check, survey weights, and an audit of extreme sales-per-employee observations.

Standard errors are heteroskedasticity-robust (HC1). Survey-weighted regressions use `wmedian` as a robustness check; they are not full survey-design estimates accounting for every aspect of the survey design.

## Results

### Large vs. small firms

| Specification | N | Large vs. Small | p-value |
|---|---:|---:|---:|
| Raw OLS | 887 | -0.88 | 0.001 |
| Winsorized 1%/99% | 887 | -0.81 | 0.001 |
| Trimmed 1%/99% | 870 | -0.63 | 0.006 |
| Trimmed 5%/95% | 798 | +0.01 | 0.963 |
| Median regression | 887 | -0.34 | 0.086 |
| **Inconsistent firms removed** | **752** | **-0.46** | **0.033** |

The cleaned large-firm coefficient of -0.46 corresponds to approximately **37% lower sales per worker** than the Small reference group. This is a discrete comparison between broad employee-count categories, not a continuous slope.

### Sales-employment elasticity

| Sample | N | Elasticity | SE | p-value vs. 1 |
|---|---:|---:|---:|---:|
| All firms with valid sales | 887 | 0.748 | 0.074 | 0.0006 |
| Inconsistent firms removed | 752 | 0.896 | 0.068 | 0.128 |
| Cleaned, survey-weighted | 752 | 0.851 | 0.129 | 0.250 |

In the cleaned sample, the elasticity estimate is **0.896 (SE 0.068)** and is not statistically distinguishable from 1. Its approximate 95% confidence interval is **0.76–1.03**. The survey-weighted estimate is **0.851 (SE 0.129)**, with an approximate 95% confidence interval of **0.60–1.10**.

These estimates are consistent with proportional scaling, while the confidence intervals also leave room for a modest negative relationship between firm size and sales per worker.

## Interpretation

The specifications capture different aspects of the size-productivity relationship:

- The raw size-dummy result shows a large negative large-vs-small difference.
- That estimate changes substantially when extreme observations are treated differently.
- After the labor-cost consistency screen, the large-firm coefficient remains negative at -0.46.
- The cleaned sales-employment elasticity is close to 1 and is not statistically different from 1.

The results should therefore be presented as **associations**, not causal effects.

## Limitations

- The data are cross-sectional, so the analysis does not identify causal effects.
- Sales per employee is not value added and can reflect differences in input intensity.
- Employment may be measured with error.
- The labor-cost consistency rule depends on its chosen thresholds and on the reliability of reported labor costs.
- The main consistency screen removes a non-trivial share of observations, and its upper threshold is deliberately tested for sensitivity.
- Firms with missing labor-cost data cannot be verified by this screen; the notebook therefore reports a robustness check that excludes observations with missing `n2a`.
- The cleaned results therefore describe firms whose reported sales and labor costs pass the consistency screen.
- Small subgroups, including foreign-owned firms (47) and exporters (98), limit what can be learned from those controls.

## Reproducing the Analysis

1. Obtain the **2024 Uzbekistan WBES** data from the World Bank Enterprise Surveys and save the file as:
   `data/raw/Uzbekistan-2024-full-data.dta`
2. Install Python, pandas, NumPy, statsmodels, and Jupyter.
3. Open `notebooks/02_analysis.ipynb`.
4. Run the notebook from top to bottom.

The raw `.dta` file is excluded from GitHub through `.gitignore`.

## Repository Structure

```
Bigger-Firms-Better-Productivity/
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   └── 02_analysis.ipynb
├── data/raw/                  # raw survey data; not tracked
├── .gitignore
└── README.md
```

## Key Takeaway

The raw data show a large negative large-vs-small productivity gap, but the estimate is sensitive to extreme observations and data-quality screens. After the main labor-cost consistency screen, the large-firm size coefficient remains negative, although substantially smaller than in the raw data. The sales-employment elasticity is below 1 point-estimate-wise, but its confidence interval includes 1; this does not establish that the elasticity equals 1.

The two specifications should therefore be reported together rather than reduced to a single conclusion. The remaining question is **what mechanisms explain productivity differences across firms of different sizes?**
