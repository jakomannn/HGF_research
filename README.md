# High-Growth Firm Prediction Using Machine Learning

## About the Project

This repository contains an academic research project focused on predicting **High-Growth Firms (HGFs)** using machine learning methods.

The main idea of the study is to investigate whether financial and non-financial characteristics observed at the beginning of a growth period can be used to predict whether a firm will become a high-growth firm during the following three years.

The empirical analysis is based on firm-level panel data from the **World Bank Enterprise Surveys (WBES)**. The final dataset combines firms from nine countries and two comparable temporal cohorts.

Particular attention is paid to non-financial characteristics, including firm structure, ownership, exports, innovation, technology use, management practices, labour characteristics, access to finance, competition and the business environment.

## Research Objective

The objective of the study is to evaluate the predictive value of financial and non-financial firm characteristics for identifying future high-growth firms and to investigate whether non-financial information provides useful additional information for HGF prediction.

Growth is analysed separately using two indicators:

- growth in annual sales;
- growth in the number of employees.

Therefore, two independent target variables and analytical datasets are constructed:

- `hgf_sales`;
- `hgf_employees`.

The project currently uses **88 baseline features**, including:

- 26 financial features;
- 62 non-financial features.

The two HGF definitions are analysed separately because rapid sales growth does not necessarily imply rapid employment growth, and vice versa.

## Dataset

The study uses panel data from the **World Bank Enterprise Surveys (WBES)**.

The final sample contains firms from nine countries:

- Bosnia and Herzegovina;
- Bulgaria;
- Hungary;
- Montenegro;
- Morocco;
- Portugal;
- Ireland;
- Sweden;
- Tunisia.

Two groups of survey waves are used.

For Bosnia and Herzegovina, Bulgaria, Hungary, Montenegro, Morocco and Portugal:

```text
Baseline survey: 2019
Target survey:   2023
Growth period:   2019–2022
```

For Ireland, Sweden and Tunisia:

```text
Baseline survey: 2020
Target survey:   2024
Growth period:   2020–2023
```

Some original WBES panel files also contain earlier survey waves, such as 2009, 2013 or 2014. These observations are retained in the original `.dta` files but are **not used as baseline or target observations in the current research design**.

Firm characteristics used as predictors are taken exclusively from the baseline survey wave.

Information from the target wave is used to construct the HGF target variables. The variable `a20y` is used to verify the corresponding fiscal year but is not included as a model feature.

After matching firms and constructing the target variables, two independent datasets were obtained:

| Dataset | Firms | HGF | Non-HGF | HGF share |
| --- | ---: | ---: | ---: | ---: |
| `master_sales` | 1,240 | 461 | 779 | 37.18% |
| `master_employees` | 1,273 | 108 | 1,165 | 8.48% |

The datasets differ in size because the availability requirements for sales and employment growth are applied independently.

The original data are provided in Stata `.dta` format.

**Data source:** World Bank Enterprise Surveys, World Bank Group.

## HGF Definition

A **High-Growth Firm (HGF)** is defined using the average annualised growth rate over a three-year period.

The Compound Annual Growth Rate (CAGR) is calculated as:

```text
CAGR = (end_value / start_value)^(1/3) - 1
```

A firm is classified as an HGF if:

```text
CAGR > 10%
```

and the firm had at least:

```text
10 employees
```

at the beginning of the growth period.

This definition follows the current Eurostat approach to high-growth enterprises.

Two separate HGF indicators are constructed.

### Sales-based HGF

Sales growth is calculated using WBES sales variables:

```text
d2 — annual sales in the last completed fiscal year
n3 — annual sales three fiscal years earlier
```

A firm satisfying the HGF conditions is assigned:

```text
hgf_sales = 1
```

otherwise:

```text
hgf_sales = 0
```

### Employment-based HGF

Employment growth is calculated using:

```text
l1 — number of permanent full-time employees
l2 — number of permanent full-time employees three years earlier
```

A firm satisfying the HGF conditions is assigned:

```text
hgf_employees = 1
```

otherwise:

```text
hgf_employees = 0
```

Sales-based and employment-based HGFs are analysed independently.

## Prediction Periods

The study uses two comparable three-year growth periods.

### 2019–2022 cohort

For Bosnia and Herzegovina, Bulgaria, Hungary, Montenegro, Morocco and Portugal, predictors are taken from the **2019 WBES wave**.

The corresponding target observations are taken from the **2023 survey wave**.

The target survey reports the last completed fiscal year as 2022 and provides retrospective values for three years earlier. Therefore, HGF status is calculated for the financial period:

```text
2019 → 2022
```

The resulting design can be represented as:

```text
Firm characteristics in 2019
            ↓
     ML predictors X
            ↓
Growth during 2019–2022
            ↓
 hgf_sales / hgf_employees
```

### 2020–2023 cohort

For Ireland, Sweden and Tunisia, predictors are taken from the **2020 WBES wave**.

The target observations are taken from the **2024 survey wave**.

The last completed fiscal year corresponds to 2023, producing the growth period:

```text
2020 → 2023
```

The design is therefore:

```text
Firm characteristics in 2020
            ↓
     ML predictors X
            ↓
Growth during 2020–2023
            ↓
 hgf_sales / hgf_employees
```

The two cohorts are subsequently combined because they represent the same prediction task with the same three-year growth horizon and comparable baseline features.

## Research Pipeline

The current research workflow is:

```text
WBES panel datasets
        ↓
Selection of relevant survey waves
        ↓
Matching firms using panel identifiers
        ↓
Selection of baseline features
        ↓
Verification of fiscal years
        ↓
Construction of 3-year sales and employment growth
        ↓
Construction of hgf_sales and hgf_employees
        ↓
Creation of independent sales and employment datasets
        ↓
Missing-value analysis
        ↓
Creation of status features
        ↓
Train/test split
        ↓
Missing-value preprocessing
        ↓
Machine learning modelling
        ↓
Model evaluation
        ↓
Feature importance and interpretation
        ↓
Comparison of financial and non-financial information
```

The current feature set contains **88 potential predictors: 26 financial and 62 non-financial variables**.

The operational activity and infrastructure group was excluded from the final feature set because of comparability and availability limitations across countries and survey waves.

Missing WBES values are not treated automatically as ordinary numerical values. For selected variables, additional `status` features are constructed to distinguish observed values from different types of missing or unavailable responses.

A total of **38 status features** are currently generated for each analytical dataset.

Categorical and status variables are preserved as categorical information rather than automatically transformed using one-hot encoding. This allows them to be used directly by machine learning models capable of handling categorical features.

The datasets have already been divided into stratified training and test samples using an 80/20 split:

| Target | Train | Test | HGF train | HGF test |
| --- | ---: | ---: | ---: | ---: |
| Sales | 992 | 248 | 369 | 92 |
| Employees | 1,018 | 255 | 86 | 22 |

Stratification preserves the original class proportions, which is particularly important for the employment-based target because HGF firms represent a relatively small share of observations.

## Limitations

The study has several limitations related to the structure of WBES data and the research design.

First, WBES surveys are conducted in separate waves rather than annually. Only countries for which suitable panel observations and comparable survey waves were available could therefore be included.

Second, the availability and coding of variables differ between countries and survey waves. The feature set must consequently be restricted to variables that can be harmonised across the selected datasets.

Third, the amount of missing information differs substantially between variables. WBES also uses special response codes such as *Don't know*, *Does not apply* and other non-response categories. These cases cannot always be interpreted as ordinary random missing values and therefore require separate processing.

Fourth, the two target variables have substantially different class distributions. Sales-based HGF firms account for 37.18% of the corresponding sample, while employment-based HGF firms account for only 8.48%. Model performance must therefore be interpreted with regard to class imbalance.

Finally, firms from different countries operate in different institutional and macroeconomic environments. Combining them increases the sample size and allows common patterns to be studied, but country-specific effects may also influence the observed relationships.

## References

### Data

World Bank Group. **World Bank Enterprise Surveys (WBES)**.

The study uses panel observations from Bosnia and Herzegovina, Bulgaria, Hungary, Montenegro, Morocco, Portugal, Ireland, Sweden and Tunisia.

### HGF Methodology

Eurostat. **Business Demography Statistics: High-Growth Enterprises**.

The current Eurostat definition identifies high-growth enterprises as firms with at least 10 employees at the beginning of the growth period and an average annualised growth rate greater than 10% per year over three years.

### Machine Learning and HGF Prediction

Pekin, S., & Şengül, A. (2024). **The Good, the Better and the Challenging: Insights into Predicting High-Growth Firms Using Machine Learning**. *Borsa Istanbul Review*, 24, 47–60.

DOI: `10.1016/j.bir.2024.12.001`

## Author

**Yaroslav Filatov**

Applied Informatics student working on machine learning and firm-level data analysis.

GitHub: `jakomannn`