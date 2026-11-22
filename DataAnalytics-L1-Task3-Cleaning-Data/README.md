# NYC Airbnb Data Cleaning

A data cleaning project completed as part of the **Oasis Infobyte Data Analytics Internship (OIBSIP) — Data Analytics Level 1, Task 3**.

The project focuses on inspecting and cleaning the **NYC Airbnb Open Data 2019** dataset using Python. The goal was to improve data quality while preserving valid observations and documenting the decisions made during the cleaning process.

## Project Overview

- **Dataset:** NYC Airbnb Open Data 2019
- **Original size:** 48,895 rows × 16 columns
- **Cleaned size:** 48,884 rows × 17 columns
- **Record unit:** Each row represents an individual Airbnb listing.
- **Tools:** Python, Pandas, NumPy, Matplotlib, Seaborn, Jupyter Notebook

## Project Structure

```text
DataAnalytics-L1-Task3-Cleaning-Data/
├── data/
│   ├── raw/
│   │   └── AB_NYC_2019.csv
│   └── processed/
│       └── AB_NYC_2019_cleaned.csv
├── notebooks/
│   └── 01_initial_data_quality_inspection.ipynb
└── README.md
```

## Data Cleaning Workflow

The notebook follows an ordered data quality workflow:

1. **Dataset overview:** Loaded the CSV and inspected its structure, columns, data types, summary statistics, and missing values.
2. **Missing value analysis:** Reviewed missing values and applied context-based handling.
3. **Duplicate checks:** Checked complete duplicate records and listing IDs; repeated host IDs were retained because a host can manage multiple listings.
4. **Data type corrections:** Converted `id` and `host_id` to strings and `last_review` to datetime.
5. **Invalid value handling:** Identified and excluded 11 records with a price of zero. Checked minimum-night values, availability, geographic coordinates, and review dates against selected validation rules.
6. **Outlier investigation:** Used the Interquartile Range (IQR) method on selected numeric columns and investigated unusual observations.
7. **Text standardization:** Removed leading and trailing whitespace from listing names.
8. **Cross-column consistency:** Checked the relationship between review counts and last review dates, and checked identifier columns for invalid zero values.
9. **Feature engineering:** Added a `price_category` column with four groups: Low, Medium, High, and Luxury.
10. **Final validation:** Rechecked data quality rules and compared the original and cleaned datasets.
11. **Export:** Saved and reloaded the cleaned CSV to verify the exported file.

## Key Cleaning Decisions and Findings

### Missing Values

| Column | Missing in original | Handling |
|---|---:|---|
| `name` | 16 | Replaced with `Unknown Listing` |
| `host_name` | 21 | Replaced with `Unknown` |
| `last_review` | 10,052 | Retained as missing |
| `reviews_per_month` | 10,052 | Replaced with 0 |

The records missing both `last_review` and `reviews_per_month` had zero total reviews. Missing review dates were retained rather than assigning artificial dates. After the 11 zero-price records were excluded, 10,051 missing `last_review` values remained.

### Duplicate Records

- Complete duplicate records: **0**
- Duplicate listing IDs: **0**
- Repeated host IDs: **11,429**

Repeated host IDs were not treated as duplicates because one host may have multiple listings.

### Invalid Values and Validation

- **Price:** 11 zero-price records were excluded using `price > 0`. The cleaned dataset contains no zero or negative prices.
- **Minimum nights:** No values less than or equal to zero were found.
- **Availability:** All values were within the valid 0–365 day range.
- **Geographic coordinates:** Latitude and longitude were checked against broad preliminary screening ranges; no values fell outside the selected ranges.
- **Review dates:** Dates ranged from 2011-03-28 to 2019-07-08, with no dates beyond the latest observed date.
- **Text formatting:** 238 listing names had leading or trailing whitespace; these were standardized. No such whitespace was found in the other checked text columns.
- **Cross-column consistency:** No listing with a positive review count was missing its last review date.

### Outlier Investigation

Potential outliers were identified with the IQR method in five columns:

| Column | Potential outliers | Decision |
|---|---:|---|
| `price` | 2,972 | Retained and flagged |
| `minimum_nights` | 6,607 | Retained and flagged |
| `number_of_reviews` | 6,021 | Retained and flagged |
| `reviews_per_month` | 1,793 | Retained and flagged |
| `calculated_host_listings_count` | 7,081 | Retained and flagged |

The unusual values were investigated using the available listing and host information. Some patterns were consistent with long-term stays, popular listings, or hosts managing multiple properties. The dataset alone did not establish that these values were errors.

**Decision:** Potential outliers were retained. No automatic capping or removal was performed. The `availability_365` and geographic coordinate columns were validated using domain-specific checks rather than IQR.

## Before and After Summary

| Metric | Original dataset | Cleaned dataset |
|---|---:|---:|
| Rows | 48,895 | 48,884 |
| Columns | 16 | 17 |
| Duplicate records | 0 | 0 |
| Duplicate listing IDs | 0 | 0 |

- 11 zero-price records were excluded.
- One derived feature, `price_category`, was added.
- Missing values in selected columns were handled; missing `last_review` dates were intentionally retained.
- The cleaned dataset passed the selected final validation checks.

## Output

The cleaned dataset is available at:

`data/processed/AB_NYC_2019_cleaned.csv`

The exported CSV was loaded again with Pandas, and its verified shape was **48,884 rows × 17 columns**.

## How to Run

1. Clone this repository.
2. Open the `DataAnalytics-L1-Task3-Cleaning-Data` folder.
3. Install the required libraries:

   ```bash
   pip install pandas numpy matplotlib seaborn jupyter
   ```

4. Launch Jupyter Notebook and open `notebooks/01_initial_data_quality_inspection.ipynb`.
5. Run the notebook cells in order. The notebook reads the raw CSV from `data/raw/` and exports the cleaned dataset to `data/processed/`.

## Conclusion

This project demonstrates a structured data cleaning workflow using Python. Cleaning decisions were based on the observed data and documented validation checks. Potential outliers were investigated and retained when there was insufficient evidence to classify them as errors. The final cleaned dataset was exported and verified for subsequent analysis.
