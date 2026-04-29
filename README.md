# 🔍 SQL Data Cleaning + EDA — Brooklyn Nine-Nine Dataset

My first independent SQL project — no walkthroughs, no guides. Built the dataset from scratch, made my own mistakes, debugged them, and found real patterns in the data.

The goal was to apply data cleaning techniques independently and then move into exploratory data analysis — asking real questions and letting the data answer them.

---

## 🛠️ Tools Used

- **MySQL** — all queries written and executed in MySQL Workbench
- **Dataset** — custom-built Brooklyn Nine-Nine precinct dataset (2080 rows, 15 columns) covering detectives, crime cases, boroughs, salaries, and episode data across 8 seasons

---

## 🧹 Part 1 — Data Cleaning

### 1. Removing Duplicates
- Identified ~80 duplicate rows hidden across the dataset
- Used `ROW_NUMBER()` with `PARTITION BY` to detect exact duplicates
- Removed safely, bringing total rows from 2080 → 1447

### 2. Standardizing Data
- Stripped `$` signs and commas from salary column using nested `REPLACE()`
- Standardized inconsistent casing across `detective_name`, `crime_type`, `borough`, `rank`, and `case_status` using `UPPER()`
- Fixed typos manually — `BURGLERY → BURGLARY`, `ROBERRY → ROBBERY`, `ASAULT → ASSAULT`, `VANDALISME → VANDALISM`
- Unified duplicate statuses — `ONGOING` and `IN PROGRESS` → `UNDER INVESTIGATION`
- Fixed double spaces in `STATEN ISLAND` and `DRUG POSSESSION` using `REPLACE()`
- Built a new `date_clean` column from four mixed date formats using `REGEXP` + `STR_TO_DATE()` + `CASE WHEN`, then dropped the original column

### 3. Handling Null & Bad Values
- Converted all empty strings to proper `NULL` across columns
- Found salary outliers (`-1`, `0`, `9999999`) using `ORDER BY column + 0` to sort text as numbers — then nulled them out
- Used domain knowledge to fill missing detective names — if `rank = CAPTAIN` and name was NULL, it can only be Raymond Holt
- Converted `annual_salary_usd` from TEXT → INT using `ALTER TABLE` + `MODIFY COLUMN`

---

## 🔍 Part 2 — Exploratory Data Analysis

Questions were written as comments first, then answered with queries — every finding recorded alongside the query that found it.

### Dataset Overview

```sql
SELECT COUNT(DISTINCT detective_name),
       COUNT(DISTINCT rank),
       COUNT(DISTINCT crime_type),
       COUNT(DISTINCT borough),
       COUNT(DISTINCT case_status)
FROM b99_d2_rn;
```

| Dimension | Count |
|-----------|-------|
| Detectives | 9 |
| Ranks | 4 |
| Crime Types | 13 |
| Boroughs | 5 |
| Case Statuses | 7 |

### Key Findings

| Question | Finding |
|----------|---------|
| Who holds Detective rank? | Jake Peralta, Amy Santiago, Rosa Diaz, Charles Boyle, Michael Hitchcock, Norm Scully |
| Highest crime type | MURDER — 164 cases |
| Lowest crime type | ROBBERY — 28 cases |
| Highest crime borough | QUEENS — 295 cases |
| Lowest crime borough | BROOKLYN — 282 cases |
| Dataset date range | 2013-01-01 to 2021-12-27 (9 years) |
| Most cases solved | Terry Jeffords — 9,019 |
| Least cases solved | Charles Boyle — 4,829 |
| Most murder cases solved | Terry Jeffords — 24 |
| Least murder cases solved | Michael Hitchcock — 8 |
| Does more hours = more cases? | Mostly yes — Terry Jeffords worked 9,955.5 hrs and solved the most cases |

### Insight
Hours worked generally correlates with cases solved — but it's not a perfect relationship. Charles Boyle worked the fewest hours yet didn't have the least cases solved, suggesting some detectives are more efficient regardless of time on the job.

---

## 📂 Files

| File | What it does |
|------|-------------|
| `EDA-QUERIES.sql` | All exploratory queries — distributions, comparisons, detective performance |
| `EDA-Q&A.sql` | Same queries with findings recorded as comments — the story the data told |
| `brooklyn_99_dataset.csv` | Raw original dataset (messy) |
| `DATA_CLEANING_COMPLETION.csv` | Final cleaned dataset |

---

## 💡 Key Techniques Used

`ROW_NUMBER()` · `PARTITION BY` · `REPLACE()` · `UPPER()` · `CAST()` · `ALTER TABLE` · `MODIFY COLUMN` · `REGEXP` · `STR_TO_DATE()` · `CASE WHEN` · `GROUP BY` · `ORDER BY DESC` · `COUNT(DISTINCT)` · `SUM()` · `WHERE` · `IS NULL` · `IN()`
