# Data & Checkpoint Download Guide

## 📊 Why aren't Data/Checkpoints on GitHub?

Due to GitHub's file size limits (max 100MB per file), large data files and model checkpoints **are not pushed to the repository**:

- **ERA5 Data**: ~40GB (combined `.nc` files)
- **Model Checkpoints**: ~5GB (combined `.pth` files)
- **Total**: ~45GB

## 🔗 Data Downloads

### Option 1: Google Drive (Recommended) ⭐

**🔗 Download link**: **[Full Project on Google Drive](https://drive.google.com/drive/folders/1tf5lpbhOmHLujCH51z2INtnHAZOprNay?usp=sharing)**

Includes:

```
Rain_Forecast_Project/
├── 1_Data_Raw/                    # Raw ERA5 data (~20GB)
│   ├── era5_2017_2020/           # 10 meteorological variables
│   └── era5_2021_2024/           # 10 meteorological variables
├── 2_Data_Processed/              # Processed datasets (~16GB)
│   ├── train_2017_2022.nc
│   ├── val_2023.nc
│   ├── test_2024.nc
│   └── normalization_stats_2017_2024.json
├── 4_Checkpoints/                 # Model weights (~5GB)
│   ├── Fourier_Convolutional_Transformer/
│   └── Spatio_Temporal_Transformer/
├── 3_Models/                      # Training notebooks
├── 5_Results/                     # Evaluation results
└── Data_Preprocessing_Notebooks/  # Data processing notebooks
```

**After downloading:**

```bash
# Download each directory from Google Drive
# Place them in the correct project path:
# - 1_Data_Raw/
# - 2_Data_Processed/
# - 4_Checkpoints/

# Or clone the repository and add the data manually:
git clone https://github.com/Cheesenoice/Rain_Forecast_Project.git
cd Rain_Forecast_Project
# Copy data from Google Drive into the corresponding folders
```

### Option 2: Re-download from Copernicus (ERA5)

If you want to reproduce everything from scratch:

```bash
# 1. Register an account at:
# https://cds.climate.copernicus.eu/

# 2. Run the data download notebook
jupyter notebook Data_Preprocessing_Notebooks/1_Era5_Weather_Data_Download.ipynb

# 3. Process the data
jupyter notebook Data_Preprocessing_Notebooks/3_Data_Merged_and_Normalized.ipynb

# 4. Retrain the model
jupyter notebook 3_Models/2_Fourier_Convolutional_Transformer/1_Train_Fourier_Convolutional_Transformer.ipynb
```

**Note**: This process takes ~6-8 hours (download + processing + training)

### Option 3: Kaggle Dataset (Coming Soon)

Will be uploaded to Kaggle Datasets for easier downloading:

```bash
kaggle datasets download -d [username]/vietnam-rainfall-era5-2017-2024
```

## 📁 Directory Structure After Download

```
Rain_Forecast_Project/
├── 1_Data_Raw/
│   ├── era5_2017_2020/              # 10 .nc files
│   └── era5_2021_2024/              # 10 .nc files
├── 2_Data_Processed/
│   ├── train_2017_2022.nc           # ~12GB
│   ├── val_2023.nc                  # ~2GB
│   ├── test_2024.nc                 # ~2GB
│   ├── era5_vn_merged_normalized_2017_2024.nc  # ~18GB
│   └── normalization_stats_2017_2024.json (available on GitHub)
└── 4_Checkpoints/
    ├── Fourier_Convolutional_Transformer/
    │   └── 20251024_051938/
    │       ├── best_model.pth       # Best model weight
    │       └── epoch_*.pth          # 22 checkpoints
    └── Spatio_Temporal_Transformer/
        └── 20251019_084207/
            └── best_model.pth
```

## ✅ Data Validation

After downloading and extracting:

```python
import os
import xarray as xr

# Check training file
train_path = '2_Data_Processed/train_2017_2022.nc'
if os.path.exists(train_path):
    ds = xr.open_dataset(train_path)
    print(f"✅ Train data OK: {ds.dims}")
else:
    print("❌ Train data not found")

# Check checkpoint
checkpoint_path = '4_Checkpoints/Fourier_Convolutional_Transformer/20251024_051938/best_model.pth'
if os.path.exists(checkpoint_path):
    import torch
    checkpoint = torch.load(checkpoint_path, map_location='cpu')
    print(f"✅ Checkpoint OK: epoch {checkpoint.get('epoch', 'N/A')}")
else:
    print("❌ Checkpoint not found")
```

## 🔄 Small Demo Data

To quickly test the code, you can generate small dummy data:

```python
# Create demo_data.py
import xarray as xr
import numpy as np
import pandas as pd

# Create dummy data (10 time steps)
time = pd.date_range('2024-01-01', periods=10, freq='2H')
lat = np.linspace(8, 24, 65)
lon = np.linspace(102, 110, 33)

data_vars = {}
for var in ['t2m', 'd2m', 'u10', 'v10', 'msl', 'sp', 'tp', 'ssrd', 'skt', 'tcwv']:
    data_vars[var] = (['time', 'lat', 'lon'], np.random.randn(10, 65, 33))

ds = xr.Dataset(data_vars, coords={'time': time, 'lat': lat, 'lon': lon})
ds.to_netcdf('2_Data_Processed/demo_small.nc')
print("✅ Demo data created")
```

## 💾 Capacity Requirements

- **Inference only**: ~7GB (test data + checkpoint)
- **Train from scratch**: ~50GB (full data + checkpoints + temp files)
- **Development**: ~60GB (including venv, cache, etc.)

## ❓ FAQ

**Q: Why not use Git LFS?**
A: Git LFS has bandwidth limits (1GB/month free), which isn't enough for 45GB of data.

**Q: Can I run the code without data?**
A: Yes, you can inspect the model architecture and logic in the notebooks. However, training/evaluation requires data.

**Q: Is the data copyrighted?**
A: ERA5 data from Copernicus is public and free for research and education.

## 📧 Contact

For support with downloading data, please contact:

- Email: huynhhuutri2004@gmail.com
- GitHub Issues: [Create an issue](https://github.com/Cheesenoice/Rain_Forecast_Project/issues)

---

**Update**: Google Drive and Kaggle links will be updated once the upload is fully complete.
