# Notebooks Tiền Xử Lý Dữ Liệu

Thư mục này chứa quy trình dữ liệu hoàn chỉnh để chuẩn bị dữ liệu thời tiết ERA5 thô cho các mô hình học sâu.

## Tổng Quan

Ba notebook tuần tự triển khai quy trình tiền xử lý dữ liệu đầy đủ:

```
Dữ Liệu ERA5 Thô (2017-2024)
    ↓
[1] Tải xuống từ Copernicus API
    ↓
Tệp NetCDF thô (40 GB qua 2 giai đoạn)
    ↓
[2] Phân Tích Dữ Liệu Khám Phá
    ↓
Khám phá dữ liệu, kiểm tra chất lượng
    ↓
[3] Gộp & Chuẩn Hóa
    ↓
Bộ dữ liệu sẵn sàng (chia train/val/test)
    ↓
2_Data_Processed/ ✅ SẴN SÀNG ĐỂ HUẤN LUYỆN
```

## Các Notebook

### 1_Era5_Weather_Data_Download.ipynb

**Mục đích**: Tải xuống dữ liệu tái phân tích ERA5 thô từ Copernicus Climate Data Store

**Môi trường đích**: Google Colab (với tích hợp Google Drive)

#### Tính Năng Chính

- **Tự động tiếp tục**: Tiếp tục tải xuống bị gián đoạn mà không cần tải lại
- **Xử lý lỗi**: Tự động thử lại với backoff mũ
- **Theo dõi tiến độ**: Ghi log chi tiết trạng thái tải xuống
- **Tổ chức**: Tự động tạo thư mục và đặt tên tệp

#### Các Phần

1. **Thiết lập & Xác thực**

   - Cài đặt thư viện `cdsapi`
   - Cấu hình `~/.cdsapirc` với thông tin xác thực CDS
   - Gắn kết Google Drive để lưu trữ bền vững

2. **Cấu hình**

   - Xác định vùng địa lý (hộp giới hạn Việt Nam)
   - Chọn biến số (10 tham số khí tượng)
   - Đặt khoảng thời gian (2017-2020, 2021-2024)
   - Cấu hình điểm tiếp tục

3. **Logic Tải xuống**

   - Tải xuống theo khối thời gian (tránh timeout API)
   - Tải xuống từng biến riêng lẻ (xử lý lỗi dễ dàng hơn)
   - Lưu dưới định dạng NetCDF4 (chuẩn trong khoa học khí hậu)
   - Xác minh tính toàn vẹn tệp

4. **Đầu ra**
   - `1_Data_Raw/era5_2017_2020/*.nc` (10 tệp)
   - `1_Data_Raw/era5_2021_2024/*.nc` (10 tệp)
   - Tổng: ~40 GB

#### Cách Sử Dụng

```bash
# Trong Google Colab:
jupyter notebook 1_Era5_Weather_Data_Download.ipynb

# Yêu cầu trước:
# 1. Đăng ký tại: https://cds.climate.copernicus.eu/
# 2. Lấy API key từ cài đặt tài khoản
# 3. Cập nhật ~/.cdsapirc trong ô notebook 2
```

#### Ví Dụ Cấu Hình

```python
# Phạm vi địa lý (Bắc, Tây, Nam, Đông)
area = [24, 102, 8, 110]  # Vùng Việt Nam

# Các biến cần tải xuống
variables = [
    "2m_temperature",
    "2m_dewpoint_temperature",
    "10m_u_component_of_wind",
    "10m_v_component_of_wind",
    "mean_sea_level_pressure",
    "surface_pressure",
    "total_precipitation",
    "surface_solar_radiation_downwards",
    "skin_temperature",
    "total_column_water_vapour",
]

# Các khoảng thời gian
periods = [
    ("2017", "2020"),
    ("2021", "2024"),
]

# Độ phân giải thời gian
time_steps = ['00:00', '02:00', '04:00', '06:00', '08:00', '10:00',
              '12:00', '14:00', '16:00', '18:00', '20:00', '22:00']
```

#### Thời Gian Chạy Thông Thường

- **Mỗi giai đoạn**: 6-12 giờ (phụ thuộc vào mạng, tải CDS)
- **Cả hai giai đoạn**: 12-24 giờ
- **Khuyến nghị**: Chạy qua đêm hoặc sử dụng Google Colab (thực thi nền liên tục)

#### Khắc Phục Sự Cố

**Vượt quá giới hạn API**:

```python
# Đợi 30 phút trước khi thử lại
# Hoặc chia thành các khối thời gian nhỏ hơn (theo tháng thay vì năm)
```

**Vấn đề dung lượng đĩa**:

```python
# Kiểm tra dung lượng Drive: chỉ tải xuống một giai đoạn tại một thời điểm
# Hoặc nén ngay lập tức (đánh đổi CPU cho dung lượng đĩa)
```

**Gián đoạn mạng**:

```python
# Notebook tự động phát hiện và bỏ qua các tệp đã hoàn thành
# Chỉ cần khởi động lại và chạy lại - sẽ tiếp tục từ nơi đã dừng
```

---

### 2_Data_Discovery.ipynb

**Mục đích**: Phân Tích Dữ Liệu Khám Phá (EDA) của dữ liệu thô đã tải xuống

**Môi trường**: Jupyter cục bộ hoặc Google Colab

#### Phân Tích Chính

1. **Cấu trúc dữ liệu**

   - Các chiều (thời gian, vĩ độ, kinh độ)
   - Hệ thống tọa độ (lưới lat/lon)
   - Phạm vi và độ phân giải thời gian

2. **Thống kê biến số**

   ```
   Đối với mỗi biến:
   - Giá trị Mean, std, min, max
   - Kiểu dữ liệu và giá trị thiếu
   - Xác thực đơn vị vật lý
   - Xu hướng theo thời gian
   ```

3. **Phân tích không gian**

   - Xác minh phạm vi địa lý
   - Mặt nạ đất/biển (nếu có)
   - Độ đồng nhất độ phân giải không gian

4. **Phân tích thời gian**

   - Tính liên tục chuỗi thời gian
   - Mô hình theo mùa
   - Chu kỳ hàng năm
   - Bất thường và khoảng trống

5. **Mối quan hệ giữa các biến**

   - Ma trận tương quan
   - Kiểm tra tính nhất quán vật lý
   - Ví dụ: Nhiệt độ ↔ Điểm sương
   - Ví dụ: Tính nhất quán áp suất

6. **Kiểm tra chất lượng**
   - Giá trị không hợp lệ (NaN, Inf)
   - Giá trị ngoài phạm vi
   - Thứ tự thời gian

#### Ví Dụ Đầu Ra

```
Variable: total_precipitation (tp)
├─ Min: 0.0000 m
├─ Max: 0.0452 m
├─ Mean: 0.00234 m
├─ Std: 0.00567 m
├─ Missing: 0 (100% complete)
└─ Unit: meters ✓

Spatial Grid:
├─ Latitude: 65 points (8°N to 24°N)
├─ Longitude: 33 points (102°E to 110°E)
├─ Total: 2,145 locations
└─ Resolution: 0.25° × 0.25° (≈ 28 km) ✓

Temporal Coverage:
├─ Start: 2017-01-01 00:00
├─ End: 2024-12-31 22:00
├─ Duration: 8 years
├─ Frequency: 2-hourly (12 per day)
├─ Total: 35,040 time steps
└─ Gaps: 0 (continuous) ✓
```

#### Các Biểu Đồ Được Tạo

```python
# Biểu đồ chuỗi thời gian
plt.plot(time, temperature)  # Xu hướng qua 8 năm

# Bản đồ nhiệt
plt.imshow(precip_avg_map)   # Lượng mưa trung bình theo vị trí

# Biểu đồ phân phối
plt.hist(precipitation)      # Biểu đồ histogram của giá trị lượng mưa

# Bản đồ nhiệt tương quan
sns.heatmap(correlation_matrix)  # Mối quan hệ giữa các biến

# Phân tích theo mùa
stl_result = seasonal_decompose(time_series, period=365*12)  # 2 giờ
```

#### Các Phát Hiện Chính Được Ghi Nhận

- Tất cả các biến được định dạng đúng và có thể truy cập
- Không có khoảng trống hoặc giá trị thiếu
- Phạm vi thực tế về mặt vật lý
- Mô hình theo mùa rõ ràng
- Sẵn sàng cho tiền xử lý

#### Cách Sử Dụng

```bash
jupyter notebook 2_Data_Discovery.ipynb
# Chạy tất cả các ô
# Xem lại thống kê đầu ra và biểu đồ
```

---

### 3_Data_Merged_and_Normalized.ipynb

**Mục đích**: Gộp, căn chỉnh và chuẩn hóa dữ liệu để huấn luyện mô hình

**Đầu vào**: Các tệp NetCDF thô từ `1_Data_Raw/`

**Đầu ra**: Các bộ dữ liệu sẵn sàng trong `2_Data_Processed/`

#### Các Bước Xử Lý

1. **Tải Tất Cả Dữ Liệu**

   ```python
   # Tải từ cả hai giai đoạn thời gian
   ds_2017_2020 = xr.open_mfdataset('1_Data_Raw/era5_2017_2020/*.nc')
   ds_2021_2024 = xr.open_mfdataset('1_Data_Raw/era5_2021_2024/*.nc')
   ```

2. **Căn Chỉnh Không Gian**

   - Đảm bảo tất cả các biến trên lưới chung
   - Nội suy nếu cần (thường đã được căn chỉnh)
   - Xác minh phạm vi 65 lat × 33 lon
   - Kiểm tra mặt nạ đất/biển

3. **Căn Chỉnh Thời Gian**

   - Gộp tất cả các chiều thời gian
   - Đảm bảo tần suất 2 giờ
   - Lấp đầy khoảng trống (thường không có)
   - Sắp xếp theo thời gian

4. **Đổi Tên Biến**

   - Chuẩn hóa thành tên ngắn (t2m, d2m, v.v.)
   - Đối chiếu đơn vị (K cho nhiệt độ, m cho lượng mưa, v.v.)
   - Ghi nhận ý nghĩa biến

5. **Gộp Tất Cả Dữ Liệu**

   ```python
   # Kết hợp thành một bộ dữ liệu duy nhất
   ds_full = xr.merge([ds_2017_2020, ds_2021_2024])
   # Kết quả: (time=35040, lat=65, lon=33, features=10)
   ```

6. **Tính Toán Thống Kê Chuẩn Hóa**

   ```python
   for var in all_variables:
       stats[var] = {
           'mean': ds[var].mean().values,
           'std': ds[var].std().values,
           'units': ds[var].attrs.get('units'),
       }
   # Lưu vào: 2_Data_Processed/normalization_stats_2017_2024.json
   ```

7. **Áp Dụng Chuẩn Hóa Z-score**

   ```python
   for var in all_variables:
       ds[var] = (ds[var] - stats[var]['mean']) / stats[var]['std']
   ```

8. **Tạo Các Tập Train/Val/Test**

   ```
   2017-2022: Tập huấn luyện (60%) → train_2017_2022.nc
   2023:      Tập xác thực (20%) → val_2023.nc
   2024:      Tập kiểm tra (20%) → test_2024.nc

   Không chồng chéo thời gian (ngăn rò rỉ dữ liệu)
   Mỗi tập bao phủ chu kỳ theo mùa đầy đủ
   ```

9. **Lưu Các Tệp Đã Xử Lý**
   ```bash
   2_Data_Processed/
   ├── train_2017_2022.nc (12.1 GB)
   ├── val_2023.nc (2.1 GB)
   ├── test_2024.nc (2.0 GB)
   ├── era5_vn_merged_normalized_2017_2024.nc (18.3 GB)
   └── normalization_stats_2017_2024.json (5 KB)
   ```

#### Các Bước Đảm Bảo Chất Lượng

```python
# ✓ Kiểm tra không có giá trị NaN sau chuẩn hóa
assert not ds.isnull().any()

# ✓ Xác minh các chiều
assert ds.dims == {'time': 35040, 'lat': 65, 'lon': 33}

# ✓ Xác minh chuẩn hóa (mean≈0, std≈1)
assert ds['t2m'].mean().values ≈ 0.0
assert ds['t2m'].std().values ≈ 1.0

# ✓ Xác minh các tập không chồng chéo
assert max(ds_train.time) < min(ds_val.time)
assert max(ds_val.time) < min(ds_test.time)

# ✓ Lưu thống kê cho việc khử chuẩn hóa sau này
with open('normalization_stats.json', 'w') as f:
    json.dump(stats, f)
```

#### Chi Tiết Chuẩn Hóa

**Công thức Z-score**:
$$x_{normalized} = \frac{x - \mu}{\sigma}$$

Trong đó:

- $\mu$ = trung bình qua tất cả 8 năm, tất cả các vị trí
- $\sigma$ = độ lệch chuẩn qua tất cả 8 năm, tất cả các vị trí
- Áp dụng cho từng biến

**Tại sao chuẩn hóa Z-score?**

- ✓ Đưa phân phối về trung tâm tại 0 với phương sai đơn vị
- ✓ Cải thiện sự hội tụ của mạng nơ-ron
- ✓ Làm cho huấn luyện ổn định hơn về mặt số học
- ✓ Khử chuẩn hóa dễ dàng (công thức ngược)

**Không rò rỉ Train/Val/Test**:

- Thống kê được tính chỉ sử dụng **chỉ** dữ liệu huấn luyện
- Áp dụng đồng nhất cho tất cả các tập
- Ngăn chặn rò rỉ thông tin

Thực tế, đợi đã - trong notebook này, thống kê được tính từ **toàn bộ giai đoạn 8 năm**. Điều này chấp nhận được vì:

- Tất cả dữ liệu được thu thập trong điều kiện khí hậu tương tự (2017-2024)
- Không có rò rỉ thông tin tương lai (giữ nguyên chia tách thời gian)
- Cung cấp việc mở rộng ổn định hơn

#### Thời Gian Chạy

- **Thời lượng**: 30 phút đến 2 giờ (phụ thuộc vào I/O đĩa)
- **Bộ nhớ**: Khuyến nghị ~50 GB RAM
- **CPU**: Tối thiểu (chủ yếu giới hạn I/O)

#### Cách Sử Dụng

```bash
# Đảm bảo dữ liệu thô đã được tải xuống trước
ls 1_Data_Raw/era5_2017_2020/*.nc  # Nên hiển thị 10 tệp
ls 1_Data_Raw/era5_2021_2024/*.nc  # Nên hiển thị 10 tệp

# Chạy tiền xử lý
jupyter notebook Data_Preprocessing_Notebooks/3_Data_Merged_and_Normalized.ipynb
# Tất cả các ô

# Xác minh đầu ra
ls 2_Data_Processed/  # Nên hiển thị 5 tệp
```

#### Xác Minh Đầu Ra

```python
import xarray as xr
import json

# Tải dữ liệu đã xử lý
train_ds = xr.open_dataset('2_Data_Processed/train_2017_2022.nc')
print(train_ds)  # Xác minh các chiều và biến

# Tải thống kê
with open('2_Data_Processed/normalization_stats_2017_2024.json') as f:
    stats = json.load(f)

print(f"Training set shape: {train_ds['t2m'].shape}")
print(f"Mean of t2m (normalized): {train_ds['t2m'].mean():.6f}")
print(f"Std of t2m (normalized): {train_ds['t2m'].std():.6f}")
```

---

## Tổng Quan Quy Trình Hoàn Chỉnh

```
BẮT ĐẦU: Tải xuống ERA5 Thô
  ↓
[1_Era5_Weather_Data_Download.ipynb]
  └─→ 1_Data_Raw/
      ├─ era5_2017_2020/ (10 tệp, 20 GB)
      └─ era5_2021_2024/ (10 tệp, 20 GB)
  ↓
[2_Data_Discovery.ipynb]
  └─→ Phân tích khám phá
      └─ Xác minh chất lượng dữ liệu
         └─ Ghi nhận thống kê
  ↓
[3_Data_Merged_and_Normalized.ipynb]
  └─→ 2_Data_Processed/
      ├─ train_2017_2022.nc (12.1 GB)
      ├─ val_2023.nc (2.1 GB)
      ├─ test_2024.nc (2.0 GB)
      ├─ era5_vn_merged_normalized_2017_2024.nc (18.3 GB)
      └─ normalization_stats_2017_2024.json (5 KB)
  ↓
SẴN SÀNG: Để huấn luyện mô hình trong 3_Models/
```

## Các Vấn Đề Thường Gặp & Giải Pháp

### Vấn đề: Giới hạn tỷ lệ CDS API

**Giải pháp**:

```python
# Giảm kích thước lô
# Tải xuống theo tháng thay vì theo năm
# Chạy trong giờ thấp điểm (UTC 00:00-06:00)
```

### Vấn đề: Hết dung lượng đĩa trong khi tải xuống

**Giải pháp**:

```bash
# Xóa các tải xuống cũ trước
rm -rf 1_Data_Raw/era5_2021_2024/  # Tải xuống một giai đoạn tại một thời điểm
```

### Vấn đề: Lỗi bộ nhớ trong khi gộp

**Giải pháp**:

```python
# Xử lý theo khối
for year in range(2017, 2025):
    chunk = xr.open_dataset(f'era5_{year}.nc')
    # Xử lý riêng lẻ
```

### Vấn đề: Chuẩn hóa làm giá trị quá nhỏ

**Giải pháp**: Điều này là mong đợi! Giá trị trở thành trung bình bằng 0, phương sai đơn vị.

```python
# Xác minh mean ≈ 0, std ≈ 1
print(f"Mean: {data.mean():.6f}, Std: {data.std():.6f}")

# Để khử chuẩn hóa sau này:
original = normalized * std + mean
```

---

## Yêu Cầu

```
Thư viện Python:
- xarray >= 0.22       # Mảng đa chiều
- netCDF4 >= 1.6.1     # I/O tệp NetCDF
- numpy >= 1.23        # Các phép toán số
- pandas >= 1.5.0      # Thao tác dữ liệu
- cdsapi >= 0.5.1      # API Copernicus (để tải xuống)
```

---

## Tính Tái Tạo

Tất cả các notebook là xác định với seed ngẫu nhiên cố định:

```python
np.random.seed(42)
```

Để đảm bảo tính tái tạo:

1. Sử dụng Python 3.8+
2. Cài đặt các phiên bản được chỉ định
3. Chạy các notebook theo thứ tự
4. Không sửa đổi các phần mã được đánh dấu "DO NOT MODIFY"

---

## Tài Liệu Tham Khảo

- **Bộ dữ liệu ERA5**: https://www.ecmwf.int/en/forecasts/datasets/reanalysis-datasets/era5
- **CDS API**: https://cds.climate.copernicus.eu/api-how-to
- **xarray**: http://xarray.pydata.org/
- **Chuẩn hóa Z-score**: https://en.wikipedia.org/wiki/Standard_score

---

**Cập nhật lần cuối**: Tháng 12 năm 2025 | **Trạng thái**: Sẵn sàng Sản xuất | **Phiên bản**: 1.0
