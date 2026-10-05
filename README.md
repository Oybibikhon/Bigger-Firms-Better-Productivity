# Bigger Firms, Better Productivity?

## Overview

This project examines the relationship between firm size and labor productivity among firms in Uzbekistan using the **2024 World Bank Enterprise Survey (WBES)**.

The raw data show a large negative difference in sales per worker between large and small firms. That relationship is sensitive to extreme observations and to how potentially inconsistent sales and labor-cost reports are handled. The project therefore compares several specifications and data-quality checks.

### Bottom line

The data do **not** show that larger firms have higher sales per worker. In the main cleaned size-dummy specification, Large firms have significantly lower sales per worker than Small firms, while Medium firms are not statistically different from Small firms. The sales-employment elasticity is below 1 point-estimate-wise and is sensitive to the consistency thresholds. The negative size gap is smaller or becomes statistically insignificant in some robustness checks, so its strength depends on specification. Taken together, the results do not support the claim that bigger firms are more productive in terms of sales per employee.

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

This flags **135 firms**. The upper threshold of 100% is a natural diagnostic boundary because labor costs exceeding total sales means that labor costs alone exceed reported revenue, which is unusual and warrants verification. The lower threshold of 0.1% is intended to flag cases where reported labor costs are implausibly small relative to sales. These are judgment-based diagnostic thresholds, not proof that either sales or labor costs are incorrect, so the notebook also tests alternative cutoffs.

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

The notebook also reports robustness checks:
- Winsorization and trimming
- Median regression
- Alternative labor-share thresholds
- A log-scale labor-share diagnostic
- A missing-labor-cost robustness check
- Survey-weighted regressions
- An audit of extreme sales-per-employee observations

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
| **Main cleaned sample** | **752** | **-0.46** | **0.033** |
| Labor costs observed only | 663 | -0.31 | 0.139 |
| ×1,000 sales rescaling sensitivity | 887 | +0.15 | 0.464 |

The main cleaned large-firm coefficient of -0.46 corresponds to approximately **37% lower sales per worker** than the Small reference group. This is a discrete comparison between broad employee-count categories, not a continuous slope.

### Sales-employment elasticity

| Sample/specification | N | Elasticity | SE | p-value vs. 1 |
|---|---:|---:|---:|---:|
| All firms with valid sales | 887 | 0.748 | 0.074 | 0.0006 |
| Main cleaned sample | 752 | 0.896 | 0.068 | 0.128 |
| Cleaned, survey-weighted | 752 | 0.851 | 0.129 | 0.250 |
| Labor costs observed only | 663 | 0.939 | 0.065 | 0.346 |
| ×1,000 sales rescaling sensitivity | 887 | 1.041 | 0.059 | 0.489 |

The main cleaned estimate is **0.896 (SE 0.068)** and is not statistically distinguishable from 1 at conventional levels. Its approximate 95% confidence interval is **0.76–1.03**. The survey-weighted estimate is **0.851 (SE 0.129)**, with an approximate 95% confidence interval of **0.60–1.10**.

These estimates do not establish that the elasticity equals 1. The point estimate is below 1, and alternative consistency thresholds can produce statistically significant estimates below 1.

### Threshold sensitivity

The main consistency rule flags labor cost above 100% of sales or below 0.1% of sales. The upper threshold is motivated by the diagnostic boundary discussed above, while the lower threshold is intended to identify extremely small labor-cost-to-sales ratios. The threshold table below varies one cutoff at a time. The upper-threshold labels refer to ratios of labor cost to sales: for example, upper 0.5 means 50% of sales, while upper 100 means 10,000% of sales.

| Rule | Upper | Lower | N | Large coef. | Large p | Elasticity | Elasticity p vs. 1 |
|---|---:|---:|---:|---:|---:|---:|---:|
| Main | 1.0 | 0.001 | 752 | -0.456 | 0.033 | 0.896 | 0.128 |
| Upper 0.5 | 0.5 | 0.001 | 653 | -0.595 | 0.008 | 0.848 | 0.034 |
| Upper 2 | 2.0 | 0.001 | 780 | -0.520 | 0.028 | 0.864 | 0.066 |
| Upper 5 | 5.0 | 0.001 | 794 | -0.560 | 0.019 | 0.838 | 0.030 |
| Upper 10 | 10.0 | 0.001 | 800 | -0.531 | 0.026 | 0.850 | 0.044 |
| Upper 100 | 100.0 | 0.001 | 821 | -0.857 | 0.001 | 0.760 | 0.001 |
| Lower 0.0005 | 1.0 | 0.0005 | 753 | -0.454 | 0.033 | 0.898 | 0.133 |
| Lower 0.002 | 1.0 | 0.002 | 747 | -0.518 | 0.014 | 0.881 | 0.079 |

Four of the seven alternative threshold rules reject an elasticity of 1 at the 5% level: Upper 0.5, Upper 5, Upper 10, and Upper 100. The other three do not: Upper 2 (p = 0.066), Lower 0.0005 (p = 0.133), and Lower 0.002 (p = 0.079). Upper 2 and Lower 0.002 are relatively close to the 5% cutoff. In contrast, the Large size coefficient remains negative and statistically significant at the 5% level under every threshold rule shown. Thus, the threshold analysis does not show that the size-dummy result loses significance; it shows that inference about proportional scaling is more sensitive to the consistency cutoff.

### Weighted size-dummy specification

Using the survey weight wmedian changes the size-dummy estimates. In the weighted regression, the Medium coefficient is -0.042 (p = 0.852) and the Large coefficient is **-1.036 (p = 0.004)**, compared with -0.456 (p = 0.033) in the unweighted main cleaned specification. The difference can arise because survey weights change the relative influence of observations and therefore the composition of the weighted estimate; it should not be interpreted as a full survey-design correction.

These weighted regressions use wmedian with HC1 standard errors as a robustness check. They are **not full survey-design estimates** that account for every aspect of the survey design.

## Interpretation

The specifications capture different aspects of the size-productivity relationship:

- The raw size-dummy result shows a large negative large-vs-small difference.
- That estimate changes substantially when extreme observations are treated differently.
- After the main labor-cost consistency screen, the Large coefficient remains negative at -0.46.
- Medium firms are not statistically different from Small firms in the main specification.
- Restricting the analysis to firms with observed labor costs reduces the Large coefficient to -0.31 and makes it statistically insignificant.
- The main cleaned elasticity is 0.896 and is not statistically different from 1, but threshold sensitivity shows that this conclusion is not robust to all consistency cutoffs.
- A separate ×1,000 sales-rescaling exercise produces a positive Large coefficient and an elasticity above 1, but this is an ad-hoc sensitivity assumption and should not be treated as a verified correction.

The results should therefore be presented as **associations**, not causal effects, and the two main specifications should be interpreted alongside the data-quality and sensitivity checks rather than reduced to a single estimate.

## Limitations

- The data are cross-sectional, so the analysis does not identify causal effects.
- Sales per employee is not value added and can reflect differences in input intensity.
- Employment may be measured with error.
- The labor-cost consistency rule depends on its chosen thresholds and on the reliability of reported labor costs.
- The consistency screen is based on labor cost relative to sales, so it uses an outcome-related variable in the data-quality check. It can identify some implausible combinations, but it cannot by itself prove which reported variable is wrong.
- The screen can also miss joint unit errors if both sales and labor costs are mis-scaled in a way that leaves their ratio looking plausible.
- Firms with missing labor-cost data cannot be verified by this screen; the main cleaned sample therefore includes some observations that were not checked. The notebook reports a separate robustness check excluding observations with missing n2a.
- The ×1,000 rescaling exercise is outcome-selected because the suspicious Large firms were identified using very low measured productivity. It is therefore treated as an appendix-style sensitivity check rather than as a preferred correction.
- Survey-weighted regressions use wmedian with HC1 standard errors as a robustness check rather than a full survey-design estimator accounting for strata and clustering.
- Small subgroups, including foreign-owned firms (47) and exporters (98), limit what can be learned from those controls.

## Reproducing the Analysis

1. Obtain the **2024 Uzbekistan WBES** data from the World Bank Enterprise Surveys and save the file as:
   `data/raw/Uzbekistan-2024-full-data.dta`
2. Install Python, pandas, NumPy, statsmodels, matplotlib, and Jupyter.
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

The two specifications should therefore be reported together rather than reduced to a single conclusion. The overall bottom line is that the analysis does not support higher sales per worker among larger firms. The remaining question is **what mechanisms explain productivity differences across firms of different sizes?**
