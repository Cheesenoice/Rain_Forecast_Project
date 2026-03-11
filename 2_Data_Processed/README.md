# Processed Data - Clean & Normalized Datasets

This directory contains meteorological data that has been **preprocessed, merged, and normalized**, ready for model training and evaluation.

## Overview

All raw ERA5 data has undergone:

- ✅ **Merging** from multiple time periods
- ✅ **Alignment** to a common spatial grid (65x33)
- ✅ **Normalization** using Z-score methodology
- ✅ **Splitting** into training, validation, and test sets

## Files

### 1. Training Set (2017-2022)

**File**: `train_2017_2022.nc`

- **Period**: Jan 1, 2017 - Dec 31, 2022
- **Duration**: 6 years
- **Size**: ~12 GB
- **Purpose**: Weight optimization and pattern learning
- **Steps**: ~26,280 time steps (2-hourly)

### 2. Validation Set (2023)

**File**: `val_2023.nc`

- **Period**: Jan 1, 2023 - Dec 31, 2023
- **Duration**: 1 year
- **Size**: ~2 GB
- **Purpose**: Hyperparameter tuning & Early slowing
- **Steps**: ~4,380 time steps

### 3. Test Set (2024)

**File**: `test_2024.nc`

- **Period**: Jan 1, 2024 - Dec 31, 2024
- **Duration**: 1 year (Unseen data)
- **Size**: ~2 GB
- **Purpose**: Final model evaluation
- **Steps**: ~4,368 time steps

### 4. Full Merged Dataset

**File**: `era5_vn_merged_normalized_2017_2024.nc`

- **Size**: ~18 GB
- **Purpose**: Complete reference, custom splits, or advanced analysis.

### 5. Normalization Statistics

**File**: `normalization_stats_2017_2024.json`
Contains mean and standard deviation for each variable computed over the 8-year period:

```json
{
  "t2m": { "mean": 298.47, "std": 4.78, "units": "K", "transform": "z-score" },
  "tp": { "mean": 0.0023, "std": 0.0056, "units": "m", "transform": "z-score" }
}
```

## Data Format

### NetCDF4 Structure

```
Dataset: train_2017_2022.nc
├── Dimensions: (time: 26280, lat: 65, lon: 33, features: 10)
├── Coordinates:
│   ├── time (float64): Unix timestamp
│   ├── latitude (float32): 8°N - 24°N
│   └── longitude (float32): 102°E - 110°E
└── Data Variables:
    ├── t2m, d2m, u10, v10, msl, sp, tp (TARGET), ssrd, skt, tcwv
```

## Variable Matrix

| Variable            | Abbr   | Unit  | Normalization | Range        |
| ------------------- | ------ | ----- | ------------- | ------------ |
| 2m Temperature      | t2m    | K     | Z-score       | 265-315 K    |
| Total Precipitation | **tp** | **m** | **Z-score**   | **0-0.05 m** |
| Water Vapour        | tcwv   | kg/m² | Z-score       | 5-70         |

## Spatial & Temporal Domain

### Geographic Scope

- **Vietnam & Neighboring Seas**
- **S-N**: 8°N - 24°N
- **W-E**: 102°E - 110°E
- **Grid**: 65 lat × 33 lon = 2,145 cells (~28km res)

### Time Splitting Strategy

- **Training**: 60% (2017-2022)
- **Validation**: 20% (2023)
- **Test**: 20% (2024)
- **Method**: Temporal split (preserves order, prevents leakage)

## Usage Example (Python)

```python
import xarray as xr
import json

# Load dataset
ds = xr.open_dataset('train_2017_2022.nc')

# Load stats for denormalization
with open('normalization_stats_2017_2024.json') as f:
    stats = json.load(f)

# Denormalize target
precip_original = ds['tp'].values * stats['tp']['std'] + stats['tp']['mean']
```

## Quality Assurance

- ✅ **Continuity**: No temporal gaps
- ✅ **Completeness**: 100% spatial coverage
- ✅ **Consistency**: Physically consistent across variables
- ✅ **Normalization**: Properly scaled for gradient-based learning

---

**Last Updated**: Dec 2025 | **Version**: 1.0 | **Status**: Production Ready
