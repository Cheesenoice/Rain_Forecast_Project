# Raw Data - ERA5 Reanalysis Dataset

This directory contains raw meteorological data downloaded from the **Copernicus Climate Data Store (ERA5)**.

## Overview

- **Data Source**: ERA5 Reanalysis (ECMWF)
- **Time Range**: 2017-2024 (8 years)
- **Spatial Resolution**: 0.25° × 0.25°
- **Temporal Resolution**: 2-hourly (00:00, 02:00, 04:00, ..., 22:00)
- **Geographic Scope**: Vietnam (8°N-24°N, 102°E-110°E)
- **Format**: NetCDF4

## Directory Structure

```
era5_2017_2020/              # Period 1: 2017-2020
├── era5_vn_2m_temperature_2017_2020.nc
├── era5_vn_2m_dewpoint_temperature_2017_2020.nc
├── era5_vn_10m_u_component_of_wind_2017_2020.nc
├── era5_vn_10m_v_component_of_wind_2017_2020.nc
├── era5_vn_mean_sea_level_pressure_2017_2020.nc
├── era5_vn_surface_pressure_2017_2020.nc
├── era5_vn_total_precipitation_2017_2020.nc
├── era5_vn_surface_solar_radiation_downwards_2017_2020.nc
├── era5_vn_skin_temperature_2017_2020.nc
└── era5_vn_total_column_water_vapour_2017_2020.nc

era5_2021_2024/              # Period 2: 2021-2024
├── era5_vn_2m_temperature_2021_2024.nc
├── era5_vn_2m_dewpoint_temperature_2021_2024.nc
├── era5_vn_10m_u_component_of_wind_2021_2024.nc
├── era5_vn_10m_v_component_of_wind_2021_2024.nc
├── era5_vn_mean_sea_level_pressure_2021_2024.nc
├── era5_vn_surface_pressure_2021_2024.nc
├── era5_vn_total_precipitation_2021_2024.nc
├── era5_vn_surface_solar_radiation_downwards_2021_2024.nc
├── era5_vn_skin_temperature_2021_2024.nc
└── era5_vn_total_column_water_vapour_2021_2024.nc
```

## Variables Included

| Variable                          | Abbreviation | Units | Description                                  |
| --------------------------------- | ------------ | ----- | -------------------------------------------- |
| 2m Temperature                    | t2m          | K     | Air temperature at 2m height above surface   |
| 2m Dewpoint Temperature           | d2m          | K     | Dewpoint temperature at 2m height            |
| 10m U Wind Component              | u10          | m/s   | Eastward component of wind at 10m height     |
| 10m V Wind Component              | v10          | m/s   | Northward component of wind at 10m height    |
| Mean Sea Level Pressure           | msl          | Pa    | Pressure reduced to mean sea level           |
| Surface Pressure                  | sp           | Pa    | Pressure at the surface                      |
| Total Precipitation               | tp           | m     | Accumulated liquid and frozen water (Target) |
| Surface Solar Radiation Downwards | ssrd         | J/m²  | Solar radiation reaching the surface         |
| Skin Temperature                  | skt          | K     | Temperature of the surface layer             |
| Total Column Water Vapour         | tcwv         | kg/m² | Vertically integrated water vapour           |

## Data Characteristics

### Spatial Scope

- **North Boundary**: 24°N (Northern Mountains)
- **South Boundary**: 8°N (Southern Coast)
- **West Boundary**: 102°E (Laos Border)
- **East Boundary**: 110°E (South China Sea)
- **Grid Points**: 65 latitude × 33 longitude = 2,145 spatial locations

### Temporal Details

- **Time Span**: 2017-01-01 00:00 to 2024-12-31 22:00
- **Data Points**: ~34,944 time steps × 10 variables × 2,145 locations
- **Total Volume**: ~40 GB (uncompressed for both periods)

## Data Quality Notes

1. **Reanalysis Product**: ERA5 is a reanalysis dataset combining model data with observations.
2. **No Missing Values**: Copernicus ensures complete coverage with gap-filling.
3. **Known Issues**:
   - Seasonal rainfall may be underestimated in complex terrain.
   - High-altitude areas may have lower accuracy.
4. **Processing**: Individual variables are split by time periods to ensure download reliability.

## Data Access

### Option 1: Use Processed Data (Recommended)

If you only need the processed and normalized datasets:

```bash
cd ../2_Data_Processed/
# Use train_2017_2022.nc, val_2023.nc, test_2024.nc
```

### Option 2: Re-download Raw Data

To re-download from CDS:

```bash
jupyter notebook ../Data_Preprocessing_Notebooks/1_Era5_Weather_Data_Download.ipynb
```

Requirements:

- CDS API Key from https://cds.climate.copernicus.eu/
- ~50GB free disk space
- ~2-3 hours download time (connection dependent)

### Option 3: Direct CDS Query

```python
import cdsapi

client = cdsapi.Client()

client.retrieve(
    'reanalysis-era5-single-levels',
    {
        'product_type': 'reanalysis',
        'format': 'netcdf',
        'variable': ['2m_temperature', 'total_precipitation'],
        'year': ['2017', '2018', '2019', '2020'],
        'month': [f'{i:02d}' for i in range(1, 13)],
        'day': [f'{i:02d}' for i in range(1, 32)],
        'time': [f'{i:02d}:00' for i in range(0, 24, 2)],
        'area': [24, 102, 8, 110],  # North, West, South, East
    },
    'output.nc'
)
```

## File Naming Convention

Format: `era5_vn_{variable}_{year_start}_{year_end}.nc`

Example: `era5_vn_total_precipitation_2017_2020.nc`

- `era5_vn`: ERA5 Vietnam prefix
- `{variable}`: Meteorological variable name
- `{year_start}_{year_end}`: Time period

## Volume Reference

Each NetCDF file (typical):

- **Temperature variables**: ~800 MB per file
- **Pressure variables**: ~600 MB per file
- **Precipitation**: ~900 MB (Key target variable)
- **Total per period**: ~8 GB

## Processing Workflow

```
era5_2017_2020/*.nc + era5_2021_2024/*.nc
                    ↓
        [Data Discovery Notebook]
        (exploratory analysis)
                    ↓
    [Merge & Normalize Notebook]
    (alignment, merging, normalization)
                    ↓
        2_Data_Processed/
        (train/val/test splits)
                    ↓
        3_Models/
        (training & inference)
```

## References

- **ERA5 Documentation**: https://confluence.ecmwf.int/display/CKB/ERA5
- **CDS API Guide**: https://cds.climate.copernicus.eu/api-how-to
- **Dataset Citation**: Hersbach et al. (2020) - https://doi.org/10.1038/s41586-020-2438-0

---

**Note**: This directory is included in the repository for reference. For full functionality, ensure files are present before running preprocessing or training notebooks.
