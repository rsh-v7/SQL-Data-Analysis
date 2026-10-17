# SQL Data Analysis

Data cleaning and exploratory analysis in SQL: staging tables that keep raw data untouched, window-function deduplication, label standardization, self-join imputation of missing values, and analytical queries built on CTEs, ranking functions and running totals.

## Projects

<!--
Add your own-dataset project here, ABOVE the layoffs project, once it's done. Template:

### <Project title> — `<Folder_Name>/`

One or two sentences: what the data is, where it comes from, date range, size.

**Cleaning (`data_cleaning.sql`)**
- <problem in the raw data> → <how you fixed it, and the SQL feature used>
- ...

**Analysis (`analysis.sql`)**
- <question> → <query technique> → <one-line finding with a number>
- ...

**Data:** <source link>, <how to download>, <any size limits>.
-->

### Tech Layoffs (2020–2023) — `Tech_Layoffs_MySQL/`

Global tech-layoff records: company, location, industry, headcount laid off, percentage of workforce, date, funding stage, country and funds raised.

**Cleaning (`data_cleaning.sql`)**
- The raw table is copied into a staging table so the source data is never modified.
- Duplicates are identified with `ROW_NUMBER() OVER (PARTITION BY <every column>)`. MySQL can't delete through a CTE, so the row numbers are materialized into a second staging table and rows with `row_num > 1` are deleted there.
- Industry labels are standardized (`Crypto Currency` / `CryptoCurrency` → `Crypto`), trailing punctuation is trimmed from country names, and text dates are converted to a `DATE` column.
- Blank industries are set to `NULL` and then filled from other rows of the same company with a self-join `UPDATE`.
- Rows with neither `total_laid_off` nor `percentage_laid_off` are removed, and the helper column is dropped.

**Exploratory analysis (`eda.sql`)**
- Largest single layoff events, and companies that laid off 100% of staff ranked by funds raised.
- Total layoffs by company, location, country, industry, funding stage and year.
- Top three companies by layoffs in each year, using `DENSE_RANK()` over yearly totals.
- Monthly layoffs with a cumulative running total via `SUM() OVER (ORDER BY month)`.

**How to run**
1. MySQL 8.0+ (window functions are required).
2. Create a schema named `world_layoffs`.
3. Import `layoffs.csv` into a table named `layoffs` (MySQL Workbench → Table Data Import Wizard).
4. Run `data_cleaning.sql`, then `eda.sql`.

## Repository Structure

```
SQL-Data-Analysis/
├── README.md
└── Tech_Layoffs_MySQL/
    ├── layoffs.csv          # raw data snapshot
    ├── data_cleaning.sql    # staging, deduplication, standardization, nulls
    └── eda.sql              # aggregations, rankings, running totals
```

## Acknowledgements

The tech-layoffs cleaning and EDA workflow follows Alex The Analyst's MySQL tutorial series. Dataset: [Layoffs 2022](https://www.kaggle.com/datasets/swaptr/layoffs-2022) (Kaggle, swaptr).
