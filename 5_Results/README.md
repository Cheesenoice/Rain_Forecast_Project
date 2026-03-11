# Results - Model Evaluation & Analysis

Detailed metrics, visualizations, and research case studies for the rainfall forecasting models.

## Structure

```
evaluation_results_Fourier_Convolutional_Transformer/
├── 📊 METRICS (CSV)
│   ├── quantitative_metrics.csv       # Overall scores (MSE, MAE, R², etc.)
│   ├── seasonal_metrics_comparison.csv # Performance by dry/rainy seasons
│   └── so_sanh_hieu_nang_theo_mua.csv # (VI) Seasonal comparison notes
│
└── 📈 VISUALIZATIONS (PNG)
    ├── Performance: Time-series, Error dist, Seasonal bar charts
    ├── Concepts: Time2Vec, Spatial Attention, CNN Encoder diagrams
    └── Case Studies: In-depth analysis for specific 2024 dates
```

## Key Metrics Summary (Test Set 2024)

| Metric          | Value   | Interpretation             |
| --------------- | ------- | -------------------------- |
| **MSE**         | 0.0034  | Low squared error          |
| **MAE**         | 0.045 m | Average error of 45mm      |
| **R² Score**    | 0.78    | 78% of variance explained  |
| **Correlation** | 0.88    | Strong linear relationship |

## Seasonal Performance

- **Dry Season (Dec-May)**: Peak performance (R² ~ 0.89)
- **Monsoon (Jun-Sep)**: Most challenging due to high volatility (R² ~ 0.65)
- **Transition (Oct-Nov)**: Robust performance (R² ~ 0.76)

## Architectural Visualizations

Detailed diagrams are provided for internal mechanisms:

- **Time2Vec**: Illustrating learnable frequency/phase components
- **Spatial Attention**: Heatmaps showing geographical areas of importance
- **CNN Encoder**: Spatial feature extraction visualization

## Reproduction

Generated using `3_Models/2_Fourier_Convolutional_Transformer/2_Evaluation_and_Inference.ipynb` using the `best_model.pth` checkpoint.

---

**Project Status**: ✅ Results validated | **Model Version**: 1.0 (FECT)
