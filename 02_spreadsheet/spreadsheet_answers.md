# Spreadsheet Answers

## Cleaning Steps

1. **Stripped all leading and trailing whitespace** from every cell in the dataset — merchant names, status values, gateway regions, and risk scores all had extra spaces.
2. **Removed duplicate spacing** within merchant names (e.g., `"Alpha  Mart"` → `"Alpha Mart"`).
3. **Standardized date format** — all dates converted to `YYYY-MM-DD` (ISO 8601) format.
4. **Extracted and unified status values** — raw status column contained mixed formats such as `"Captured "`, `" CAPTURED"`, `"failed e05 timeout"`, `" chargeback "`. These were cleaned to one of three values: `captured`, `failed`, or `chargeback`.
5. **Extracted numeric risk scores** — the raw column used inconsistent formats: `score:62`, `risk-83`, `59 ` (with trailing space), plain integers. All were parsed to extract the numeric value only.
6. **Standardized gateway region** — values like `"APAC"`, `"apac"`, `" APAC "`, `"eu"`, `"us"` were all uppercased and trimmed to: `APAC`, `EU`, `US`.
7. **Filled missing gateway regions** from `merchant_master.csv` using the merchant's `default_region`.

---

## Standardization Rules

| Column | Rule Applied |
|---|---|
| `merchant_name` | Matched to canonical name in merchant_master using case-insensitive, whitespace-stripped comparison |
| `transaction_date` | Parsed to datetime, output as `YYYY-MM-DD` |
| `status` | Lowercased + keyword match: contains `captured` → `captured`; contains `failed` → `failed`; contains `chargeback` → `chargeback` |
| `risk_score` | Extracted first integer from string using regex; blank/null left as empty |
| `gateway_region` | Uppercased + stripped; blanks filled from `merchant_master.default_region` |

---

## Lookup and Enrichment Logic

- **Currency conversion**: Joined `transactions_raw.csv` with `exchange_rates.csv` on `transaction_date` and `currency`. Applied `amount_usd = raw_amount × usd_rate` for each row. Rates used: INR ≈ 0.0119–0.0121, EUR ≈ 1.07–1.09, USD = 1.0.
- **Merchant enrichment**: Left-joined cleaned transactions with `merchant_master.csv` on `merchant_name` to add `merchant_category` and `default_region`.

---

## Final Answers

| Metric | Value |
|---|---|
| Total raw rows | 30 |
| Total cleaned rows | 30 |
| Invalid or missing rows handled | 0 rows dropped; all 30 retained with values corrected in-place |
| Top region by GMV | APAC ($82,594.00 total GMV) |
| Number of high value transactions | 7 |
| Number of high risk transactions | 9 |
| Top merchant by captured GMV | Beta Stores ($33,431.00) |

---

## Formula Samples

**Amount in USD** (Excel formula):
```
=VLOOKUP(C2&D2, exchange_rates_lookup, 3, 0) * E2
```
*(where C2 = transaction_date, D2 = currency, E2 = raw_amount)*

**High Value Flag**:
```
=IF(AND(H2="APAC", K2>5000), 1, IF(AND(H2="EU", K2>6000), 1, IF(AND(H2="US", K2>7000), 1, 0)))
```
*(where H2 = gateway_region, K2 = amount_usd)*

**High Risk Flag**:
```
=IF(OR(J2>=70, I2="chargeback"), 1, 0)
```
*(where J2 = risk_score, I2 = status)*

**Chargeback Ratio per Merchant** (used in merchant_risk_summary):
```
=COUNTIFS(merchant_col, A2, status_col, "chargeback") / COUNTIF(merchant_col, A2)
```
