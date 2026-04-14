# Patient Readmission Prediction

This application predicts hospital patient readmission using a machine learning model trained on patient encounter data. It provides both single-patient and batch prediction capabilities.

## Getting Started

1. Install the application from the Snowflake Marketplace.
2. Open the application — the Streamlit UI will launch automatically.
3. For **single predictions**, use the "Single Prediction" tab and fill in patient details.
4. For **batch predictions**, connect your patient table (and optionally a results table) using the buttons on the "Batch Prediction" tab. The app will prompt you through the standard Snowsight permissions flow.

## References Required

| Reference | Object Type | Privileges | Purpose |
|---|---|---|---|
| Consumer Patient Table | TABLE | SELECT | Source patient data for batch predictions |
| Consumer Results Table | TABLE | SELECT, INSERT | Destination for prediction results |

When you open the Batch Prediction tab, the app will prompt you to connect each table through the standard Snowsight permissions dialog. Click the **"Connect Patient Table"** and **"Connect Results Table"** buttons to grant access.

## Input Patient Table Schema

Your patient table **must** contain the following columns with **exact names and accepted values** as shown below. All VARCHAR values are **case-sensitive**.

| Column | Type | Description | Accepted Values |
|---|---|---|---|
| `AGE` | VARCHAR | Age group bracket | `[0-10)`, `[10-20)`, `[20-30)`, `[30-40)`, `[40-50)`, `[50-60)`, `[60-70)`, `[70-80)`, `[80-90)`, `[90-100)` |
| `A1CTEST` | VARCHAR | HbA1c test result | `none`, `normal`, `high` |
| `CHANGE` | VARCHAR | Medication changed during encounter | `no`, `yes` |
| `DIABETES_MED` | VARCHAR | Diabetes medication prescribed | `no`, `yes` |
| `DIAG_1` | VARCHAR | Primary diagnosis group | `Diag_0` through `Diag_49` (format: `Diag_<number>`) |
| `DIAG_2` | VARCHAR | Secondary diagnosis group | `Diag_0` through `Diag_39` (format: `Diag_<number>`) |
| `DIAG_3` | VARCHAR | Tertiary diagnosis group | `Diag_0` through `Diag_29` (format: `Diag_<number>`) |
| `GLUCOSE_TEST` | VARCHAR | Serum glucose test result | `none`, `normal`, `high` |
| `MEDICAL_SPECIALTY` | VARCHAR | Admitting physician specialty | `Cardiology`, `InternalMedicine`, `Surgery`, `Emergency`, `Family/GeneralPractice` |
| `N_LAB_PROCEDURES` | NUMBER | Count of lab procedures performed | Integer >= 0 |
| `N_PROCEDURES` | NUMBER | Count of medical procedures performed | Integer >= 0 |
| `N_MEDICATIONS` | NUMBER | Count of medications administered | Integer >= 1 |
| `N_OUTPATIENT` | NUMBER | Outpatient visits in prior year | Integer >= 0 |
| `N_INPATIENT` | NUMBER | Inpatient visits in prior year | Integer >= 0 |
| `N_EMERGENCY` | NUMBER | Emergency visits in prior year | Integer >= 0 |
| `TIME_IN_HOSPITAL` | NUMBER | Length of hospital stay in days | Integer >= 1 |

### Example Input Data

```sql
SELECT
  '[50-60)'          AS AGE,
  'none'             AS A1CTEST,
  'yes'              AS CHANGE,
  'yes'              AS DIABETES_MED,
  'Diag_14'          AS DIAG_1,
  'Diag_5'           AS DIAG_2,
  'Diag_22'          AS DIAG_3,
  'normal'           AS GLUCOSE_TEST,
  'InternalMedicine' AS MEDICAL_SPECIALTY,
  45                 AS N_LAB_PROCEDURES,
  2                  AS N_PROCEDURES,
  15                 AS N_MEDICATIONS,
  1                  AS N_OUTPATIENT,
  0                  AS N_INPATIENT,
  0                  AS N_EMERGENCY,
  5                  AS TIME_IN_HOSPITAL;
```

## Output Results Table Schema

Your results table **must** be created with the following schema before binding it to the app. The app writes prediction results using `INSERT` — it will not create or alter the table.

```sql
CREATE TABLE my_results_table (
    AGE                    VARCHAR,
    A1CTEST                VARCHAR,
    CHANGE                 VARCHAR,
    DIABETES_MED           VARCHAR,
    DIAG_1                 VARCHAR,
    DIAG_2                 VARCHAR,
    DIAG_3                 VARCHAR,
    GLUCOSE_TEST           VARCHAR,
    MEDICAL_SPECIALTY      VARCHAR,
    N_LAB_PROCEDURES       NUMBER,
    N_PROCEDURES           NUMBER,
    N_MEDICATIONS          NUMBER,
    N_OUTPATIENT           NUMBER,
    N_INPATIENT            NUMBER,
    N_EMERGENCY            NUMBER,
    TIME_IN_HOSPITAL       NUMBER,
    PREDICTION             NUMBER,
    PREDICTION_LABEL       VARCHAR,
    READMIT_PROBABILITY    FLOAT,
    NO_READMIT_PROBABILITY FLOAT
);
```

| Column | Type | Description |
|---|---|---|
| `AGE` | VARCHAR | Age group (echoed from input) |
| `A1CTEST` | VARCHAR | A1C test result (echoed from input) |
| `CHANGE` | VARCHAR | Medication change flag (echoed from input) |
| `DIABETES_MED` | VARCHAR | Diabetes medication flag (echoed from input) |
| `DIAG_1` | VARCHAR | Primary diagnosis group (echoed from input) |
| `DIAG_2` | VARCHAR | Secondary diagnosis group (echoed from input) |
| `DIAG_3` | VARCHAR | Tertiary diagnosis group (echoed from input) |
| `GLUCOSE_TEST` | VARCHAR | Glucose test result (echoed from input) |
| `MEDICAL_SPECIALTY` | VARCHAR | Physician specialty (echoed from input) |
| `N_LAB_PROCEDURES` | NUMBER | Lab procedure count (echoed from input) |
| `N_PROCEDURES` | NUMBER | Procedure count (echoed from input) |
| `N_MEDICATIONS` | NUMBER | Medication count (echoed from input) |
| `N_OUTPATIENT` | NUMBER | Outpatient visits (echoed from input) |
| `N_INPATIENT` | NUMBER | Inpatient visits (echoed from input) |
| `N_EMERGENCY` | NUMBER | Emergency visits (echoed from input) |
| `TIME_IN_HOSPITAL` | NUMBER | Hospital stay in days (echoed from input) |
| `PREDICTION` | NUMBER | Model prediction: `1` = Readmission, `0` = No Readmission |
| `PREDICTION_LABEL` | VARCHAR | Human-readable label: `Readmission` or `No Readmission` |
| `READMIT_PROBABILITY` | FLOAT | Probability of readmission (0.0 to 1.0) |
| `NO_READMIT_PROBABILITY` | FLOAT | Probability of no readmission (0.0 to 1.0) |

> **Note:** All 16 input columns are echoed into the results table alongside the 4 prediction columns, so you can trace each prediction back to its input record.
