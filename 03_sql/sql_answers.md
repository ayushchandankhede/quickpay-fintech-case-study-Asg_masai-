# SQL Answers

## Q1
### Query
```sql
SELECT status, COUNT(*) AS transaction_count
FROM cleaned_transactions
GROUP BY status
ORDER BY transaction_count DESC;
```
### Result Summary
| status | transaction_count |
|---|---|
| captured | 19 |
| failed | 7 |
| chargeback | 4 |

The majority of transactions (19 out of 30) were successfully captured. 7 transactions failed and 4 resulted in chargebacks.

---

## Q2
### Query
```sql
SELECT merchant_name, ROUND(SUM(amount_usd), 2) AS captured_gmv_usd
FROM cleaned_transactions
WHERE status = 'captured'
GROUP BY merchant_name
ORDER BY captured_gmv_usd DESC;
```
### Result Summary
| merchant_name | captured_gmv_usd |
|---|---|
| Beta Stores | 33431.00 |
| Alpha Mart | 29984.50 |
| Delta Travels | 10300.00 |
| City Pharma | 8640.00 |

Beta Stores leads in captured GMV at $33,431. Eco Home had no captured transactions. Total confirmed GMV across all merchants is $82,355.50.

---

## Q3
### Query
```sql
SELECT merchant_name, ROUND(SUM(amount_usd), 2) AS captured_gmv_usd,
       COUNT(*) AS captured_transactions
FROM cleaned_transactions
WHERE status = 'captured'
GROUP BY merchant_name
ORDER BY captured_gmv_usd DESC
LIMIT 10;
```
### Result Summary
| merchant_name | captured_gmv_usd | captured_transactions |
|---|---|---|
| Beta Stores | 33431.00 | 7 |
| Alpha Mart | 29984.50 | 8 |
| Delta Travels | 10300.00 | 2 |
| City Pharma | 8640.00 | 2 |

Only 4 merchants exist in the dataset. Beta Stores ranks first by GMV despite fewer transactions than Alpha Mart, indicating higher average transaction values.

---

## Q4
### Query
```sql
SELECT transaction_date, ROUND(SUM(amount_usd), 2) AS daily_gmv_usd,
       COUNT(CASE WHEN status = 'captured' THEN 1 END) AS successful_transactions
FROM cleaned_transactions
GROUP BY transaction_date
ORDER BY transaction_date;
```
### Result Summary
| transaction_date | daily_gmv_usd | successful_transactions |
|---|---|---|
| 2026-03-01 | 26382.00 | 5 |
| 2026-03-02 | 25049.00 | 3 |
| 2026-03-03 | 18391.00 | 4 |
| 2026-03-04 | 16420.00 | 4 |
| 2026-03-05 | 19232.00 | 1 |
| 2026-03-06 | 10606.00 | 2 |

GMV peaked on 2026-03-01. A significant drop in successful transactions on 2026-03-05 (only 1 out of 6) was driven by multiple failed and chargeback transactions from user U008.

---

## Q5
### Query
```sql
SELECT merchant_name, COUNT(*) AS total_transactions,
       COUNT(CASE WHEN status = 'chargeback' THEN 1 END) AS chargeback_count,
       ROUND(COUNT(CASE WHEN status = 'chargeback' THEN 1 END) * 100.0 / COUNT(*), 2) AS chargeback_ratio_pct
FROM cleaned_transactions
GROUP BY merchant_name
HAVING chargeback_ratio_pct > 1
ORDER BY chargeback_ratio_pct DESC;
```
### Result Summary
| merchant_name | total_transactions | chargeback_count | chargeback_ratio_pct |
|---|---|---|---|
| Eco Home | 2 | 1 | 50.00 |
| Delta Travels | 4 | 1 | 25.00 |
| Beta Stores | 11 | 1 | 9.09 |
| Alpha Mart | 11 | 1 | 9.09 |

All 4 merchants with chargebacks exceed the 1% threshold. Eco Home has the highest ratio (50%) due to a small transaction volume. Every merchant in the dataset requires chargeback monitoring.

---

## Q6
### Query
```sql
SELECT gateway_region, COUNT(*) AS transaction_count,
       ROUND(AVG(risk_score), 2) AS avg_risk_score
FROM cleaned_transactions
WHERE risk_score IS NOT NULL
GROUP BY gateway_region
HAVING avg_risk_score > 50 AND transaction_count > 20
ORDER BY avg_risk_score DESC;
```
### Result Summary
| gateway_region | transaction_count | avg_risk_score |
|---|---|---|
| APAC | 21 | 65.48 |

Only the APAC region meets both conditions (avg risk score > 50 and more than 20 transactions). EU and US regions each have only 4 transactions, so they do not meet the volume threshold. APAC warrants additional risk monitoring given its high volume and elevated average risk score.

---

## Q7
### Query
```sql
SELECT user_id, transaction_date, COUNT(*) AS failed_or_chargeback_count
FROM cleaned_transactions
WHERE status IN ('failed', 'chargeback')
GROUP BY user_id, transaction_date
HAVING COUNT(*) >= 3
ORDER BY failed_or_chargeback_count DESC;
```
### Result Summary
| user_id | transaction_date | failed_or_chargeback_count |
|---|---|---|
| U008 | 2026-03-05 | 4 |

User U008 (Ishaan Verma) had 4 failed or chargeback transactions on 2026-03-05 — a strong indicator of suspicious activity or payment method issues. This user should be flagged for review.

---

## Q8
### Query
```sql
SELECT merchant_name, COUNT(*) AS chargeback_count,
       COUNT(DISTINCT user_id) AS unique_affected_users,
       ROUND(SUM(amount_usd), 2) AS total_chargeback_amount_usd
FROM cleaned_transactions
WHERE status = 'chargeback'
GROUP BY merchant_name
ORDER BY chargeback_count DESC;
```
### Result Summary
| merchant_name | chargeback_count | unique_affected_users | total_chargeback_amount_usd |
|---|---|---|---|
| Eco Home | 1 | 1 | 6649.00 |
| Delta Travels | 1 | 1 | 2500.00 |
| Beta Stores | 1 | 1 | 1711.00 |
| Alpha Mart | 1 | 1 | 5400.00 |

Each merchant has exactly 1 chargeback affecting 1 unique user. Eco Home has the highest chargeback dollar value ($6,649) despite having only 2 total transactions. Total amount at risk from chargebacks across all merchants is $16,260.
