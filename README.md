# quickpay-fintech-case-study-Asg_masai-

## Student Name
Ayush Chandnakhede

## Student ID
bitsom_ftai_2601282 

## Public GitHub Repository Link
[https://github.com/ayushchandankhede/quickpay-fintech-case-study-Asg_masai]

---

## Tools Used

| Section | Tool |
|---|---|
| Spreadsheet (Part 1) | Python (pandas, openpyxl) / Excel or Google Sheets |
| SQL Analysis (Part 2) | SQLite / PostgreSQL |
| Python Pipeline (Parts 3 & 4) | Python 3, pandas, Jupyter Notebook |
| Dashboard (Part 5) | Looker Studio |

---

## Run Instructions

### Part 1 — Spreadsheet
Open `02_spreadsheet/spreadsheet_workbook.xlsx` in Excel or Google Sheets.
The workbook contains tabs for raw data, cleaned transactions, merchant risk summary, exchange rates, and summary KPIs.

### Part 2 — SQL
Load `01_data/processed/cleaned_transactions.csv` into any SQL environment (SQLite, PostgreSQL, BigQuery, etc.) as a table named `cleaned_transactions`.
Then run the queries from `03_sql/analysis_queries.sql` in order (Q1 through Q8).

### Parts 3 & 4 — Python Notebook
Requires Python 3 with pandas installed.
```bash
pip install pandas openpyxl jupyter
cd 04_python
jupyter notebook fintech_pipeline.ipynb
```
Run all cells top to bottom. Outputs are saved to `01_data/processed/`.

### Part 5 — Dashboard
Open the Looker Studio link in `05_visualization/dashboard_link.txt`.
The dashboard connects to the processed CSV files and provides KPIs, trends, and breakdown views.

---

## Repository Structure

```
quickpay-fintech-case-study/
├── README.md
├── 01_data/
│   ├── raw/
│   │   ├── transactions_raw.csv
│   │   ├── merchant_master.csv
│   │   ├── users.csv
│   │   ├── ledger.csv
│   │   ├── gateway.csv
│   │   ├── exchange_rates.csv
│   │   └── api_response_sample.json
│   └── processed/
│       ├── cleaned_transactions.csv
│       ├── merchant_risk_summary.csv
│       ├── missing_in_gateway.csv
│       ├── missing_in_ledger.csv
│       ├── amount_mismatches.csv
│       ├── status_mismatches.csv
│       ├── reconciliation_report.csv
│       ├── api_normalized.csv
│       ├── daily_summary.csv
│       ├── payment_method_breakdown.csv
│       ├── region_breakdown.csv
│       └── merchant_performance_summary.csv
├── 02_spreadsheet/
│   ├── spreadsheet_workbook.xlsx
│   └── spreadsheet_answers.md
├── 03_sql/
│   ├── analysis_queries.sql
│   └── sql_answers.md
├── 04_python/
│   ├── fintech_pipeline.ipynb
│   └── summary_metrics.json
└── 05_visualization/
    └── dashboard_link.txt
```
