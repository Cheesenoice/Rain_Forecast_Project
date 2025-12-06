# Hướng Dẫn Tải Dữ Liệu và Checkpoints

## 📊 Tại Sao Không Có Data/Checkpoints Trên GitHub?

Do giới hạn của GitHub (file tối đa 100MB), các file dữ liệu lớn và model checkpoints **không được đẩy lên repository**:

- **Dữ liệu ERA5**: ~40GB (các file `.nc`)
- **Model Checkpoints**: ~5GB (các file `.pth`)
- **Tổng cộng**: ~45GB

## 🔗 Tải Xuống Dữ Liệu

### Tùy Chọn 1: Google Drive (Khuyến Nghị)

**Link tải**: [Coming Soon - Sẽ cập nhật]

Bao gồm:

```
Rain_Forecast_Project_Data.zip (~20GB nén)
├── 1_Data_Raw/                    # Dữ liệu ERA5 gốc
├── 2_Data_Processed/              # Dữ liệu đã xử lý (train/val/test)
└── 4_Checkpoints/                 # Model weights đã train
```

**Sau khi tải:**

```bash
# Giải nén vào thư mục dự án
unzip Rain_Forecast_Project_Data.zip

# Hoặc giải nén thủ công và đặt vào đúng thư mục
```

### Tùy Chọn 2: Tải Lại Từ Copernicus (ERA5)

Nếu bạn muốn tái tạo hoàn toàn từ đầu:

```bash
# 1. Đăng ký tài khoản tại:
# https://cds.climate.copernicus.eu/

# 2. Chạy notebook tải dữ liệu
jupyter notebook Data_Preprocessing_Notebooks/1_Era5_Weather_Data_Download.ipynb

# 3. Xử lý dữ liệu
jupyter notebook Data_Preprocessing_Notebooks/3_Data_Merged_and_Normalized.ipynb

# 4. Train lại model
jupyter notebook 3_Models/2_Fourier_Convolutional_Transformer/1_Train_Fourier_Convolutional_Transformer.ipynb
```

**Lưu ý**: Quá trình này mất ~6-8 giờ (tải + xử lý + train)

### Tùy Chọn 3: Kaggle Dataset (Coming Soon)

Sẽ upload lên Kaggle Datasets để dễ dàng tải xuống:

```bash
kaggle datasets download -d [username]/vietnam-rainfall-era5-2017-2024
```

## 📁 Cấu Trúc Thư Mục Sau Khi Tải

```
Rain_Forecast_Project/
├── 1_Data_Raw/
│   ├── era5_2017_2020/              # 10 file .nc
│   └── era5_2021_2024/              # 10 file .nc
├── 2_Data_Processed/
│   ├── train_2017_2022.nc           # ~12GB
│   ├── val_2023.nc                  # ~2GB
│   ├── test_2024.nc                 # ~2GB
│   ├── era5_vn_merged_normalized_2017_2024.nc  # ~18GB
│   └── normalization_stats_2017_2024.json (có sẵn trên GitHub)
└── 4_Checkpoints/
    ├── Fourier_Convolutional_Transformer/
    │   └── 20251024_051938/
    │       ├── best_model.pth       # Model tốt nhất
    │       └── epoch_*.pth          # 22 checkpoints
    └── Spatio_Temporal_Transformer/
        └── 20251019_084207/
            └── best_model.pth
```

## ✅ Kiểm Tra Dữ Liệu

Sau khi tải và giải nén:

```python
import os
import xarray as xr

# Kiểm tra file train
train_path = '2_Data_Processed/train_2017_2022.nc'
if os.path.exists(train_path):
    ds = xr.open_dataset(train_path)
    print(f"✅ Train data OK: {ds.dims}")
else:
    print("❌ Train data not found")

# Kiểm tra checkpoint
checkpoint_path = '4_Checkpoints/Fourier_Convolutional_Transformer/20251024_051938/best_model.pth'
if os.path.exists(checkpoint_path):
    import torch
    checkpoint = torch.load(checkpoint_path, map_location='cpu')
    print(f"✅ Checkpoint OK: epoch {checkpoint.get('epoch', 'N/A')}")
else:
    print("❌ Checkpoint not found")
```

## 🔄 Dữ Liệu Demo (Nhỏ)

Nếu chỉ muốn test code nhanh, có thể tạo dữ liệu demo nhỏ:

```python
# Tạo file demo_data.py
import xarray as xr
import numpy as np
import pandas as pd

# Tạo dữ liệu giả (10 time steps)
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

## 💾 Dung Lượng Yêu Cầu

- **Chỉ chạy inference**: ~7GB (chỉ cần test data + checkpoint)
- **Train từ đầu**: ~50GB (full data + checkpoints + temp files)
- **Phát triển**: ~60GB (bao gồm môi trường ảo, cache, v.v.)

## ❓ Câu Hỏi Thường Gặp

**Q: Tại sao không dùng Git LFS?**
A: Git LFS vẫn có giới hạn bandwidth (1GB/tháng miễn phí), không đủ cho data 45GB.

**Q: Có thể chạy code mà không có data?**
A: Có, bạn có thể xem kiến trúc mô hình, logic code trong notebooks. Nhưng để train/evaluate cần có data.

**Q: Data có bản quyền không?**
A: Dữ liệu ERA5 từ Copernicus là công khai, miễn phí cho nghiên cứu và giáo dục.

## 📧 Liên Hệ

Nếu cần hỗ trợ tải dữ liệu, vui lòng liên hệ:

- Email: huynhhuutri2004@gmail.com
- GitHub Issues: [Create an issue](https://github.com/Cheesenoice/Rain_Forecast_Project/issues)

---

**Cập nhật**: Link Google Drive và Kaggle sẽ được bổ sung sau khi upload hoàn tất.
