# Models - Training & Evaluation Notebooks

This directory contains Jupyter notebooks for training and evaluating spatio-temporal neural network models for weather forecasting.

## Overview

Two complementary architectures are implemented:

1. **Spatio-Temporal Transformer** - Pure Transformer approach
2. **Fourier-Enhanced Convolutional Transformer (FECT)** - Hybrid CNN + Transformer (Main model)

## Model Architectures

### 1. Fourier-Enhanced Convolutional Transformer (FECT) ⭐ Main Model

**Location**: `2_Fourier_Convolutional_Transformer/`

A sophisticated hybrid architecture designed to capture complex spatio-temporal patterns in weather data.

#### Architectural Components

- **CNN Encoder**: Extracts local spatial features (Conv2D blocks)
- **Time2Vec Embedding**: Learnable time encoding (sine/cosine basis)
- **Fourier Transform Layer**: Frequency domain feature extraction for periodic patterns
- **Spatial Attention**: Learns geographical weights and importance
- **Transformer Stack**: 4 layers with Multi-Head Attention (8 heads, d_model=128)
- **MLP Decoder**: Final precipitation value prediction

#### Training Configuration

- **Sequence Length**: 4 steps (8-hour lookback)
- **Loss**: Mean Squared Error (MSE)
- **Optimizer**: Adam with weight decay
- **Highlights**: Learnable Time2Vec, Fourier features, Spatial Attention

### 2. Spatio-Temporal Transformer

**Location**: `1_Spatio_Temporal_Transformer/`

A pure Transformer architecture without CNN preprocessing, focused on direct sequence-to-sequence learning.

---

## Notebooks Overview

### 📓 1_Train_Fourier_Convolutional_Transformer.ipynb

**Purpose**: Complete training pipeline for the FECT model.

- **Data Loading**: PyTorch DataLoaders from `2_Data_Processed/`
- **Training Loop**: Backpropagation, gradient clipping, validation monitoring
- **Output**: Checkpoints saved to `4_Checkpoints/`

### 📓 2_Evaluation_and_Inference.ipynb

**Purpose**: Model evaluation on the test set and prediction generation.

- **Metrics**: MSE, MAE, RMSE, R², MAPE, Correlation
- **Analysis**: Seasonal performance (Dry/Rainy), geographical maps, error distribution
- **Visuals**: Time-series comparison, heatmaps, case studies

### 📓 3_Visualize_Learning_Curves.ipynb

**Purpose**: Analysis of training dynamics and production of high-quality plots.

- **Plots**: Loss curves, convergence analysis, model architecture diagrams

---

## Model Checkpoints

Saved weights are available in `4_Checkpoints/`:

- `best_model.pth`: Peak validation performance
- `epoch_1.pth` to `epoch_22.pth`: Training snapshots
- `training_log.json`: Training history metrics

## Dependencies

- **Core**: `torch >= 2.0`, `xarray`, `numpy`, `pandas`, `scikit-learn`
- **Visuals**: `matplotlib`, `cartopy`, `seaborn`
- **Utils**: `tqdm`, `jupyter`

## Usage Roadmap

1. **Inspect Results**: Run `2_Evaluation_and_Inference.ipynb` (Results already generated)
2. **Review Training**: Run `3_Visualize_Learning_Curves.ipynb`
3. **Retrain (Optional)**: Run `1_Train_Fourier_Convolutional_Transformer.ipynb`

---

**Model Performance Summary (Test 2024)**:

- **R² Score**: 0.78
- **MAE**: 0.045 m
- **Status**: ✅ Completed & Validated
