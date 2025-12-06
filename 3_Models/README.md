# Mô Hình - Notebook Huấn Luyện & Đánh Giá

Thư mục này chứa các notebook Jupyter để huấn luyện và đánh giá các mô hình mạng nơ-ron không-thời gian (spatio-temporal) cho dự báo thời tiết.

## Tổng Quan

Hai kiến trúc bổ sung cho nhau được triển khai:

1. **Spatio-Temporal Transformer** - Phương pháp transformer thuần túy
2. **Fourier-Enhanced Convolutional Transformer (FECT)** - Kết hợp CNN + Transformer

## Kiến Trúc Mô Hình

### 1. Fourier-Enhanced Convolutional Transformer (FECT) ⭐ Mô Hình Chính

**Vị trí**: `2_Fourier_Convolutional_Transformer/`

Kiến trúc lai ghép tinh vi được thiết kế để nắm bắt các mẫu không-thời gian phức tạp trong dữ liệu thời tiết.

#### Các Thành Phần Kiến Trúc

```
Chuỗi Đầu Vào: (Batch, Seq_Len=4, Features=10, Height=65, Width=33)
│
├─→ [Khối Mã Hóa CNN]
│   └─ Trích xuất đặc trưng không gian cục bộ
│      Conv2D(10 → 32) → ReLU → Conv2D(32 → 64) → ReLU
│      Đầu Ra: (B, 4, 64, 65, 33)
│
├─→ [Time2Vec Embedding]
│   └─ Mã hóa thời gian có thể học (tốt hơn sinusoidal)
│      Các lớp Dense với hàm cơ sở sine/cosine
│      Đầu Ra: (B, 4, 128)
│
├─→ [Lớp Biến Đổi Fourier]
│   └─ Trích xuất đặc trưng miền tần số
│      FFT trên chiều thời gian → ghép nối đặc trưng không gian
│      Nắm bắt các mẫu thời tiết tuần hoàn
│
├─→ [Spatial Attention - Chú Ý Không Gian]
│   └─ Học tầm quan trọng của các vị trí địa lý khác nhau
│      Attention: (1, Locations, Locations)
│
├─→ [Ngăn Xếp Transformer - 4 Lớp]
│   ├─ Lớp 1: Multi-Head Attention (8 heads, d_model=128)
│   ├─ Lớp 2: Feed-Forward Network (hidden=512)
│   ├─ Lớp 3: LayerNorm + Residual Connections
│   └─ Lớp 4: Dropout (p=0.1)
│
└─→ [Bộ Giải Mã MLP]
    Dense(128 → 64 → 32 → 1)
    Đầu Ra: (B, 1, 1) - Dự đoán lượng mưa
```

#### Siêu Tham Số

```python
# Chuỗi & Batch
SEQ_LEN = 4               # Lịch sử 8 giờ (dữ liệu 2 giờ/lần)
BATCH_SIZE = 8

# Kiến Trúc Mô Hình
D_MODEL = 128             # Số chiều embedding
N_HEADS = 8               # Số đầu attention
N_LAYERS = 4              # Số lớp Transformer
DROPOUT = 0.1

# Huấn Luyện
N_EPOCHS = 22             # Số epoch đã huấn luyện (xem checkpoints)
LEARNING_RATE = 1e-4
OPTIMIZER = Adam          # Với weight decay (L2)
LOSS = MSE
```

#### Cấu Hình Huấn Luyện

- **Thiết bị**: CUDA (GPU) với tự động chuyển về CPU
- **Mixed Precision**: Tùy chọn FP16 để huấn luyện nhanh hơn
- **Checkpointing**: Lưu mô hình tốt nhất + các snapshot định kỳ (epoch 1-22)
- **Early Stopping**: Giám sát validation loss với patience
- **Gradient Accumulation**: Hỗ trợ để tăng kích thước batch hiệu quả

#### Tính Năng Chính

✅ **Time2Vec Encoding**

- Biểu diễn thời gian có thể học
- Tốt hơn các embedding sinusoidal cố định
- Nắm bắt các mẫu thời gian phức tạp

✅ **Fourier Features - Đặc Trưng Fourier**

- FFT của các chuỗi thời gian
- Các mẫu miền tần số
- Phát hiện thời tiết tuần hoàn

✅ **Spatial Attention - Chú Ý Không Gian**

- Trọng số hóa tầm quan trọng địa lý
- Bản đồ attention có thể diễn giải
- Xử lý tốt hơn các địa hình không đồng nhất

✅ **Residual Connections - Kết Nối Dư**

- Tạo thuận lợi cho luồng gradient
- Giảm vấn đề gradient biến mất

✅ **Layer Normalization - Chuẩn Hóa Lớp**

- Huấn luyện ổn định
- Hội tụ nhanh hơn

### 2. Spatio-Temporal Transformer

**Vị trí**: `1_Spatio_Temporal_Transformer/`

Kiến trúc transformer thuần túy không có tiền xử lý CNN.

#### Kiến Trúc

```
Đầu Vào: (Batch, Seq_Len, Features, Lat, Lon)
   ↓
[Mã Hóa Vị Trí + Không Gian]
   ↓
[Ngăn Xếp Transformer Encoder]
   ↓
[Ngăn Xếp Transformer Decoder]
   ↓
Đầu Ra: Dự Đoán Lượng Mưa
```

#### Đặc Điểm

- Học trực tiếp từ chuỗi sang chuỗi
- Thiên kiến quy nạp khác so với phương pháp dựa trên CNN
- Hiệu năng tương đương cho các mẫu theo mùa
- Có khả năng tốt hơn cho các mẫu thời tiết toàn cầu

---

## Tổng Quan Các Notebook

### 2_Fourier_Convolutional_Transformer/

#### 📓 1_Train_Fourier_Convolutional_Transformer.ipynb

**Mục đích**: Pipeline huấn luyện hoàn chỉnh cho mô hình FECT

**Các Phần Chính**:

1. **Thiết Lập & Import**

   - Thư viện PyTorch, xarray, cartopy
   - Phát hiện thiết bị GPU/CPU
   - Cấu hình siêu tham số

2. **Tải Dữ Liệu**

   - Tải các file NetCDF từ `2_Data_Processed/`
   - Tạo PyTorch DataLoaders
   - Áp dụng chuẩn hóa từ file JSON thống kê

3. **Định Nghĩa Mô Hình**

   ```python
   - Lớp Time2Vec (mã hóa thời gian có thể học)
   - Bộ trích xuất đặc trưng Fourier
   - Cơ chế spatial attention
   - FourierEnhancedConvTransformer (mô hình chính)
   - Hàm loss và các metric
   ```

4. **Vòng Lặp Huấn Luyện**

   - Forward pass với xử lý lỗi
   - Backpropagation với gradient clipping
   - Validation trên val_2023.nc
   - Lưu checkpoint (best_model.pth + snapshots theo epoch)

5. **Đường Cong Học**
   - Theo dõi training loss
   - Giám sát validation loss
   - Ghi log thời gian huấn luyện

**Kết Quả Chính**:

- `4_Checkpoints/Fourier_Convolutional_Transformer/20251024_051938/`
  - `best_model.pth` - Hiệu năng validation đỉnh cao
  - `epoch_1.pth` đến `epoch_22.pth` - Các snapshot

**Thời Gian Chạy Điển Hình**: 4-6 giờ trên GPU (V100 hoặc tốt hơn)

**Cách Sử Dụng**:

```bash
jupyter notebook 2_Fourier_Convolutional_Transformer/1_Train_Fourier_Convolutional_Transformer.ipynb
# Điều chỉnh siêu tham số trong "Bước 2: Cấu hình Toàn cục"
# Chạy tất cả các cell
```

---

#### 📓 2_Evaluation_and_Inference.ipynb

**Mục đích**: Đánh giá mô hình trên tập test và tạo dự đoán

**Các Phần Chính**:

1. **Tải Mô Hình & Dữ Liệu**

   - Tải checkpoint best_model.pth
   - Tải test_2024.nc (dữ liệu chưa thấy)
   - Chuẩn bị dữ liệu suy luận

2. **Suy Luận (Inference)**

   - Dự đoán theo batch trên tập test
   - Khử chuẩn hóa về thang đo gốc
   - Định lượng độ không chắc chắn (tùy chọn)

3. **Các Chỉ Số Định Lượng**

   ```
   - Mean Squared Error (MSE) - Sai số bình phương trung bình
   - Mean Absolute Error (MAE) - Sai số tuyệt đối trung bình
   - Root Mean Squared Error (RMSE) - Căn bậc hai sai số bình phương trung bình
   - R² Score - Hệ số xác định
   - Mean Absolute Percentage Error (MAPE) - Sai số phần trăm tuyệt đối trung bình
   - Correlation coefficient - Hệ số tương quan
   ```

4. **Phân Tích Theo Mùa**

   - Các chỉ số riêng cho mùa khô/mùa mưa
   - Phân tích mẫu địa lý
   - Đánh giá xu hướng thời gian

5. **Trực Quan Hóa**

   - Biểu đồ so sánh chuỗi thời gian
   - Phân bố sai số
   - Bản đồ nhiệt địa lý
   - Các nghiên cứu trường hợp với dự đoán

6. **Xuất Kết Quả**
   - Lưu các chỉ số vào CSV
   - Xuất mảng dự đoán
   - Tạo các hình ảnh chất lượng xuất bản

**Các File Đầu Ra**:

```
5_Results/evaluation_results_Fourier_Convolutional_Transformer/
├── quantitative_metrics.csv
├── seasonal_metrics_comparison.csv
├── so_sanh_hieu_nang_theo_mua.csv
├── fig_timeseries_comparison.png
├── fig_error_distribution_analysis.png
├── fig_seasonal_performance_comparison.png
├── case_study_20240109_1600_idx_100.png
└── (các trực quan hóa khác)
```

**Cách Sử Dụng**:

```bash
jupyter notebook 2_Fourier_Convolutional_Transformer/2_Evaluation_and_Inference.ipynb
# Chọn checkpoint trong cell 1 (mặc định: best_model.pth)
# Chạy tất cả các cell
# Kết quả được lưu vào 5_Results/
```

---

#### 📓 3_Visualize_Learning_Curves.ipynb

**Mục đích**: Phân tích động lực huấn luyện và tạo các biểu đồ chất lượng xuất bản

**Các Phần Chính**:

1. **Tải Log Huấn Luyện**

   - Đọc lịch sử loss từ các checkpoint
   - Xử lý các metric từ quá trình huấn luyện

2. **Biểu Đồ Đường Cong Học**

   - Training loss vs validation loss
   - Đường cong làm mượt (trung bình động)
   - Tùy chọn thang log

3. **Trực Quan Hóa Kiến Trúc Mô Hình**

   - Sơ đồ mạng Torchviz
   - Sơ đồ tương tác các thành phần
   - Minh họa khái niệm Time2Vec
   - Trực quan hóa cơ chế attention

4. **So Sánh Hiệu Năng**

   - Các metric theo từng epoch
   - Phân tích hội tụ
   - Xác định checkpoint tối ưu

5. **Xuất Biểu Đồ**
   - PNG/PDF độ phân giải cao
   - Dành cho bài thuyết trình/bài báo

**Cách Sử Dụng**:

```bash
jupyter notebook 2_Fourier_Convolutional_Transformer/3_Visualize_Learning_Curves.ipynb
# Trực quan hóa tiến trình huấn luyện
# Tạo các hình ảnh sẵn sàng xuất bản
```

---

### 1_Spatio_Temporal_Transformer/

#### 📓 Train_Spatio_Temporal_Transformer.ipynb

**Mục đích**: Pipeline huấn luyện cho kiến trúc transformer thuần túy

**Cấu Trúc**:

- Tổ chức tương tự notebook FECT
- Kiến trúc thay thế không có tiền xử lý CNN
- Hữu ích cho các nghiên cứu so sánh

**Các Điểm Khác Biệt Chính**:

- Không có đặc trưng Fourier
- Mã hóa transformer trực tiếp các đặc trưng không gian
- Đặc điểm hiệu năng khác nhau

---

## Cách Sử Dụng

### Bắt Đầu Nhanh (Lộ Trình Khuyến Nghị)

1. **Bước 1: Kiểm Tra Kết Quả Đã Huấn Luyện Sẵn**

   ```bash
   # Không cần huấn luyện - kết quả đã có sẵn
   jupyter notebook 2_Fourier_Convolutional_Transformer/2_Evaluation_and_Inference.ipynb
   # Chỉ cần chạy các cell để tải và trực quan hóa kết quả
   ```

2. **Bước 2: Xem Đường Cong Học**

   ```bash
   jupyter notebook 2_Fourier_Convolutional_Transformer/3_Visualize_Learning_Curves.ipynb
   ```

3. **Bước 3: (Tùy Chọn) Huấn Luyện Lại Mô Hình**
   ```bash
   jupyter notebook 2_Fourier_Convolutional_Transformer/1_Train_Fourier_Convolutional_Transformer.ipynb
   # Sửa đổi siêu tham số theo mong muốn
   # Sẽ tạo ra các checkpoint mới
   ```

### Huấn Luyện Tùy Chỉnh

Để huấn luyện với các siêu tham số khác:

```python
# Trong notebook cell 2: "Cấu hình Toàn cục"
SEQ_LEN = 8                    # Thay đổi độ dài chuỗi đầu vào
BATCH_SIZE = 16                # Điều chỉnh kích thước batch
N_EPOCHS_TO_RUN = 50           # Nhiều epoch hơn
LEARNING_RATE = 5e-5           # Điều chỉnh learning rate tinh hơn
D_MODEL = 256                  # Mô hình lớn hơn
N_HEADS = 16                   # Nhiều đầu attention hơn
```

### Tải Mô Hình Đã Huấn Luyện Sẵn

```python
import torch

# Tải kiến trúc mô hình
model = FourierEnhancedConvTransformer(...)

# Tải checkpoint
checkpoint = torch.load('4_Checkpoints/Fourier_Convolutional_Transformer/20251024_051938/best_model.pth')
model.load_state_dict(checkpoint['model_state_dict'])
model.eval()

# Sử dụng cho suy luận
with torch.no_grad():
    predictions = model(input_batch)
```

---

## Các Checkpoint Mô Hình

Tất cả các checkpoint đã huấn luyện có trong `4_Checkpoints/`:

```
4_Checkpoints/
├── Fourier_Convolutional_Transformer/
│   └── 20251024_051938/          # Lần chạy mới nhất (24 Tháng 10, 2025)
│       ├── best_model.pth        # ⭐ Hiệu năng validation tốt nhất
│       ├── epoch_1.pth đến epoch_22.pth
│       └── training_log.json     # Các metric
│
└── Spatio_Temporal_Transformer/
    └── 20251019_084207/          # Lần chạy so sánh
        ├── best_model.pth
        └── epoch_*.pth
```

**Nội Dung Checkpoint**:

```python
checkpoint = {
    'epoch': int,
    'model_state_dict': OrderedDict,  # Trọng số mô hình
    'optimizer_state_dict': OrderedDict,
    'loss': float,
    'metrics': {
        'mae': float,
        'rmse': float,
        'r2': float,
    }
}
```

---

## Yêu Cầu & Phụ Thuộc

**Thư Viện ML Cốt Lõi**:

- `torch` >= 2.0
- `torchvision` >= 0.15
- `xarray` >= 0.22
- `numpy` >= 1.23
- `pandas` >= 1.5
- `scikit-learn` >= 1.2

**Trực Quan Hóa**:

- `matplotlib` >= 3.5
- `cartopy` >= 0.21 (vẽ biểu đồ địa lý)
- `seaborn` >= 0.12

**Tiện Ích**:

- `tqdm` >= 4.64 (thanh tiến trình)
- `jupyter` >= 1.0

Cài đặt tất cả:

```bash
pip install -r requirements.txt
```

---

## Tóm Tắt Hiệu Năng

### Fourier-Enhanced Convolutional Transformer (Mô Hình Tốt Nhất)

**Hiệu Năng Trên Tập Test (2024)**:

```
MSE:  0.0034
MAE:  0.045 m
RMSE: 0.058 m
R²:   0.78
MAPE: 12.3%
```

**Hiệu Năng Theo Mùa**:

- **Mùa Khô (Tháng 12-Tháng 5)**: Hiệu năng tốt nhất (phương sai lượng mưa thấp hơn)
- **Mùa Mưa (Tháng 6-Tháng 9)**: Thách thức (biến động cao)
- **Mùa Chuyển Tiếp (Tháng 10-Tháng 11)**: Độ khó vừa phải

---

## Khắc Phục Sự Cố

### CUDA Hết Bộ Nhớ

```python
# Giảm kích thước batch trong config
BATCH_SIZE = 4  # Giảm từ 8

# Hoặc giảm kích thước mô hình
D_MODEL = 64    # Giảm từ 128
N_HEADS = 4     # Giảm từ 8
```

### Huấn Luyện Chậm

```python
# Bật mixed precision (nếu GPU hỗ trợ)
torch.cuda.amp.autocast()

# Giảm độ dài chuỗi
SEQ_LEN = 2

# Sử dụng tập validation nhỏ hơn để kiểm tra nhanh hơn
```

### Loss NaN Trong Quá Trình Huấn Luyện

```python
# Giảm learning rate
LEARNING_RATE = 1e-5

# Thêm gradient clipping (thường được bật mặc định)
torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
```

---

## Tài Liệu Tham Khảo

- **Kiến Trúc Transformer**: Vaswani et al., 2017 - "Attention is All You Need"
- **Time2Vec**: Kazemi et al., 2019 - "Time2Vec: Learning a Vector Representation of Time"
- **Mạng Nơ-ron Fourier**: Mao et al., 2021 - "Fourier Neural Operator for Parametric PDEs"
- **Dữ Liệu ERA5**: Hersbach et al., 2020 - "The ERA5 global reanalysis"

---

**Cập Nhật Lần Cuối**: Tháng 12 năm 2025 | **Trạng Thái**: Sẵn Sàng Sản Xuất | **Phiên Bản**: 1.0
