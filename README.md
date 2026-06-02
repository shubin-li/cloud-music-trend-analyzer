# Cloud-Based Analysis of Music Aesthetic Trends

**CSC1142 Cloud Technologies | MSc in Computing (Data Analytics) | Autumn 2025 | Dublin City University**

An end-to-end data engineering and analytics pipeline built with Apache Spark / PySpark on Google Colab, investigating the divergence between critical acclaim (Grammy Awards) and commercial popularity (Spotify streaming data) in the music industry from 2000 to 2023.

## Motivation

Do Grammy-winning songs actually reflect what listeners enjoy? This project tackles that question by building three cohorts for comparison:

- **Critically Acclaimed** — "Record of the Year" Grammy winners (2000–2023)
- **Listener Hits** — Top 100 most popular songs per year on Spotify
- **General Population** — Baseline sample from the full Spotify catalogue (~1.16M tracks)

By comparing audio features (danceability, energy, valence, tempo, etc.) across these groups over a 23-year timeline, we reveal measurable "taste gaps" between industry recognition and listener preference.

## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Compute | Apache Spark 3.5.1 / PySpark | Distributed ETL, joins, window functions over 1.16M records |
| Environment | Google Colab + OpenJDK 11 | Interactive development and orchestration |
| Storage | Google Drive + Parquet | Schema-preserving columnar storage for reproducible datasets |
| Visualization | Pandas, Matplotlib, Seaborn, ipywidgets | Interactive EDA with dynamic feature/statistic switching |

## Pipeline Architecture

```
Grammy CSV (25K rows)          Spotify CSV (1.16M rows)
        │                              │
        ▼                              ▼
   ┌─────────┐                   ┌──────────┐
   │ Filter  │                   │  Parse   │
   │ 2000-23 │                   │  (quote/ │
   │ Record  │                   │  escape/ │
   │ of Year │                   │ multiLine│
   └────┬────┘                   │ options) │
        │                        └────┬─────┘
        ▼                             ▼
   ┌─────────┐    Normalize      ┌──────────┐
   │ Regex   │◄──────────────────│ Lowercase│
   │ Clean   │    & Merge        │ & Trim   │
   └────┬────┘                   └────┬─────┘
        │                             │
        ▼                             ▼
   ┌──────────────────────────────────────┐
   │  Entity Resolution                   │
   │  • Automated join on artist + track  │
   │  • Manual index mapping (41/54)      │
   └──────────────┬───────────────────────┘
                  │
         ┌────────┴────────┐
         ▼                 ▼
   Grammy Group      Spotify Group
   (130 records)     (2,400 records)
         │                 │
         ▼                 ▼
      Parquet           Parquet
         │                 │
         └────────┬────────┘
                  ▼
        Pandas Visualization
        (Interactive EDA)
```

## Key Technical Challenges

**1. Messy CSV Parsing**
The Spotify dataset contained track names with embedded quotes and line breaks (e.g. classical music movements), causing PySpark's default parser to shift columns. Solved with custom read options: `.option("quote", '"').option("escape", '"').option("multiLine", True)`.

**2. Cross-Dataset Entity Resolution**
Grammy and Spotify use different naming conventions for the same tracks (e.g. "Crazy in Love" vs "Crazy in Love (feat. Jay-Z)"). Applied a hybrid approach: regex normalization handled ~90% of matches, followed by manual index mapping for the remaining records to ensure analytical accuracy.

**3. Comparing Groups with Unequal Sample Sizes**
Grammy group (~5 winners/year) vs Spotify popular (100/year) vs general (thousands/year) created high volatility in visualizations. Addressed through both mean and median aggregation options in the interactive plots.

## Visualizations

- **Interactive Line Plot** — Compare any audio feature (danceability, valence, tempo, etc.) across all three cohorts over time, with toggleable Mean/Median aggregation
- **Stacked Bar Chart** — Genre distribution comparison revealing the "taste gap" between Grammy selections and streaming popularity

## Datasets

| Dataset | Source | Records |
|---|---|---|
| Grammy Winners & Nominees (1965–2024) | [Kaggle](https://www.kaggle.com/datasets/johnpendenque/grammywinners-and-nominees-from-1965-to-2024) | 25,370 |
| Spotify 1 Million Tracks | [Kaggle](https://www.kaggle.com/datasets/amitanshjoshi/spotify-1million-tracks) | 1,159,764 |

## Project Structure

```
├── CloudTechProject.ipynb    # Full pipeline: ETL + EDA + Visualization
├── CloudTechReport.pdf       # Technical report with methodology & findings
└── README.md
```

## How to Run

1. Open `CloudTechProject.ipynb` in Google Colab
2. Mount Google Drive and upload the two source CSVs to your Drive
3. Run all cells sequentially — the pipeline will:
   - Set up Spark environment (Java + PySpark)
   - Execute ETL pipeline producing Parquet outputs
   - Generate interactive visualizations
  
## My Contributions

This was a 2-person project. I was responsible for technical implementation:

- **ETL Pipeline:** Full PySpark environment setup, CSV parsing with custom 
  quote/escape/multiLine options for messy track names, regex-based artist & 
  track normalisation, and cross-dataset entity resolution (automated join + 
  manual index mapping for 41 of 54 unmatched Grammy records)
- **Cohort Construction:** Spark window functions and filtering logic to build 
  three analytical groups — Grammy winners, top-100 popular per year, and 
  general population baseline
- **Data Engineering:** Parquet output pipeline for schema-preserving columnar 
  storage, ensuring reproducibility across sessions
- **Interactive Visualisation:** All EDA plots including dual-axis time-series 
  with Mean/Median toggle (ipywidgets), genre distribution stacked bar charts, 
  and audio feature comparison across cohorts

## Key Findings

- Grammy winners consistently show **higher valence** (musical positiveness) than the general Spotify population
- **Dance** and **pop** dominate across all groups, but Grammy selections skew more toward **soul**, **folk**, and **blues**
- The "taste gap" between critical acclaim and streaming popularity is measurable and persistent across the 23-year period

## Authors

- Shubin Li
- Megha Kanojia
