# Melinda-s-repository

# # AFM 244 — Team Exercise: Nintendo Quarterly Revenue Forecast

A time-series regression analysis forecasting Nintendo Co., Ltd. (`NTDOY`) quarterly revenue, built for the AFM 244 Week 11 team exercise. The notebook fits a linear time trend plus two dummy variables, validates specifications with a train/test split and MAPE, and forecasts the next four fiscal quarters with prediction intervals.

## Overview

Nintendo's quarterly revenue shows two clear patterns: strong holiday-season seasonality and a structural step-up in revenue after the Nintendo Switch launched in March 2017. This project builds an OLS regression model that captures both effects, compares three specifications by out-of-sample accuracy, and uses the best-performing model to forecast revenue through the next four quarters.

## Data

- **Source:** `qSales_2024.csv`, quarterly sales data (Compustat-style fields) filtered to `tic == 'NTDOY'`.
- **Coverage:** 92 raw quarterly rows; the earliest 9 quarters (fiscal 2001–2003) are dropped because only a full-year figure was reported for those periods, leaving **83 clean quarterly observations** from fiscal 2004 Q2 through fiscal 2023 Q3.
- Nintendo's fiscal year ends March 31, so fiscal Q3 (`fqtr == 3`) corresponds to the October–December holiday shopping quarter.

## Methodology

1. **Visualize** quarterly revenue to identify patterns.
2. **Time trend:** a simple integer counter (`time = 1, 2, 3, ...`) across the 83 quarters.
3. **Dummy variables:**
   - `holiday_dv`: equals 1 for the holiday fiscal quarter (`fqtr == 3`), capturing seasonality.
   - `switch_dv`: equals 1 for all quarters from fiscal-quarter-end 2017-03-31 onward, capturing the post-Switch revenue level shift.
   - Both dummies are interacted with `time` so the trend slope can differ across regimes.
4. **Train/test split:** chronological 75% / 25% split (62 training quarters, 21 test quarters).
5. **Model comparison by test-set MAPE** (Mean Absolute Percentage Error):

   | Model | Variables | Test MAPE |
   |---|---|---|
   | 1 | Time only | 58.46% |
   | 2 | Time + holiday dummy | 66.76% |
   | 3 | Time + holiday dummy + Switch-era dummy | **18.83%** |

   Model 3 wins by a wide margin. Model 2 alone performs *worse* than the baseline because the test period falls almost entirely in the post-Switch era, and a model that only accounts for seasonality (without the level shift) systematically misjudges those quarters.
6. **Forecast:** the winning specification (Model 3) is refit on the full 83-quarter history and used to forecast the next four fiscal quarters (2024-03-31 through 2024-12-31), with an 80% prediction interval for each quarter.

## Results

The final forecast (revenue in USD millions):

| Quarter ending | Fiscal quarter | Forecast | 80% interval |
|---|---|---|---|
| 2024-03-31 | Q4 | ~2,833 | 1,078 – 4,589 |
| 2024-06-30 | Q1 | ~2,862 | 1,093 – 4,631 |
| 2024-09-30 | Q2 | ~2,890 | 1,107 – 4,673 |
| 2024-12-31 | Q3 (holiday) | ~5,740 | 3,871 – 7,610 |

The holiday-quarter spike and the wide prediction intervals both reflect genuine patterns and genuine uncertainty in the data, not modeling artifacts.

## Repository contents

```
.
├── AFM244_Week11_TeamExercise_Nintendo.ipynb   # main analysis notebook
├── qSales_2024.csv                             # source data (not included; see Data below)
└── README.md
```

> **Note:** `qSales_2024.csv` is the course-provided dataset and is not included in this repository. Place a copy in the same directory as the notebook before running it.

## Requirements

- Python 3.9+
- `pandas`
- `numpy`
- `statsmodels`
- `matplotlib`
- Jupyter (Notebook or Lab)

Install with:

```bash
pip install pandas numpy statsmodels matplotlib jupyter
```

## Running the notebook

1. Clone this repository and place `qSales_2024.csv` alongside the notebook.
2. Launch Jupyter:

   ```bash
   jupyter notebook AFM244_Week11_TeamExercise_Nintendo.ipynb
   ```

3. Run all cells in order, top to bottom.

## Limitations

Time-series regression forecasts are inherently less precise than in-sample fits, which is reflected in the width of the prediction intervals above. The model also cannot anticipate one-off events (a new console launch, a supply shock, a major shift in consumer spending) that aren't already captured by the two dummy variables. A future console generation would likely require adding a further dummy variable, the same way this model added one for the 2017 Switch launch.

## Course context

Built for AFM 244, Week 11 team exercise, following the time-series regression workflow introduced in class (linear trend + dummy variables + train/test validation via MAPE).
