# Data Preprocessing Pipeline

This directory contains the sequential workflow for converting raw ERA5 meteorological data into model-ready datasets.

## Pipeline Overview

```
Raw ERA5 Data (40GB)
    ↓
[1] Era5_Weather_Data_Download.ipynb
    ↓
Raw NetCDF files per variable/period
    ↓
[2] Data_Discovery.ipynb
    ↓
Quality checks, EDA, Statistical insights
    ↓
[3] Data_Merged_and_Normalized.ipynb
    ↓
2_Data_Processed/ (Normalized Train/Val/Test sets)
```

## Notebooks Detail

### 1. Era5_Weather_Data_Download.ipynb

**Purpose**: Automated download from Copernicus Climate Data Store.

- **Features**: Auto-resume interrupted downloads, error handling with backoff, parallel retrieval by variable.
- **Output**: `1_Data_Raw/` (NetCDF4 format).

### 2. Data_Discovery.ipynb

**Purpose**: Exploratory Data Analysis (EDA) of raw files.

- **Analyses**: Spatial grid verification (65x33), temporal continuity checks, variable correlations, unit validation.
- **Visuals**: Climatology maps, histogram distributions, time-series trends.

### 3. Data_Merged_and_Normalized.ipynb

**Purpose**: Processing raw files into final ML datasets.

- **Steps**: Spatial alignment, temporal merging, Z-score normalization, Train/Val/Test splitting.
- **Normalization**: Z-score calculation based on full dataset statistics (stored in JSON).
- **Split**: 2017-2022 (Train), 2023 (Val), 2024 (Test).

## Requirements

- `cdsapi`: For data downloads
- `xarray` & `dask`: For handling large multi-dimensional arrays
- ~50GB disk space for full pipeline execution

---

**Status**: ✅ Pipelines verified and complete.
