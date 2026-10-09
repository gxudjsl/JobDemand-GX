# JobDemand-GX

A city-level job demand dataset from **Guangxi, China**, covering **15 cities**,
**25 job categories**, and **60 months (2020-01 to 2024-12)**.

**This dataset is released in aggregated form only.** Each record is a
city × category × month aggregate; the underlying **655,499 raw job
advertisements are not part of this release**. Each observation unit
(city × category × month) carries the aggregated monthly job posting count
together with a 19-dimensional aggregate feature vector describing salary,
requirements, benefits, and employer characteristics.

## Dataset at a glance

| Item | Value |
|---|---|
| Region | 15 cities in Guangxi, China (incl. Nanning, Liuzhou, Guilin) |
| Job categories | 25 |
| Time span | 2020-01 – 2024-12 (monthly, 60 months) |
| Series (city × category) | 373 |
| Cells (series × month) | 22,380 |
| Raw advertisements aggregated | 655,499 (raw records not included in this release) |
| Features per cell | 19 aggregated features + posting count + JDI |

## Repository structure

```
JobDemand-GX/
├── dataset/
│   ├── demand/
│   │   └── demand_monthly.csv        # wide matrix: one row per series, one column per month
│   ├── features/
│   │   └── features_monthly.csv      # long table: 19-dim aggregated features per cell
│   └── entity_map/
│       ├── city_map.csv              # city_id ↔ city names (zh/en)
│       └── category_map.csv          # category_id ↔ keyword_id ↔ category names (zh/en)
├── docs/
│   └── FIELDS.md                     # field descriptions and keyword dictionaries
├── LICENSE
└── README.md
```

## Quick start

```python
import pandas as pd

# Wide demand matrix: rows = city-category series, columns = months
demand = pd.read_csv("dataset/demand/demand_monthly.csv", index_col=0)

# Long feature table
features = pd.read_csv("dataset/features/features_monthly.csv")
```

## Forecasting target

The Job Demand Intensity (JDI) is defined as:

```
JDI(c, j, t) = ln(1 + N(c, j, t))
```

where `N(c, j, t)` is the number of job postings of category `j` in city `c`
during month `t`. `log1p_position_count` in the feature table is the
precomputed JDI.

## Notes

- **Aggregated release.** The dataset is released only in aggregated
  city–category–month form. It was aggregated from 655,499 raw job
  advertisements, which are **not** included in this release; all counts and
  features in the tables are per-cell aggregates.
- **Unobserved cells.** `is_observed = 0` marks series-months not observed in
  the raw data. They carry `0` in the long feature table and empty values in
  the wide demand matrix; do not confuse them with observed zero-demand months.
- **Suggested split.** Following the associated paper, a chronological
  60% / 20% / 20% train/validation/test split is recommended.
- **Privacy.** The dataset is aggregated at the city–category–month level and
  contains no personal information, company names, or raw posting texts.

## 

```
