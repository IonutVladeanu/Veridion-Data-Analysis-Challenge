# Supplier Data Quality & Entity Resolution – POC Findings and Recommendations

## Overview
This repository documents a **Proof of Concept (POC)** simulation for supplier database cleansing and entity resolution.  
The client scenario: a large manufacturing company whose **Procurement department** is hindered by a messy supplier database filled with duplicates, outdated records, and missing values. The objective was to demonstrate how entity resolution and systematic data quality checks can transform a noisy dataset into a reliable foundation for spend analysis and cost-saving strategies.

The project covers:
- **Entity Resolution:** Matching 591 input suppliers against Veridion candidates (up to 5 per input).  
- **Data Cleaning & Quality Control:** Addressing missing values, duplicates, outliers, distribution biases, and timeliness.  
- **Recommendations:** Actionable insights for making the dataset production-ready.  

---

## Key Results
- **89.2% MATCHED** (527 inputs)  
- **9.8% BEST_GUESS** (58 inputs, good candidates but weaker corroboration)  
- **1.0% UNMATCHED** (6 inputs, irrelevant or insufficient candidates)  

> Note: While results align closely with human judgment, the process is not 100% error-free. Human-in-the-loop review remains essential for low-confidence cases.

---

## Methodology

### 1. Entity Resolution
A transparent, rule-based algorithm was developed to mimic analyst decision-making:
- **Name matching** (normalized tokens, legal/commercial variants)  
- **Country confirmation** (ISO code and string)  
- **City/Region support**  
- **Website/domain validation**  
- **Industry coherence** (NAICS/NACE/SIC alignment)  
- **Sanity checks** on revenue and employee count  

**Scoring logic:**  
- Weighted signals (Name 0.60, Country 0.25, Website 0.20, City/Region 0.10)  
- Candidate classification into `CANDIDATE_MATCH` vs. `CANDIDATE_UNLIKELY`  
- Final status per input: `MATCHED`, `BEST_GUESS`, `UNMATCHED`  

### 2. Data Analysis & Quality Control
Systematic QC was applied to identify gaps and risks:
- **Missing Values:** Major gaps in `year_founded`, `revenue`, `employee_count`, and social media fields.  
- **Duplicates:** 180 duplicated Veridion IDs (e.g., Huawei, Telenor, Zalaris).  
- **Outliers:** Extreme anomalies in revenue and workforce size (Amazon, Deloitte, ISS).  
- **Distribution Checks:** Skewed toward IT/Telecom industries and Nordic/Asian geographies.  
- **Cross-Validation:** Revenue and employee counts often modelled, website data highly consistent.  
- **Timeliness:** 90% updated within the past year; none updated in the past 90 days.  
- **Coverage:** Websites (77%), phone numbers (75%), emails (61%), LinkedIn (45%).  

### 3. Data Cleaning
Targeted cleaning steps were applied:
- **Imputation:** Filled numeric gaps with medians, categorical with mode; added imputation flags.  
- **Outlier Treatment:** Winsorization at 1st/99th percentiles for revenue and employee_count.  
- **Deduplication:** Removed duplicate `veridion_id` entries, preserving first occurrence.  


