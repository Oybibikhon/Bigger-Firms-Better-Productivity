Bigger Firms, Better Productivity?
Overview
This project examines the relationship between firm size and labor productivity among firms in Uzbekistan using data from the World Bank Enterprise Survey.
The original research question asks whether larger firms tend to exhibit higher productivity. Rather than assuming that relationship in advance, the analysis tests the relationship empirically using descriptive statistics and regression analysis.
Research Question
Do larger firms exhibit higher labor productivity than smaller firms in Uzbekistan?
The analysis focuses on whether productivity differs systematically across firm-size categories after accounting for other observable firm characteristics.
Data
The analysis uses firm-level data from the World Bank Enterprise Survey for Uzbekistan.
Key variables include:
Annual sales
Number of employees
Firm size
Firm age
Foreign ownership
Exporter status
Sector
Region
The main productivity measure is calculated as annual sales per employee and transformed using the natural logarithm.
Methodology
The project uses Python for data cleaning, exploratory analysis, visualization, and regression analysis.
The main specification is a Weighted Least Squares (WLS) regression with heteroskedasticity-robust (HC1) standard errors.
The model controls for:
Firm size
Sector
Region
Firm age
Foreign ownership
Exporter status
Small firms are used as the reference category for firm size.
Descriptive Results
The final analytical sample contains 887 firms.
Firm size Observations Mean log productivity
Below 5 9 19.37
Small 400 18.33
Medium 305 18.21
Large 173 17.52
Mean log productivity declines from small to medium and large firms in the final analytical sample. The “Below 5” category contains only 9 observations and should therefore be interpreted cautiously.
Regression Results
The final WLS model has an R² of 0.094 and is statistically significant overall (F-test p < 0.001).
Relative to small firms:
Medium firms: coefficient = −0.202, p = 0.403
Large firms: coefficient = −1.099, p = 0.0015
Below 5 firms: coefficient = +2.443, p = 0.087
The difference between large and small firms is statistically significant in the final specification. The coefficient corresponds to a lower level of measured productivity for large firms relative to small firms, conditional on the other variables included in the model.
Interpretation
The results indicate a negative association between large-firm status and measured labor productivity in this sample and specification.
Importantly, this analysis is observational. The results should therefore not be interpreted as evidence that becoming a larger firm causes productivity to decline. Differences between firms may reflect characteristics that are not fully captured by the available data.
The findings suggest that the relationship between firm size and productivity in Uzbekistan is not necessarily monotonic: larger firms do not automatically exhibit higher measured productivity than smaller firms.
Limitations
Several limitations should be considered:
The analysis uses cross-sectional firm-level data.
The results identify associations rather than causal effects.
Some firm-size categories have relatively small numbers of observations.
Productivity is measured using annual sales per employee.
Unobserved firm characteristics may influence the estimated relationship.
Tools
Python
pandas
NumPy
statsmodels
Matplotlib
Jupyter Notebook
Repository Structure
Bigger-Firms-Better-Productivity/
│
├── notebooks/
│   └── 01_data_exploration.ipynb
│
├── README.md
└── ...
Key Takeaway
The evidence from this analysis does not support a simple assumption that larger firms are necessarily more productive. In the final regression specification, large firms show significantly lower measured labor productivity than small firms, while the difference between medium and small firms is not statistically significant.
