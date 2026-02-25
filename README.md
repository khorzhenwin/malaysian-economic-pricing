# Malaysian Economic Pricing Dataset

This project analyzes Malaysia's **PriceCatcher** consumer pricing data from `data.gov.my` and builds a joined analysis dataset for exploratory data analysis (EDA) and downstream cleaning.

## Source Data

The dataset is built from three official source tables:

- `pricecatcher` (transactional records):  
  https://data.gov.my/data-catalogue/pricecatcher
- `lookup_item` (item lookup):  
  https://data.gov.my/data-catalogue/lookup_item
- `lookup_premise` (premise lookup):  
  https://data.gov.my/data-catalogue/lookup_premise

Direct files currently used in the notebook:

- `https://storage.data.gov.my/pricecatcher/pricecatcher_2026-02.parquet`
- `https://storage.data.gov.my/pricecatcher/lookup_item.parquet`
- `https://storage.data.gov.my/pricecatcher/lookup_premise.parquet`

## Data Model and Joins

The analysis table is created by:

1. Starting from `pricecatcher` as the base transactional table.
2. Left-joining `lookup_item` on `item_code`.
3. Left-joining `lookup_premise` on `premise_code`.

This preserves all transaction rows while enriching them with item and premise metadata.

## EDA and Cleaning Workflow (Current)

Implemented in `consumer-pricing.ipynb`:

1. Load and join source tables.
2. Save raw joined dataset under `data/`.
3. Filter out rows with missing `item_category` (main analysis facet).
4. Inspect date range and missingness.
5. Review cardinality for selected fields (`item`, `unit`, `price`).
6. Drop technical/granular columns for analysis (`item_code`, `premise_code`, `address`).
7. Normalize `unit` text (e.g. `340 g` -> `340g`).
8. Derive:
   - `unit_in_kg` (for kg/g compatible units)
   - `price_per_kg` (`price / unit_in_kg`)
9. Run second-pass parsing for complex units (for example `5x79g`, `+-450g`) to recover more convertible rows.

## Folder Convention

- `data/`: raw and intermediate analysis artifacts.
- `cleaned/`: finalized cleaned and normalized datasets only.

## Notes and Caveats

- Units like `biji`, `ml`, `liter`, and other non-mass units may remain non-convertible for `unit_in_kg` unless additional assumptions are introduced.
- PriceCatcher is useful for high-frequency price surveillance, but should complement (not replace) official CPI analysis.
- Source data license: **CC BY 4.0** (as documented on data.gov.my pages).
