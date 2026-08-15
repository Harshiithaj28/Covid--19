[README(1).md](https://github.com/user-attachments/files/31095151/README.1.md)
# India COVID-19 Analysis & Forecasting

## Project Overview

This project analyzes the COVID-19 pandemic in India, with a focus on:

- Temporal patterns across the first and second waves
- State-level case and case-fatality patterns
- Vaccination progress and vaccine-product shares
- Time-series forecasting of daily confirmed cases
- Comparison of SARIMA against a simple Naive MA7 baseline
- Forecast validation, residual diagnostics, limitations, and future improvements

The analysis uses data available up to **11 August 2021**.

---

## Key Findings

India experienced two major COVID-19 waves:

- **First-wave peak:** 17 September 2020 — 93,198 cases/day
- **Second-wave peak:** 9 May 2021 — 391,279 cases/day
- The second-wave peak was approximately **4.2×** the first-wave peak.
- Daily deaths increased from approximately **1.2K/day** during the first peak to around **4K/day** during the second wave.
- By August 2021, daily cases had stabilized around **35–40K/day**.

At the 11 August 2021 cutoff:

- Confirmed cases: **32,036,511**
- Deaths: **429,179**
- Active cases: **386,351**
- National CFR: **1.34%**

### State-level observations

Maharashtra was the largest contributor in absolute cases:

- **6.36M cases**
- Approximately **20% of India's total**
- CFR: **2.11%**
- Second-wave state peak: **65,447 cases on 25 April 2021**

Kerala showed a contrasting pattern:

- **3.59M cases**
- CFR: **0.50%**
- Approximately **172K active cases** at the cutoff

The state peaks were concentrated between late April and late May 2021, indicating a highly synchronized nationwide second-wave surge.

---

## Vaccination Analysis

Between **16 January and 9 August 2021**, India administered approximately:

- **513.2M total doses**
- **400.2M first doses**
- **113.1M second doses**

Relative to a population of approximately 1.4 billion:

- About **29%** had received at least one dose
- About **8%** were fully vaccinated

Vaccine distribution:

| Vaccine | Doses | Share |
|---|---:|---:|
| Covishield | 446.8M | 87.7% |
| Covaxin | 62.4M | 12.2% |
| Sputnik V | ~0.5M | ~0.1% |

---

## Forecasting Methodology

The project uses:

**SARIMA(1,1,1)(1,1,1,7)**

The model includes:

- Non-seasonal AR term: `p = 1`
- First-order differencing: `d = 1`
- Non-seasonal MA term: `q = 1`
- Seasonal AR term: `P = 1`
- Seasonal differencing: `D = 1`
- Seasonal MA term: `Q = 1`
- Weekly seasonality: `s = 7`

The Augmented Dickey-Fuller test produced a **p-value of 0.0061**. However, because the pandemic contains major structural breaks between waves, differencing remains important for obtaining a suitable modeling regime.

---

## Forecasting Results

Two epidemic regimes were evaluated.

### Growth phase: 14–27 April 2021

| Model | MAPE |
|---|---:|
| SARIMA | **21.4%** |
| Naive MA7 | 46.2% |

SARIMA performed substantially better during rapid growth, reducing the MAPE by approximately 54% relative to the naive baseline.

### Plateau phase: 13 July–11 August 2021

| Model | MAPE |
|---|---:|
| SARIMA | 20.0% |
| Naive MA7 | **10.7%** |

During the stabilized phase, the simple Naive MA7 baseline performed better.

### 30-day hold-out validation

The additional 30-day hold-out evaluation produced:

| Model | MAE |
|---|---:|
| SARIMA | 7,742 |
| Naive MA7 | **3,630** |

This reinforces the importance of evaluating forecasting models across different epidemic regimes rather than relying on a single aggregate metric.

---

## Residual Diagnostics

Residual analysis identified several limitations:

1. **Heteroscedasticity**  
   Residual variance increases sharply during the March–May 2021 second-wave peak.

2. **Wide uncertainty during high volatility**  
   Standard SARIMA confidence intervals may be too narrow during periods of extreme variance.

3. **Residual autocorrelation**  
   The residual ACF shows remaining dependence, particularly around lags **3 and 6**.

These diagnostics indicate that SARIMA captures important temporal structure but does not fully explain the changing volatility and dependence in the pandemic data.

---

## Important Limitations

### State population data

State results are based largely on absolute counts. Without population normalization, comparisons between large and small states can be misleading.

### Reporting adjustments

Converting cumulative totals into daily increments can produce negative values when historical data are revised. Replacing these with zero using `clip(lower=0)` can systematically reduce the calculated mean.

### Missing positivity data

The positivity-rate variable has approximately **65% missingness**, so conclusions based on positivity should remain qualitative.

### CFR vs IFR

The case fatality ratio (CFR) should not be interpreted as the infection fatality ratio (IFR). Confirmed cases represented only a portion of total infections, particularly during 2020 when testing capacity was more limited.

### Univariate forecasting

The SARIMA model uses the historical case series without external predictors. Factors such as:

- Mobility
- Government stringency
- Vaccination coverage
- Hospitalizations
- Testing volume
- Public-health interventions

could provide additional predictive information.

---

## Future Work

Potential improvements include:

1. **Estimate effective reproduction number (`R_t`)**
   - Apply the Cori et al. methodology.

2. **Try LightGBM**
   - Build lagged features
   - Add rolling averages
   - Include calendar and seasonal variables

3. **Add exogenous variables**
   - Mobility indices
   - Stringency index
   - Vaccination rates
   - Hospitalizations
   - Testing volume

4. **Use rolling-origin validation**
   - Evaluate models over multiple historical forecasting windows.

5. **Evaluate interventions**
   - Use difference-in-differences across states to study lockdowns and other interventions.

6. **Population-normalized state analysis**
   - Calculate cases and deaths per 100,000 population.

---

## Project Structure

A recommended structure is:

```text
covid-19/
│
├── data/
│   └── covid_data.csv
│
├── notebooks/
│   └── covid_analysis.ipynb
│
├── src/
│   ├── preprocessing.py
│   ├── analysis.py
│   └── forecasting.py
│
├── outputs/
│   ├── forecast_results.csv
│   ├── figures/
│   └── reports/
│
├── requirements.txt
├── README.md
└── .gitignore
```

Adjust the filenames to match the actual files in the repository.

---

## Technologies Used

- **Python**
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Statsmodels
- Scikit-learn
- Jupyter Notebook

### Models

- SARIMA
- Naive moving-average baseline
- Future extension: LightGBM

### Evaluation Metrics

- MAPE
- MAE
- Residual ACF
- Forecast confidence intervals

---

## Running the Project

### 1. Clone the repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd covid-19
```

### 2. Create a virtual environment

Windows:

```bash
python -m venv .venv
.venv\Scripts\activate
```

Linux/macOS:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

If `requirements.txt` has not yet been created:

```bash
pip install pandas numpy matplotlib seaborn statsmodels scikit-learn jupyter
```

### 4. Run the notebook

```bash
jupyter notebook
```

Open the project notebook and execute the cells in order.

---

## Forecast Output

The forecasting workflow should save the final predictions and evaluation results in:

```text
outputs/forecast_results.csv
```

The output should contain the forecast date, actual values where available, predicted values, and relevant model/baseline information.

---

## Visualizations

The project includes visualizations for:

- First vs second COVID-19 waves
- SARIMA vs Naive MA7 forecasting
- 30-day hold-out validation
- 30-day future forecast
- Forecast confidence intervals
- SARIMA residuals
- Residual ACF
- Cumulative vaccination
- Vaccine-product shares
- State-level confirmed cases
- State-level CFR

---

## Main Conclusion

The most important result of this project is that **there is no universally superior forecasting model across all epidemic regimes**.

SARIMA performed better during the rapid growth of the second wave because trend and weekly seasonality contained useful predictive information. Once cases stabilized, the additional complexity of SARIMA became less useful and the simple Naive MA7 baseline performed better.

Therefore, forecasting performance should be evaluated using **regime-aware validation**, with model selection based on the underlying behavior of the time series.

---

## Report

A two-page analytical report containing the major findings, visualizations, forecasting results, diagnostics, limitations, and future directions is included separately with the project submission.

---

## Author

**Harshitha**  
CSE – Data Science  
Bangalore Institute of Technology

