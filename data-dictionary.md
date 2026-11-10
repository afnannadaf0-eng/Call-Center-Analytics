# Data Dictionary

Describes every table, column and KPI in `data/Call-Center-Analytics-Dashboard.xlsx`.

## 1. Workbook overview

| Sheet | Purpose |
|---|---|
| `Data` | Source data: two Excel Tables, `calls` and `customers` |
| `Customer Centre Report` | The final dashboard |
| `Pivots` | PivotTables and helper ranges that feed the dashboard charts |
| `Assets` | Representative images shown in the rep summary |

## 2. Table relationship

`calls` and `customers` are linked by **Customer ID** (many calls to one customer).

```
customers (15 rows)  1 ────── *  calls (1,000 rows)
  Customer ID  ───────────────────  Customer ID
```

## 3. `calls` table (1,000 rows, no missing values)

### Source columns

| Column | Type | Description | Values / range |
|---|---|---|---|
| Call number | Text | Unique call identifier | `Call_7271` onward |
| Customer ID | Text | Customer who made the call; links to `customers` | `C0001` to `C0015` |
| Duration | Integer | Length of the call in **minutes** | 2 to 176 |
| Representative | Text | Agent who handled the call | `R01` to `R05` |
| Date of Call | Date | Date the call took place | 01-Jan-2023 to 31-Dec-2023 |
| Purchase Amount | Integer | Purchase value linked to the call (USD) | 20 to 225 |
| Satisfaction Rating | Decimal | Customer rating of the call | 0.0 to 5.0 |

### Calculated columns

| Column | Formula | Description |
|---|---|---|
| FY | `IF(MONTH(date)<=6, YEAR(date), YEAR(date)+1)` | Fiscal year. Jan to Jun keeps the calendar year; Jul to Dec rolls to the next year. Result: 2023 (586 calls) or 2024 (414 calls) |
| Day of week | `TEXT(date, "DDDD")` | Weekday name (Sunday to Saturday) |
| Duration Bucket | Array formula on Duration | Groups calls into five ranges (below) |
| Rating rounded | `ROUND(Satisfaction Rating, 0)` | Rating rounded to a whole number, 0 to 5 |

**Duration Bucket ranges (minutes)**

| Bucket | Duration range | Calls |
|---|---|---|
| Under 10 mins | 2 to 10 | 9 |
| 10 to 30 mins | 11 to 30 | 51 |
| 30 to 60 mins | 31 to 60 | 179 |
| 1 to 2 hours | 61 to 120 | 524 |
| More than 2 hours | 121 to 176 | 237 |

**Rating rounded distribution**

| Rating | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|---|
| Calls | 1 | 8 | 59 | 197 | 428 | 307 |

## 4. `customers` table (15 rows)

| Column | Type | Description | Values |
|---|---|---|---|
| Customer ID | Text | Primary key | `C0001` to `C0015` |
| Gender | Text | Customer gender (the column header is spelled "Gedner" in the workbook) | Male (6), Female (9) |
| Age | Integer | Customer age in years | 22 to 43 |
| City | Text | Customer city | Cincinnati, Cleveland, Columbus |

## 5. Dashboard KPI definitions

| KPI card | Definition | Overall value |
|---|---|---|
| Calls | Count of calls | 1,000 |
| Amount | Sum of Purchase Amount | $96,623 |
| Duration | Sum of Duration (minutes) | 89,850 |
| Avg. Rating | Average of Satisfaction Rating | 3.9 |
| Happy Callers | Calls with Rating rounded = 5 (rating 4.5 or higher) | 307 |

Each card shows the overall value in large text and the selected representative's value in small text beneath it.

## 6. Data notes
- All 1,000 rows are complete; there are no blank cells in the `calls` table.
- One call has a rating of 0.0, which may mean "no rating given". It is included in the average rating as recorded.
- All calls are from calendar year 2023, so the **FY** column splits them into FY 2023 (Jan to Jun) and FY 2024 (Jul to Dec).
