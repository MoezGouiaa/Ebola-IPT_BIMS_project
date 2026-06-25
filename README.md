Here is a GitHub-ready `README.md` that explains the workflow between the two notebooks and how to generate `insp_smoothed_incidence.csv`.

---

# Ebola 2026 Outbreak Data Preparation and SEIDdIndFR Modeling

This repository contains:

1. **Data preparation notebook** (`data preparation from Sitrep.ipynb`)
2. **Epidemiological model notebook** (`Ebola_SEIDdIndFR_Notebook.ipynb`)

The first notebook downloads and processes epidemiological data from the official BDBV2026 dataset and generates the file:

```text
insp_smoothed_incidence.csv
```

The second notebook uses this processed dataset to calibrate and simulate an Ebola transmission model.

---

## Repository Structure

```text
.
├── data preparation from Sitrep.ipynb
├── Ebola_SEIDdIndFR_Notebook.ipynb
├── data/
│   └── insp_smoothed_incidence.csv
└── README.md
```

---

# 1. Data Preparation

## Objective

The notebook `data preparation from Sitrep.ipynb` downloads national Ebola surveillance data and computes smoothed daily incidence curves for:

* Confirmed cases
* Confirmed deaths

The resulting dataset is saved as:

```text
insp_smoothed_incidence.csv
```

---

## Data Source

The notebook uses publicly available data from the BDBV2026 repository:

[BDBV2026 Data Repository](https://github.com/INRB-UMIE/BDBV2026-Data?utm_source=chatgpt.com)

Specifically:

* National cumulative confirmed cases
* National cumulative confirmed deaths

---

## Required Python Packages

Install the required dependencies:

```bash
pip install pandas numpy matplotlib
```

---

## Running the Notebook

Open:

```text
data preparation from Sitrep.ipynb
```

and run all cells sequentially.

The notebook will:

### Step 1 – Download cumulative data

```python
confirmed_cases = pd.read_csv(...)
confirmed_deaths = pd.read_csv(...)
```

### Step 2 – Compute daily incidence

Daily incidence is obtained from cumulative counts:

```python
new_cases = cumulative_cases.diff()
new_deaths = cumulative_deaths.diff()
```

### Step 3 – Apply 7-day moving average smoothing

```python
rolling(window=7, center=True, min_periods=1).mean()
```

This reduces reporting noise and day-to-day fluctuations.

### Step 4 – Create final dataset

The notebook merges:

* Smoothed confirmed cases
* Smoothed confirmed deaths

into a single dataframe.

---

## Output File

The final file contains:

| Column                    | Description                           |
| ------------------------- | ------------------------------------- |
| Date                      | Observation date                      |
| smoothed_confirmed_cases  | 7-day smoothed daily confirmed cases  |
| smoothed_confirmed_deaths | 7-day smoothed daily confirmed deaths |

Example:

| Date       | smoothed_confirmed_cases | smoothed_confirmed_deaths |
| ---------- | ------------------------ | ------------------------- |
| 2026-01-20 | 1.4                      | 0.1                       |
| 2026-01-21 | 1.7                      | 0.2                       |
| ...        | ...                      | ...                       |

---

## Saving the CSV

The original notebook saves the file using a local path:

```python
df.to_csv(
    '/your_location/insp_smoothed_incidence.csv'
)
```

For portability, it is recommended to replace it with:

```python
df.to_csv(
    'insp_smoothed_incidence.csv',
    index=False
)
```

or

```python
df.to_csv(
    'data/insp_smoothed_incidence.csv',
    index=False
)
```

---

# 2. Running the Epidemiological Model

The notebook:

```text
Ebola_SEIDdIndFR_Notebook.ipynb
```

uses the generated file:

```text
insp_smoothed_incidence.csv
```

as calibration data.

---

## Model Structure

The implemented model is:

```text
S  → E

E  → Id   (declared infectious)
E  → Ind  (undeclared infectious)

Id  → R
Id  → Dd

Ind → R
Ind → F

F → Dnd
```

Compartments:

| Symbol | Description           |
| ------ | --------------------- |
| S      | Susceptible           |
| E      | Exposed               |
| Id     | Declared infectious   |
| Ind    | Undeclared infectious |
| F      | Funeral compartment   |
| R      | Recovered             |
| Dd     | Declared deaths       |
| Dnd    | Undeclared deaths     |

---

## Input Data

The notebook loads:

```python
df = pd.read_csv("insp_smoothed_incidence.csv")
```

and uses:

```python
smoothed_confirmed_cases
smoothed_confirmed_deaths
```

for parameter estimation.

---

## Calibration Method

Parameters are estimated using:

* Differential Evolution optimization
* Poisson Negative Log-Likelihood objective function

The model is fitted simultaneously to:

* Daily confirmed cases
* Daily confirmed deaths

---

# Workflow Summary

```text
Raw BDBV2026 Sitrep Data
            │
            ▼
data preparation from Sitrep.ipynb
            │
            ▼
insp_smoothed_incidence.csv
            │
            ▼
Ebola_SEIDdIndFR_Notebook.ipynb
            │
            ▼
Model Calibration
            │
            ▼
Forecasts & Epidemiological Analysis
```

---

## Citation

If you use this repository, please cite the BDBV2026 surveillance data source:

[BDBV2026 Data Repository](https://github.com/INRB-UMIE/BDBV2026-Data?utm_source=chatgpt.com)

and acknowledge the Institut National de Recherche Biomédicale (INRB) outbreak reports used to construct the incidence dataset.
