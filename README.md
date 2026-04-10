# Patient Readmission Prediction

The Patient Readmission Prediction accelerator helps healthcare organizations identify patients at high risk of hospital readmission using a pre-trained machine learning model built on clinical and administrative data.

## Key Features

- **Single Patient Prediction** — Enter individual patient details to get an instant readmission risk score with probability percentages.
- **Batch Prediction** — Connect your own Snowflake patient tables to run predictions across thousands of records at once.
- **No Data Movement** — All predictions run inside Snowflake. Your patient data never leaves your account.
- **Pre-Trained Model** — Random Forest classifier trained on 16 clinical features.

## How to Use

1. Install the app.
2. The Streamlit interface opens with two tabs: **Single Prediction** and **Batch Prediction**.

### Single Prediction

Enter patient details manually and click **Predict Readmission** to get a real-time prediction with probability scores.

### Batch Prediction

1. Go to the app **Settings** (gear icon) > **References** tab.
2. Grant SELECT access on your patient table (**consumer_patient_table** reference).
3. Optionally grant SELECT + INSERT access on a results table (**consumer_results_table** reference).
4. Return to the **Batch Prediction** tab, preview your data, and click **Run Batch Predictions**.
5. Download results as CSV or have them written to your results table.

## Required Table Schema

Your patient table must contain the following columns:

| Column | Type | Description |
|--------|------|-------------|
| AGE | VARCHAR | Age group of the patient (e.g. [50-60)) |
| A1CTEST | VARCHAR | A1C test result (none, normal, high) |
| CHANGE | VARCHAR | Whether medication was changed (yes, no) |
| DIABETES_MED | VARCHAR | Whether patient is on diabetes medication (yes, no) |
| DIAG_1 | VARCHAR | Primary diagnosis code |
| DIAG_2 | VARCHAR | Secondary diagnosis code |
| DIAG_3 | VARCHAR | Additional diagnosis code |
| GLUCOSE_TEST | VARCHAR | Glucose test result (none, normal, high) |
| MEDICAL_SPECIALTY | VARCHAR | Admitting medical specialty |
| N_LAB_PROCEDURES | NUMBER | Number of lab procedures performed |
| N_PROCEDURES | NUMBER | Number of procedures performed |
| N_MEDICATIONS | NUMBER | Number of medications administered |
| N_OUTPATIENT | NUMBER | Number of outpatient visits in prior year |
| N_INPATIENT | NUMBER | Number of inpatient visits in prior year |
| N_EMERGENCY | NUMBER | Number of emergency visits in prior year |
| TIME_IN_HOSPITAL | NUMBER | Length of hospital stay in days |

## Built For

Healthcare providers, hospital systems, health insurers, and clinical analytics teams looking to proactively reduce readmission rates, improve patient outcomes, and optimize resource allocation.

## Requirements

- A Snowflake account with the app installed
- For batch mode: a patient table matching the schema above

## About

Powered by **Boolean Data Systems**

We leverage Snowflake to plan and design emerging data architectures that facilitate incorporation of high-quality and flexible data. These solutions lower costs and enhance output, designed to transform smoothly as your enterprise, and your data continue to increase over time.
