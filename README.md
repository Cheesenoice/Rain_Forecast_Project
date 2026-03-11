# Rain Forecast Project: Advanced Spatio-Temporal Deep Learning

> A comprehensive machine learning system for rainfall forecasting in Vietnam using ERA5 reanalysis data and deep neural networks.

<div align="center">

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-2.0%2B-orange)
![License](https://img.shields.io/badge/License-MIT-green)

[🇻🇳 Tiếng Việt](README_VI.md) | [🇺🇸 English](README.md)

</div>

## 📄 Project Documentation

**Scientific Research Paper Preview:**

- 📊 **[Science Research Report](https://drive.google.com/file/d/1Cxhg5HA4oih5E2pFF0SWeDFgl7aVhWfT/view?usp=sharing)** - Detailed report on methodology, model architecture, experiments, and results
- 🎯 **[Presentation Slides](https://drive.google.com/file/d/1Dwrhw-IcwBDLRsRLCWWoyQrkAwsVRyLE/view?usp=sharing)** - Summary presentation of the project with visualizations

## 💾 Data & Checkpoint Downloads

**📦 Google Drive - Full Project:**

🔗 **[Download Data, Model Checkpoints & Notebooks](https://drive.google.com/drive/folders/1tf5lpbhOmHLujCH51z2INtnHAZOprNay?usp=sharing)**

Includes:

- ✅ Raw ERA5 data (2017-2024)
- ✅ Processed datasets (train/val/test)
- ✅ Trained model checkpoints
- ✅ Notebooks and results

> **Note**: Due to GitHub's 100MB/file limit, large data (~45GB) is stored on Google Drive. See [DATA_DOWNLOAD.md](DATA_DOWNLOAD.md) for details.

---

## Overview

This project implements two advanced spatio-temporal neural network architectures for rainfall forecasting in Vietnam:

1. **Spatio-Temporal Transformer** - A pure Transformer-based approach for sequential weather forecasting
2. **Fourier-Enhanced Convolutional Transformer (FECT)** - A hybrid architecture featuring:
   - Convolutional layers for local spatial feature extraction
   - Transformer modules to learn global dependencies
   - Time2Vec for learnable time encoding
   - Fourier transform features to capture periodic patterns
   - Spatial Attention mechanism to weigh geographical importance

The models leverage 8 years of ERA5 reanalysis data (2017-2024) including 10 meteorological variables to predict total precipitation with high accuracy.

## Project Structure

```
Rain_Forecast_Project/
├── 1_Data_Raw/                          # Raw ERA5 weather data
│   ├── era5_2017_2020/                  # 10 variables × 2 periods
│   └── era5_2021_2024/
├── 2_Data_Processed/                    # Processed and normalized datasets
│   ├── train_2017_2022.nc               # Training set
│   ├── val_2023.nc                      # Validation set
│   ├── test_2024.nc                     # Test set
│   ├── era5_vn_merged_normalized_2017_2024.nc
│   └── normalization_stats_2017_2024.json
├── 3_Models/                            # Training and evaluation notebooks
│   ├── 1_Spatio_Temporal_Transformer/
│   │   └── Train_Spatio_Temporal_Transformer.ipynb
│   └── 2_Fourier_Convolutional_Transformer/
│       ├── 1_Train_Fourier_Convolutional_Transformer.ipynb
│       ├── 2_Evaluation_and_Inference.ipynb
│       └── 3_Visualize_Learning_Curves.ipynb
├── 4_Checkpoints/                       # Saved model weights
│   ├── Fourier_Convolutional_Transformer/
│   │   └── 20251024_051938/
│   │       ├── epoch_1.pth to epoch_22.pth
│   │       └── best_model.pth
│   └── Spatio_Temporal_Transformer/
│       └── 20251019_084207/
├── 5_Results/                           # Evaluation metrics and visualizations
│   └── evaluation_results_Fourier_Convolutional_Transformer/
│       ├── quantitative_metrics.csv
│       ├── seasonal_metrics_comparison.csv
│       └── (Various PNG images)
└── Data_Preprocessing_Notebooks/        # Data processing pipeline
    ├── 1_Era5_Weather_Data_Download.ipynb
    ├── 2_Data_Discovery.ipynb
    └── 3_Data_Merged_and_Normalized.ipynb
```

## Key Features

<div align="center">

### 🌍 Data Processing Workflow

<img src="5_Results/Project_Pipeline.png" alt="Data Pipeline" width="500"/>

</div>

### 📊 ERA5 Data Pipeline

- **ERA5 Reanalysis Data**: 10 meteorological variables at 0.25° resolution
  - 2m Temperature (t2m)
  - 2m Dewpoint Temperature (d2m)
  - 10m U/V Wind components (u10, v10)
  - Mean Sea Level Pressure (msl)
  - Surface Pressure (sp)
  - Total Precipitation (tp) - **Target variable**
  - Surface Solar Radiation Downwards (ssrd)
  - Skin Temperature (skt)
  - Total Column Water Vapour (tcwv)
- **Time Range**: 2017-2024 (8 years)
- **Geographic Scope**: Vietnam region (8°N-24°N, 102°E-110°E)
- **Temporal Resolution**: 2-hourly measurements
- **Normalization**: Z-score normalization with stored statistics

### Model Architectures

<div align="center">

#### 🏗️ Architecture Comparison

<img src="5_Results/Model_Compare.png" alt="FECT vs SST comparison" width="700"/>

_Two architectures: **FECT** (Enhanced with CNN, Fourier, Time2Vec) and **SST** (Basic Spatio-Temporal Transformer)_

</div>

**⚡ FECT Highlights (Main Model):**

- 🔹 **CNN Encoder**: Efficiently extracts local spatial features
- 🔹 **Time2Vec**: Learnable time encoding, superior to standard sine embeddings
- 🔹 **Fourier Features**: Captures cyclical weather patterns in frequency domain
- 🔹 **Spatial Attention**: Learns importance of individual geographical regions
- 🔹 **Transformer Stack**: 4 layers, 8 heads, handles global dependencies

**⚙️ Training Configuration:**

- Sequence Length: 4 steps (8h) | Model Dim: 128 | Heads: 8 | Layers: 4
- Batch: 8 | Learning Rate: 1e-4 | Optimizer: Adam | Dropout: 0.1

### Evaluation & Results

The models demonstrate strong performance across multiple metrics:

**Quantitative Metrics**:

- Mean Squared Error (MSE)
- Mean Absolute Error (MAE)
- Root Mean Squared Error (RMSE)
- R² Score
- Mean Absolute Percentage Error (MAPE)

**Qualitative Analysis**:

- Seasonal performance comparison (Dry, Transition, Rainy seasons)
- Error distribution analysis
- Time-series visualization of predictions vs. ground truth
- Case studies with spatial attention maps for interpretability

**Visualization Toolkit**:

- Time2Vec conceptual diagrams
- CNN Encoder architecture illustrations
- Spatial Attention heatmaps
- Seasonal performance comparison
- Error distribution and trends

## Getting Started

### Prerequisites

- Python 3.8+
- CUDA 11.0+ (for GPU acceleration)
- 8GB+ GPU Memory (recommended)

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/Rain_Forecast_Project.git
cd Rain_Forecast_Project

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Quick Start

#### 1. Data Preparation (Optional - Processed data already included)

```bash
# Download ERA5 data
jupyter notebook Data_Preprocessing_Notebooks/1_Era5_Weather_Data_Download.ipynb

# Explore and validate
jupyter notebook Data_Preprocessing_Notebooks/2_Data_Discovery.ipynb

# Merge and normalize
jupyter notebook Data_Preprocessing_Notebooks/3_Data_Merged_and_Normalized.ipynb
```

#### 2. Training

**Fourier-Enhanced Convolutional Transformer**:

```bash
jupyter notebook 3_Models/2_Fourier_Convolutional_Transformer/1_Train_Fourier_Convolutional_Transformer.ipynb
```

**Spatio-Temporal Transformer**:

```bash
jupyter notebook 3_Models/1_Spatio_Temporal_Transformer/Train_Spatio_Temporal_Transformer.ipynb
```

#### 3. Evaluation & Inference

```bash
jupyter notebook 3_Models/2_Fourier_Convolutional_Transformer/2_Evaluation_and_Inference.ipynb
```

#### 4. Visualization

```bash
jupyter notebook 3_Models/2_Fourier_Convolutional_Transformer/3_Visualize_Learning_Curves.ipynb
```

## Usage Examples

### Loading Pre-trained Models

```python
import torch
import xarray as xr

# Load model
model = FourierEnhancedConvTransformer(
    n_features=10,
    d_model=128,
    n_heads=8,
    n_layers=4,
    dropout=0.1
)
checkpoint = torch.load('4_Checkpoints/Fourier_Convolutional_Transformer/20251024_051938/best_model.pth')
model.load_state_dict(checkpoint['model_state_dict'])
model.eval()

# Load test data
test_data = xr.open_dataset('2_Data_Processed/test_2024.nc')

# Run prediction
with torch.no_grad():
    predictions = model(input_batch)
```

### Accessing Results

```python
import pandas as pd

# Load metrics
metrics = pd.read_csv('5_Results/evaluation_results_Fourier_Convolutional_Transformer/quantitative_metrics.csv')
seasonal = pd.read_csv('5_Results/evaluation_results_Fourier_Convolutional_Transformer/seasonal_metrics_comparison.csv')

print(metrics)
print(seasonal)
```

## Technical Details

### Data Preprocessing

- **Alignment**: All variables interpolated to a common spatial grid (65×33 points)
- **Normalization**: Z-score normalization for each feature
- **Train/Val/Test Split**:
  - Training: 2017-2022 (5 years)
  - Validation: 2023 (1 year)
  - Test: 2024 (1 year)
- **Time Windowing**: Sliding window with 4 look-back steps
- **Format**: NetCDF4 for efficient multi-dimensional storage

### Model Training

- **Loss Function**: Mean Squared Error (MSE)
- **Optimizer**: Adam with weight decay (L2 regularization)
- **Learning Rate Scheduler**: Adaptive based on validation performance
- **Early Stopping**: Patience-based monitoring of validation loss
- **Checkpoint Strategy**: Best model and periodic snapshots saved
- **Device**: Automatic GPU detection with CPU fallback

### Evaluation Strategy

- **Temporal CV**: Time-based splitting (no data leakage)
- **Metrics**: Standard regression and domain-specific indicators
- **Seasonal Analysis**: Separate evaluation for dry/rainy/transition periods
- **Geographic Visualization**: Attention maps for interpretability
- **Case Studies**: Deep-dive analysis of specific forecast events

## Results Summary

The **Fourier-Enhanced Convolutional Transformer** achieved:

- Superior capture of cyclic weather patterns via Fourier features
- Effective spatial attention for geography-aware predictions
- Robust temporal encoding with Time2Vec
- Balanced performance across seasonal variations

Model checkpoints show progressive improvement through training epochs (saved 1-22), with `best_model.pth` representing peak validation performance.

## Dependencies

Core libraries:

- **PyTorch** 2.0+ - Deep learning framework
- **xarray** - Multi-dimensional data handling
- **numpy/pandas** - Data manipulation
- **scikit-learn** - Metrics and preprocessing
- **matplotlib/cartopy** - Visualization
- **cdsapi** - ERA5 data downloading

See `requirements.txt` for the full list and versions.

## Main Notebooks Overview

| Notebook                                          | Purpose                           | Input                       | Output                               |
| ------------------------------------------------- | --------------------------------- | --------------------------- | ------------------------------------ |
| `1_Era5_Weather_Data_Download.ipynb`              | Download raw data from Copernicus | API Credentials             | NetCDF files (2017-2024)             |
| `2_Data_Discovery.ipynb`                          | Exploratory data analysis         | Raw NetCDF files            | Statistical summary, Visuals         |
| `3_Data_Merged_and_Normalized.ipynb`              | Preprocessing pipeline            | Multi-file raw data         | Train/Val/Test splits + stats        |
| `1_Train_Fourier_Convolutional_Transformer.ipynb` | Model Training                    | Normalized datasets         | Checkpoints, Loss curves             |
| `2_Evaluation_and_Inference.ipynb`                | Test set evaluation               | Best checkpoint + test data | Metrics, predictions, error analysis |
| `3_Visualize_Learning_Curves.ipynb`               | Training visualization            | Loss logs                   | Training curves, performance charts  |

## Future Work

- [ ] Multi-step ahead forecasting (beyond 2h lead time)
- [ ] Ensemble methods combining both architectures
- [ ] Operational deployment pipeline
- [ ] Uncertainty quantification with prediction intervals
- [ ] Expansion to more meteorological variables
- [ ] Regional transfer learning to neighboring countries
- [ ] Real-time inference API with FastAPI
- [ ] Hyperparameter optimization with Optuna/Ray Tune

## Citation

If you use this project in your research, please cite:

```bibtex
@project{RainForecastProject2025,
  title={Advanced Spatio-Temporal Deep Learning for Rainfall Prediction in Vietnam},
  author={Your Name},
  year={2025},
  url={https://github.com/yourusername/Rain_Forecast_Project}
}
```

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Contact & Support

- **Email**: huynhhuutri2004@gmail.com
- **GitHub**: [@cheesenoice](https://github.com/cheesenoice)
- **LinkedIn**: [Trí Huỳnh](https://www.linkedin.com/in/trisdev)

## Acknowledgements

- **Data Source**: Copernicus Climate Data Store (ERA5 Reanalysis)
- **Geographic Scope**: Vietnam Meteorological and Hydrological Administration
- **Inspiration**: Recent advances in spatio-temporal deep learning, attention mechanisms, and Fourier neural networks

---

**Project Status**: ✅ Completed | **Last Updated**: Dec 2025 | **Version**: 1.0
