# Patient Recovery Analysis in Hospital

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Pandas](https://img.shields.io/badge/Library-Pandas-150458)
![Interface](https://img.shields.io/badge/Interface-CLI%20(Text--Based)-green)
![Program](https://img.shields.io/badge/Training-HCL%20Career%20Shaper-orange)

A modular, pure-Python command-line analytics tool (`patient_recovery_analyzer.py`) built to evaluate hospital recovery outcomes, benchmark departments and treatments, test cost/stay efficiency, and flag high-priority patients for clinical follow-up—without relying on external BI tools or visualization libraries.

---

## Table of Contents
- [Problem Statement](#problem-statement)
- [Key Findings](#key-findings)
- [Features](#features)
- [Project Structure](#project-structure)
- [Dataset Overview](#dataset-overview)
- [Getting Started](#getting-started)
- [Sample Demo & Triage Logic](#sample-demo--triage-logic)
- [Assumptions & Limitations](#assumptions--limitations)
- [Author & Acknowledgments](#author--acknowledgments)

---

## Problem Statement

Hospitals log thousands of patient visits across departments, treatments, age groups, and billing tiers. Relying on raw spreadsheets or isolated averages makes it difficult for clinical administrators to answer three core operational questions:
1. Is recovery quality consistent across departments and treatment types?
2. Do higher treatment costs or longer hospital stays lead to better recovery scores?
3. Which individual patients require immediate post-discharge intervention to prevent readmission?

This project solves that problem through an interactive Python CLI engine that transforms 5,000 hospital visit records into automated plain-English insights, structured performance tables, individual patient diagnostic cards, and an exportable **Critical Watchlist**.

---

## Key Findings

Analysis of all **5,000 visits** (January 1, 2022 – July 28, 2022; 0 missing values, 0 duplicate IDs):

| Metric | Value | Operational Takeaway |
| :--- | :---: | :--- |
| **Average Recovery Score** | `74.72 / 100` | 50.9% achieved Good/Excellent recovery (`75+`); 516 patients (10.3%) scored Low (`<60`). |
| **Average Readmission Risk** | `28.1%` | 506 patients (10.1%) carry High readmission risk ($\ge 50\%$). |
| **Average Length of Stay** | `4.06 days` | Median stay is `4.00 days` (range: `0.0` to `11.9` days). |
| **Average Treatment Cost** | `54,915.47` | Wide range (`746` to `119,307`) across all modalities. |
| **Critical Watchlist** | `51 patients (1.02%)` | Simultaneously have **Low Recovery (`<60`)** and **High Readmission Risk ($\ge 50\%$)**. |

### Core Insights
- **Uniform Departmental Care:** Average recovery sits tightly between **Neurology (`75.18`)** and **Cardiology (`74.15`)**—a gap of just `1.03` points—indicating consistent hospital-wide standards. **Orthopedics** handles the highest volume (`1,058` visits).
- **No Linear Link Between Cost/Stay and Recovery:** Pearson correlations are near zero for both Treatment Cost vs. Recovery ($r = -0.015$) and Length of Stay vs. Recovery ($r = +0.012$). Higher spending or longer stays do not automatically guarantee better recovery.
- **Primary Capacity Drivers:** **Routine visits (`68.6%`)** outnumber **Emergency visits (`31.4%`)**, and working-age adults (**`31-45`**) account for the largest patient share (`34.3%` / `1,716` visits).
- **Actionable Value Through Risk Flagging:** Because group averages are flat across departments, the highest operational value comes from patient-level triage—isolating the **51 Critical Watchlist patients** for immediate post-discharge follow-up.

---

## Features

`patient_recovery_analyzer.py` is built around 8 modular functions (`load_data`, 6 analytical modules, and a `main` controller loop):

1. **Automated Executive Report:** Prints a complete plain-English narrative covering global KPIs, top/bottom departments, patient load, correlations, and critical-risk counts.
2. **Department & Treatment Performance Tables:** Ranked multi-metric summary tables built with Pandas named aggregations (`.groupby().agg()`).
3. **Age Group & Visit Type Breakdown:** Demographic volume, percentage share, recovery scores, and risk profiles across cohorts.
4. **Critical Watchlist & CSV Exporter:** Displays the top 10 highest-urgency patients (sorted by highest readmission risk, then lowest recovery score) and exports all 51 critical cases to `critical_watchlist_patients.csv`.
5. **Patient Diagnostic Lookup (by ID):** Retrieves any patient (`1`–`5000`) with input validation and outputs a rule-based clinical discharge verdict (`CRITICAL`, `HIGH RISK`, `SLOW RECOVERY`, or `STABLE`).
6. **Case-Insensitive Department Query:** Filters live KPIs and identifies the most common treatment for any chosen department.

---

## Project Structure

```text
PATIENT_RECOVERY_ANALYSIS/
├── patient_recovery_analyzer.py                   # Main pure-Python CLI analytics application
├── healthcare_patient_analytics_seaborn.csv       # Raw dataset (5,000 rows, 12 columns)
├── critical_watchlist_patients.csv                # Generated export of 51 high-priority patients
├── SRS_Patient_Recovery_Analysis_Akshit_Singh.docx# Software Requirements Specification document
└── README.md                                      # Project documentation
```

---

## Dataset Overview

- **Source:** [Healthcare Patient Analytics Dataset on Kaggle](https://www.kaggle.com/datasets/abbas829/healthcare-patient-analytics-dataset)
- **Records:** `5,000` hourly hospital visits (`2022-01-01` to `2022-07-28`)
- **Original Columns (12):** `patient_id`, `visit_date`, `age_group`, `gender`, `region`, `department`, `treatment_type`, `visit_type`, `length_of_stay_days`, `treatment_cost`, `recovery_score`, `readmission_risk`
- **Engineered Columns (4):** `month`, `recovery_tier`, `risk_level`, `critical_watchlist`

---

## Getting Started

### Prerequisites
- **Python 3.10+**
- **Pandas** (installs NumPy automatically as a dependency)

### Installation & Execution

1. **Clone the repository:**
   ```bash
   git clone https://github.com/singhakshit1402-lgtm/PATIENT_RECOVERY_ANALYSIS.git
   cd PATIENT_RECOVERY_ANALYSIS
   ```

2. **Install Pandas:**
   ```bash
   pip install pandas
   ```

3. **Run the analyzer:**
   ```bash
   python patient_recovery_analyzer.py
   ```
   *(Ensure `healthcare_patient_analytics_seaborn.csv` is in the same directory as the script.)*

---

## Sample Demo & Triage Logic

### Rule-Based Discharge Verdicts (Option 5)
| Condition | Verdict |
| :--- | :--- |
| `recovery_score < 60` **AND** `readmission_risk >= 0.50` | `CRITICAL — Immediate post-discharge follow-up required` |
| `readmission_risk >= 0.50` | `HIGH RISK — Schedule 7-day checkup` |
| `recovery_score < 60` | `SLOW RECOVERY — Review treatment plan` |
| Otherwise | `STABLE — Standard discharge protocol` |

### Quick Test IDs
- **Patient ID `3545`** (Cardiology, `60+`, Emergency): Recovery `53/100`, Readmission Risk `83%` -> **`CRITICAL`**
- **Patient ID `2`** (Orthopedics, `60+`, Routine): Recovery `97/100`, Readmission Risk `11%` -> **`STABLE`**

---

## Assumptions & Limitations

- **Synthetic Data:** The source dataset is simulated for healthcare analytics training, which explains its clean hourly timestamps and near-zero numeric correlations.
- **Operational Thresholds:** Cutoffs for Low Recovery (`<60`) and High Risk ($\ge 50\%$) are rule-based demonstration thresholds that would be clinically calibrated in a live hospital environment.
- **Single-Visit Scope:** Each row represents an independent visit; longitudinal multi-visit histories and comorbidity records are not included.
- **Currency Unit:** No currency symbol is assumed because the raw dataset does not specify a monetary unit for `treatment_cost`.

---

## Author & Acknowledgments

- **Author:** Akshit Singh
- **Roll Number:** `2026B01010274`
- **Training Program:** HCL Career Shaper – 5-Day Data Analysis Training
- **Mentor:** Meenakshi
- **Repository:** [github.com/singhakshit1402-lgtm/PATIENT_RECOVERY_ANALYSIS](https://github.com/singhakshit1402-lgtm/PATIENT_RECOVERY_ANALYSIS)
