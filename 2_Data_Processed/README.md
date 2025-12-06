# Dữ Liệu Đã Xử Lý - Bộ Dữ Liệu Sạch & Chuẩn Hóa

Thư mục này chứa dữ liệu khí tượng **đã tiền xử lý, gộp và chuẩn hóa** sẵn sàng cho huấn luyện và đánh giá mô hình.

## Tổng Quan

Tất cả dữ liệu thô ERA5 đã được:

- ✅ Gộp từ nhiều khoảng thời gian
- ✅ Căn chỉnh theo lưới không gian chung
- ✅ Chuẩn hóa sử dụng phương pháp z-score
- ✅ Chia thành tập train/validation/test

## Các Tệp

### 1. Tập Huấn Luyện (2017-2022)

**Tệp**: `train_2017_2022.nc`

- **Khoảng Thời Gian**: 1 tháng 1, 2017 - 31 tháng 12, 2022
- **Thời Lượng**: 6 năm dữ liệu
- **Kích Thước**: ~12 GB
- **Mục Đích**: Huấn luyện mô hình
- **Bước Thời Gian**: ~26,280 (mỗi 2 giờ)

**Số Chiều**:

```
Dimensions: (time: 26280, lat: 65, lon: 33, features: 10)
```

### 2. Tập Kiểm Chứng (2023)

**Tệp**: `val_2023.nc`

- **Khoảng Thời Gian**: 1 tháng 1, 2023 - 31 tháng 12, 2023
- **Thời Lượng**: 1 năm dữ liệu
- **Kích Thước**: ~2 GB
- **Mục Đích**: Điều chỉnh siêu tham số, dừng sớm
- **Bước Thời Gian**: ~4,380 (mỗi 2 giờ)

**Số Chiều**:

```
Dimensions: (time: 4380, lat: 65, lon: 33, features: 10)
```

### 3. Tập Kiểm Tra (2024)

**Tệp**: `test_2024.nc`

- **Khoảng Thời Gian**: 1 tháng 1, 2024 - 31 tháng 12, 2024
- **Thời Lượng**: 1 năm dữ liệu
- **Kích Thước**: ~2 GB
- **Mục Đích**: Đánh giá mô hình cuối cùng (dữ liệu chưa thấy)
- **Bước Thời Gian**: ~4,368 (mỗi 2 giờ, bao gồm ngày nhuận)

**Số Chiều**:

```
Dimensions: (time: 4368, lat: 65, lon: 33, features: 10)
```

### 4. Bộ Dữ Liệu Gộp (Đầy Đủ)

**Tệp**: `era5_vn_merged_normalized_2017_2024.nc`

- **Chứa**: Tất cả 8 năm đã gộp
- **Kích Thước**: ~18 GB
- **Mục Đích**: Tham chiếu hoàn chỉnh, phân tích nâng cao
- **Hữu Ích Cho**: Tạo các phân chia train/val/test tùy chỉnh

### 5. Thống Kê Chuẩn Hóa

**Tệp**: `normalization_stats_2017_2024.json`

Chứa giá trị trung bình và độ lệch chuẩn cho mỗi biến được tính toán trên toàn bộ giai đoạn 8 năm:

```json
{
    "t2m": {
        "mean": 298.47,
        "std": 4.78,
        "units": "K",
        "transform": "z-score"
    },
    "d2m": {...},
    "u10": {...},
    "v10": {...},
    "msl": {...},
    "sp": {...},
    "tp": {...},      // <- Target variable (precipitation)
    "ssrd": {...},
    "skt": {...},
    "tcwv": {...}
}
```

## Định Dạng Dữ Liệu

### Cấu Trúc NetCDF4

```
Dataset: train_2017_2022.nc
├── Dimensions:
│   ├── time: 26280
│   ├── lat: 65
│   ├── lon: 33
│   └── features: 10
│
├── Coordinates:
│   ├── time (float64): Unix timestamp
│   ├── latitude (float32): 8°N - 24°N
│   └── longitude (float32): 102°E - 110°E
│
└── Data Variables:
    ├── t2m: (time, lat, lon) - 2m Temperature [K]
    ├── d2m: (time, lat, lon) - Dewpoint Temperature [K]
    ├── u10: (time, lat, lon) - Eastward Wind [m/s]
    ├── v10: (time, lat, lon) - Northward Wind [m/s]
    ├── msl: (time, lat, lon) - Mean Sea Level Pressure [Pa]
    ├── sp: (time, lat, lon) - Surface Pressure [Pa]
    ├── tp: (time, lat, lon) - Total Precipitation [m] **TARGET**
    ├── ssrd: (time, lat, lon) - Solar Radiation [J/m²]
    ├── skt: (time, lat, lon) - Skin Temperature [K]
    └── tcwv: (time, lat, lon) - Water Vapour [kg/m²]
```

## Chi Tiết Biến

| Biến                  | Viết Tắt | Đơn Vị | Chuẩn Hóa   | Phạm Vi        |
| --------------------- | -------- | ------ | ----------- | -------------- |
| Nhiệt Độ 2m           | t2m      | K      | z-score     | 265-315 K      |
| Nhiệt Độ Điểm Sương   | d2m      | K      | z-score     | 265-310 K      |
| Gió U (10m)           | u10      | m/s    | z-score     | -15 đến +15    |
| Gió V (10m)           | v10      | m/s    | z-score     | -15 đến +15    |
| Áp Suất Mực Nước Biển | msl      | Pa     | z-score     | 95,000-105,000 |
| Áp Suất Bề Mặt        | sp       | Pa     | z-score     | 90,000-105,000 |
| **Lượng Mưa**         | **tp**   | **m**  | **z-score** | **0-0.05**     |
| Bức Xạ Mặt Trời       | ssrd     | J/m²   | z-score     | 0-300,000      |
| Nhiệt Độ Bề Mặt       | skt      | K      | z-score     | 260-320 K      |
| Hơi Nước              | tcwv     | kg/m²  | z-score     | 5-70           |

## Phạm Vi Không Gian

### Phạm Vi Địa Lý

- **Khu Vực**: Việt Nam + Vùng Biển Lân Cận
- **Bắc**: 24°N (Cao Nguyên Phía Bắc)
- **Nam**: 8°N (Bờ Biển Phía Nam)
- **Tây**: 102°E (Biên Giới Lào)
- **Đông**: 110°E (Biển Đông)

### Độ Phân Giải Lưới

- **Điểm Vĩ Độ**: 65 (khoảng cách 0.25°)
- **Điểm Kinh Độ**: 33 (khoảng cách 0.25°)
- **Tổng Số Vị Trí**: 2,145 ô lưới
- **Diện Tích Ô**: ~625 km² (tại xích đạo)

## Phạm Vi Thời Gian

### Dữ Liệu Huấn Luyện (2017-2022)

```
Phân Bố Theo Mùa:
├── Mùa Đông (Th12-Th2): 1,753 bước
├── Mùa Xuân (Th3-Th5): 1,752 bước
├── Mùa Hè (Th6-Th8): 1,752 bước
├── Mùa Thu (Th9-Th11): 1,752 bước
└── (lặp lại ~6 lần cho 6 năm)
```

### Dữ Liệu Kiểm Chứng (2023)

```
Một năm bao gồm tất cả các mùa
├── Mùa Đông (Th12 2022-Th2 2023): 292 bước
├── Mùa Xuân (Th3-Th5): 360 bước
├── Mùa Hè (Th6-Th8): 360 bước
└── Mùa Thu (Th9-Th11): 360 bước
```

### Dữ Liệu Kiểm Tra (2024)

```
Một năm bao gồm tất cả các mùa + ngày nhuận
Tổng: 4,368 bước (mỗi 2 giờ)
```

## Ví Dụ Sử Dụng

### Tải Dữ Liệu trong Python

```python
import xarray as xr
import numpy as np

# Load train set
train_ds = xr.open_dataset('train_2017_2022.nc')
print(train_ds)  # Xem cấu trúc
print(train_ds['tp'].shape)  # (26280, 65, 33)

# Truy cập các biến riêng lẻ
temperature = train_ds['t2m'].values  # Mảng NumPy
precipitation = train_ds['tp'].values

# Trích xuất chuỗi thời gian tại vị trí cụ thể (lat_idx, lon_idx)
lat_idx, lon_idx = 32, 16  # Trung tâm lưới
ts = train_ds['tp'][:, lat_idx, lon_idx].values

# Tải thống kê chuẩn hóa
import json
with open('normalization_stats_2017_2024.json') as f:
    stats = json.load(f)

# Khử chuẩn hóa dự đoán
pred_normalized = model_output
pred_original = pred_normalized * stats['tp']['std'] + stats['tp']['mean']
```

### Tạo Khung Thời Gian Tùy Chỉnh

```python
import xarray as xr

# Tải bộ dữ liệu
ds = xr.open_dataset('train_2017_2022.nc')

# Trích xuất khoảng thời gian cụ thể
start_date = '2018-06-01'
end_date = '2018-08-31'  # Gió mùa hè
summer_data = ds.sel(time=slice(start_date, end_date))

# Trích xuất khu vực cụ thể
data_subset = ds.sel(lat=slice(10, 22), lon=slice(104, 108))
```

## Lý Do Chia Train/Val/Test

| Tập   | Năm       | Tỷ Lệ | Mục Đích              | Theo Thời Gian | Ngày Nhuận |
| ----- | --------- | ----- | --------------------- | -------------- | ---------- |
| Train | 2017-2022 | 60%   | Học mô hình           | ✓              | Có         |
| Val   | 2023      | 20%   | Kiểm chứng & dừng sớm | ✓              | Có         |
| Test  | 2024      | 20%   | Đánh giá cuối cùng    | ✓              | Có         |

**Chiến Lược Chia Theo Thời Gian**:

- Bảo toàn thứ tự thời gian (không rò rỉ dữ liệu)
- Mỗi tập bao gồm chu kỳ mùa đầy đủ
- Cho phép kiểm tra trên giai đoạn thời gian chưa thấy
- Dữ liệu gần đây (2024) đại diện tốt hơn cho khí hậu hiện tại

## Các Bước Tiền Xử Lý Đã Áp Dụng

### 1. Căn Chỉnh Không Gian

```
Tất cả các biến được nội suy theo lưới chung 0.25° × 0.25°
Độ phân giải: 65 vĩ độ × 33 kinh độ = 2,145 điểm
```

### 2. Căn Chỉnh Thời Gian

```
Tất cả các biến được gộp theo dấu thời gian chung mỗi 2 giờ
Tần suất: 00:00, 02:00, 04:00, ..., 22:00
Giá trị thiếu: Được điền bằng nội suy xarray
```

### 3. Chuẩn Hóa Z-score

```
Đối với mỗi biến v:
    v_chuẩn_hóa = (v - trung_bình) / độ_lệch_chuẩn

Trong đó trung_bình và độ_lệch_chuẩn được tính trên toàn bộ giai đoạn 2017-2024
Tránh rò rỉ train/test trong khi sử dụng phạm vi đầy đủ
```

### 4. Kiểm Tra Dữ Liệu

```
✓ Không có giá trị NaN
✓ Không có giá trị vô hạn
✓ Phạm vi thực tế về mặt vật lý
✓ Đã kiểm tra tính liên tục theo thời gian
✓ Đã kiểm tra tính đầy đủ về không gian
```

## Kích Thước Tệp & Lưu Trữ

```
train_2017_2022.nc ......... 12.1 GB
val_2023.nc ................ 2.1 GB
test_2024.nc ............... 2.0 GB
─────────────────────────────────────
Train/Val/Test Total ....... 16.2 GB

era5_vn_merged_normalized_2017_2024.nc ... 18.3 GB
normalization_stats_2017_2024.json ....... 5 KB
```

## Truy Cập Dữ Liệu

Các bộ dữ liệu đã xử lý sẵn sàng cho sản xuất. Để sử dụng chúng:

1. **Để Huấn Luyện**:

   ```bash
   jupyter notebook ../3_Models/2_Fourier_Convolutional_Transformer/1_Train_Fourier_Convolutional_Transformer.ipynb
   ```

2. **Để Phân Tích Tùy Chỉnh**:

   ```python
   import xarray as xr
   ds = xr.open_dataset('train_2017_2022.nc')
   # Phân tích của bạn ở đây
   ```

3. **Để Tái Tạo**:
   ```bash
   jupyter notebook ../Data_Preprocessing_Notebooks/3_Data_Merged_and_Normalized.ipynb
   ```

## Đảm Bảo Chất Lượng Dữ Liệu

- ✅ **Đầy Đủ**: Bao phủ không gian 100%, không có khoảng trống
- ✅ **Chính Xác**: Độ chính xác phân tích lại ERA5 gốc (~1-2% cho hầu hết các biến)
- ✅ **Nhất Quán**: Các biến nhất quán về mặt vật lý (bảo toàn khối lượng, cân bằng năng lượng)
- ✅ **Chuẩn Hóa**: Được tính toán đúng cách trên toàn bộ cơ sở 8 năm
- ✅ **Phân Chia**: Không có sự chồng chéo hoặc rò rỉ về thời gian

## Tài Liệu Tham Khảo

- **Dữ Liệu Gốc**: Copernicus Climate Data Store (ERA5)
- **Tiền Xử Lý**: Pipeline tùy chỉnh với xarray, numpy
- **Lý Thuyết Chuẩn Hóa**: Z-score tiêu chuẩn (căn giữa theo trung bình và mở rộng)
- **Cơ Sở Khí Hậu**: Giai đoạn 8 năm (2017-2024)

---

**Cập Nhật Lần Cuối**: Tháng 12, 2025 | **Phiên Bản**: 1.0 | **Trạng Thái**: Sẵn Sàng Cho Sản Xuất
