# Dữ Liệu Thô - Bộ Dữ Liệu Tái Phân Tích ERA5

Thư mục này chứa dữ liệu khí tượng thô được tải xuống từ **Kho Dữ Liệu Khí Hậu Copernicus (ERA5)**.

## Tổng Quan

- **Nguồn Dữ Liệu**: ERA5 Reanalysis (ECMWF)
- **Phạm Vi Thời Gian**: 2017-2024 (8 năm)
- **Độ Phân Giải Không Gian**: 0.25° × 0.25°
- **Độ Phân Giải Thời Gian**: 2 giờ/lần (00:00, 02:00, 04:00, ..., 22:00)
- **Khu Vực Địa Lý**: Việt Nam (8°N-24°N, 102°E-110°E)
- **Định Dạng**: NetCDF4

## Cấu Trúc Thư Mục

```
era5_2017_2020/              # Giai đoạn 1: 2017-2020
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

era5_2021_2024/              # Giai đoạn 2: 2021-2024
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

## Các Biến Được Bao Gồm

| Biến                             | Tên Viết Tắt | Đơn Vị | Mô Tả                                      |
| -------------------------------- | ------------ | ------ | ------------------------------------------ |
| Nhiệt Độ 2m                      | t2m          | K      | Nhiệt độ không khí ở độ cao 2m trên bề mặt |
| Nhiệt Độ Điểm Sương 2m           | d2m          | K      | Nhiệt độ điểm sương ở độ cao 2m            |
| Thành Phần Gió U 10m             | u10          | m/s    | Thành phần gió hướng đông ở độ cao 10m     |
| Thành Phần Gió V 10m             | v10          | m/s    | Thành phần gió hướng bắc ở độ cao 10m      |
| Áp Suất Mực Nước Biển Trung Bình | msl          | Pa     | Áp suất quy về mực nước biển trung bình    |
| Áp Suất Bề Mặt                   | sp           | Pa     | Áp suất tại bề mặt                         |
| Tổng Lượng Mưa                   | tp           | m      | Lượng mưa tích lũy (biến mục tiêu)         |
| Bức Xạ Mặt Trời Bề Mặt           | ssrd         | J/m²   | Bức xạ mặt trời chiếu xuống bề mặt         |
| Nhiệt Độ Bề Mặt                  | skt          | K      | Nhiệt độ của lớp bề mặt                    |
| Tổng Hơi Nước Cột                | tcwv         | kg/m²  | Hơi nước tích phân theo phương thẳng đứng  |

## Đặc Điểm Dữ Liệu

### Phạm Vi Không Gian

- **Biên Phía Bắc**: 24°N (Miền Núi Phía Bắc)
- **Biên Phía Nam**: 8°N (Bờ Biển Phía Nam)
- **Biên Phía Tây**: 102°E (Biên Giới Lào)
- **Biên Phía Đông**: 110°E (Biển Đông)
- **Điểm Lưới**: 65 vĩ độ × 33 kinh độ = 2,145 vị trí không gian

### Chi Tiết Thời Gian

- **Khoảng Thời Gian**: 2017-01-01 00:00 đến 2024-12-31 22:00
- **Điểm Dữ Liệu**: ~34,944 bước thời gian × 10 biến × 2,145 vị trí
- **Tổng Dung Lượng**: ~40 GB (không nén cho cả hai giai đoạn)

## Ghi Chú Chất Lượng Dữ Liệu

1. **Sản Phẩm Tái Phân Tích**: ERA5 là bộ dữ liệu tái phân tích kết hợp dữ liệu mô hình với quan sát
2. **Không Có Giá Trị Thiếu**: Copernicus đảm bảo độ bao phủ đầy đủ với việc lấp đầy khoảng trống
3. **Vấn Đề Đã Biết**:
   - Lượng mưa mùa có thể bị đánh giá thấp ở địa hình phức tạp
   - Các khu vực độ cao lớn có thể có độ chính xác thấp hơn
4. **Xử Lý**: Các biến riêng lẻ được chia theo giai đoạn thời gian để đảm bảo độ tin cậy khi tải xuống

## Truy Cập Dữ Liệu

### Tùy Chọn 1: Sử Dụng Dữ Liệu Đã Xử Lý (Khuyến Nghị)

Nếu bạn chỉ cần các bộ dữ liệu đã xử lý và chuẩn hóa:

```bash
cd ../2_Data_Processed/
# Sử dụng train_2017_2022.nc, val_2023.nc, test_2024.nc
```

### Tùy Chọn 2: Tải Lại Dữ Liệu Thô

Để tải lại từ CDS:

```bash
jupyter notebook ../Data_Preprocessing_Notebooks/1_Era5_Weather_Data_Download.ipynb
```

Yêu cầu:

- Khóa API CDS từ https://cds.climate.copernicus.eu/
- ~50GB dung lượng đĩa trống
- ~2-3 giờ thời gian tải xuống (tùy thuộc vào kết nối)

### Tùy Chọn 3: Truy Vấn CDS Trực Tiếp

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
        'month': ['01', '02', '03', '04', '05', '06', '07', '08', '09', '10', '11', '12'],
        'day': [f'{i:02d}' for i in range(1, 32)],
        'time': ['00:00', '02:00', '04:00', '06:00', '08:00', '10:00', '12:00', '14:00', '16:00', '18:00', '20:00', '22:00'],
        'area': [24, 102, 8, 110],  # North, West, South, East
    },
    'output.nc'
)
```

## Quy Ước Đặt Tên Tệp

Định dạng: `era5_vn_{variable}_{year_start}_{year_end}.nc`

Ví dụ: `era5_vn_total_precipitation_2017_2020.nc`

- `era5_vn`: Tiền tố ERA5 Việt Nam
- `{variable}`: Tên biến khí tượng
- `{year_start}_{year_end}`: Giai đoạn thời gian

## Tham Khảo Dung Lượng

Mỗi tệp NetCDF (điển hình):

- **Biến nhiệt độ**: ~800 MB mỗi tệp
- **Biến áp suất**: ~600 MB mỗi tệp
- **Lượng mưa**: ~900 MB (biến mục tiêu quan trọng)
- **Tổng mỗi giai đoạn**: ~8 GB

## Quy Trình Xử Lý

```
era5_2017_2020/*.nc + era5_2021_2024/*.nc
                    ↓
        [Notebook Khám Phá Dữ Liệu]
        (phân tích khám phá)
                    ↓
    [Notebook Gộp và Chuẩn Hóa Dữ Liệu]
    (căn chỉnh, gộp, chuẩn hóa)
                    ↓
        2_Data_Processed/
        (chia tập train/val/test)
                    ↓
        3_Models/
        (huấn luyện & suy luận)
```

## Tài Liệu Tham Khảo

- **Tài Liệu ERA5**: https://www.ecmwf.int/en/forecasts/datasets/reanalysis-datasets/era5
- **Hướng Dẫn API CDS**: https://cds.climate.copernicus.eu/api-how-to
- **Trích Dẫn Bộ Dữ Liệu**: Hersbach et al. (2020) - https://doi.org/10.1038/s41586-020-2438-0

---

**Lưu ý**: Thư mục này được bao gồm trong kho lưu trữ để tham khảo. Để có đầy đủ chức năng, hãy đảm bảo các tệp có mặt trước khi chạy các notebook tiền xử lý hoặc huấn luyện.
