# Đóng góp cho Dự án Dự báo Mưa

Cảm ơn bạn đã quan tâm đến việc đóng góp! Tài liệu này cung cấp hướng dẫn về cách đóng góp cho dự án.

## Quy tắc Ứng xử

- Tôn trọng và hòa nhập
- Chào đón phản hồi và các quan điểm khác nhau
- Tập trung vào những gì tốt nhất cho cộng đồng

## Cách Đóng góp

### 1. Báo cáo Vấn đề

Tìm thấy lỗi? Có đề xuất? Mở một issue với:

- **Tiêu đề rõ ràng**: Mô tả vấn đề một cách súc tích
- **Mô tả**: Điều gì đã xảy ra và điều gì nên xảy ra
- **Các bước tái hiện**: Nếu đó là lỗi
- **Môi trường**: Phiên bản Python, hệ điều hành, thông tin GPU
- **Ảnh chụp màn hình/Logs**: Nếu có

**Mẫu Issue**:

```
## Description
Mô tả ngắn gọn về vấn đề

## Steps to Reproduce
1. Bước 1
2. Bước 2
3. ...

## Expected Behavior
Điều gì nên xảy ra

## Actual Behavior
Điều gì thực sự xảy ra

## Environment
- OS: [ví dụ: Windows 10]
- Python Version: [ví dụ: 3.9]
- PyTorch Version: [ví dụ: 2.0]
- GPU: [ví dụ: NVIDIA V100]
```

### 2. Đề xuất Cải tiến

Các ý tưởng cải tiến luôn được chào đón!

- Sử dụng tiêu đề mô tả rõ ràng
- Cung cấp mô tả chi tiết
- Giải thích trường hợp sử dụng
- Đề xuất cách triển khai có thể

### 3. Gửi Thay đổi Code

#### Fork & Clone

```bash
git clone https://github.com/yourusername/Rain_Forecast_Project.git
cd Rain_Forecast_Project
```

#### Tạo Feature Branch

```bash
git checkout -b feature/your-feature-name
# hoặc
git checkout -b fix/bug-description
```

#### Thực hiện Thay đổi

Tuân theo các hướng dẫn sau:

- Viết code sạch, dễ đọc
- Thêm comment cho logic phức tạp
- Tuân theo hướng dẫn style PEP 8
- Cập nhật docstrings

#### Commit Thay đổi

```bash
git add .
git commit -m "feat: Add description of changes

- Điểm 1
- Điểm 2

Closes #issue_number"
```

**Định dạng Commit Message**:

```
<type>: <subject>

<body>

Closes #<issue_number>
```

Các loại: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

#### Push & Tạo Pull Request

```bash
git push origin feature/your-feature-name
# Sau đó tạo PR trên GitHub
```

**Mẫu Pull Request**:

```
## Description
Mô tả ngắn gọn về các thay đổi

## Type of Change
- [ ] Sửa lỗi
- [ ] Tính năng mới
- [ ] Cập nhật tài liệu
- [ ] Cải thiện hiệu suất

## Changes Made
- Thay đổi 1
- Thay đổi 2

## Testing
Đã được test như thế nào?

## Related Issues
Closes #issue_number

## Checklist
- [ ] Code tuân theo hướng dẫn style
- [ ] Đã thêm comment cho logic phức tạp
- [ ] Đã cập nhật tài liệu
- [ ] Đã thêm/cập nhật tests
- [ ] Không có cảnh báo mới
```

---

## Thiết lập Môi trường Phát triển

### Cài đặt Môi trường

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install in development mode
pip install -e .
pip install -r requirements.txt
pip install -r requirements-dev.txt  # Additional dev dependencies
```

### Chất lượng Code

```bash
# Format code
black .

# Check style
pylint src/

# Sort imports
isort .

# Run tests
pytest tests/
```

### Pre-commit Hooks (Tùy chọn)

```bash
pip install pre-commit
pre-commit install
```

---

## Các Lĩnh vực Đóng góp

### 1. Sửa Lỗi ✅

Giúp sửa các vấn đề hiện có. Kiểm tra các issue có nhãn `bug`.

### 2. Cải thiện Hiệu suất 🚀

- Tối ưu hóa suy luận mô hình
- Giảm sử dụng bộ nhớ
- Tải dữ liệu nhanh hơn
- Cải thiện sử dụng GPU

### 3. Tài liệu 📚

- Thêm docstrings
- Cải thiện các phần README
- Thêm ví dụ
- Viết hướng dẫn
- Sửa lỗi chính tả

### 4. Tính năng Mới 🎯

Các lĩnh vực tiềm năng:

- Dự báo nhiều bước trước
- Phương pháp ensemble
- Các biến khí tượng bổ sung
- Transfer learning theo vùng
- API suy luận thời gian thực
- Định lượng độ không chắc chắn
- Tối ưu hóa siêu tham số
- Nén mô hình (quantization, pruning)

### 5. Testing 🧪

- Thêm unit tests
- Integration tests
- Model validation tests
- Data pipeline tests

### 6. Ví dụ & Hướng dẫn 📖

- Ví dụ sử dụng
- Hướng dẫn tải dữ liệu
- Hướng dẫn tinh chỉnh mô hình
- Hướng dẫn triển khai
- Ví dụ transfer learning

---

## Hướng dẫn Cấu trúc Dự án

### Quy ước Notebook

```
Data_Preprocessing_Notebooks/
├── 1_*.ipynb          # Loading/collection
├── 2_*.ipynb          # Exploration/analysis
└── 3_*.ipynb          # Preprocessing/transformation

3_Models/
├── 1_*.ipynb          # Training
├── 2_*.ipynb          # Evaluation
└── 3_*.ipynb          # Visualization
```

### Tổ chức Code

- Giữ các hàm tiện ích trong modules (không chỉ notebooks)
- Ghi chú tất cả các hàm bằng docstrings
- Sử dụng type hints khi có thể
- Tuân theo nguyên tắc DRY (Don't Repeat Yourself)

### File Dữ liệu

- Sử dụng đường dẫn tương đối để truy cập dữ liệu
- Ghi chú các yêu cầu định dạng dữ liệu
- Thêm kiểm tra validation

---

## Testing

### Chạy Tests Hiện có

```bash
pytest tests/
pytest tests/test_models.py -v  # Specific test file
pytest tests/test_models.py::test_function_name  # Specific test
```

### Viết Tests Mới

```python
# tests/test_example.py

import pytest
from your_module import function_to_test

def test_function_basic():
    """Test basic functionality."""
    result = function_to_test(input_data)
    assert result == expected_output

def test_function_edge_case():
    """Test edge cases."""
    with pytest.raises(ValueError):
        function_to_test(invalid_input)

@pytest.mark.parametrize("input,expected", [
    (1, 2),
    (2, 4),
    (3, 6),
])
def test_function_parametrized(input, expected):
    """Test with multiple inputs."""
    assert function_to_test(input) == expected
```

---

## Tài liệu

### Định dạng Docstring

```python
def function_name(param1: int, param2: str) -> dict:
    """Mô tả ngắn gọn.

    Mô tả dài hơn nếu cần.

    Args:
        param1 (int): Mô tả param1
        param2 (str): Mô tả param2

    Returns:
        dict: Mô tả giá trị trả về

    Raises:
        ValueError: Khi param1 là số âm

    Example:
        >>> result = function_name(10, "test")
        >>> print(result)
        {'status': 'success'}
    """
    pass
```

### Hướng dẫn Phần README

- Tiêu đề rõ ràng với emoji (tùy chọn)
- Mô tả ngắn gọn
- Ví dụ code
- Liên kết đến nội dung liên quan
- Giữ nguyên tắc DRY (tránh trùng lặp)

---

## Quy trình Review

### Những gì Chúng tôi Tìm kiếm

- ✅ Chất lượng code và khả năng đọc
- ✅ Tính đầy đủ của tài liệu
- ✅ Độ bao phủ test
- ✅ Không có thay đổi phá vỡ
- ✅ Các cân nhắc về hiệu suất
- ✅ Các vấn đề bảo mật đã được giải quyết

### Chu kỳ Phản hồi

1. Gửi PR
2. Maintainers review
3. Giải quyết phản hồi
4. Review lại nếu cần
5. Merge khi được chấp thuận

### Thời gian

- Review ban đầu: Trong vòng 1-2 tuần
- Theo dõi: Trong vòng 3-5 ngày
- Merge: Sau khi được chấp thuận và vượt qua CI

---

## Hướng dẫn Style

### Python Code

- Tuân theo [PEP 8](https://pep8.org/)
- Sử dụng thụt lề 4 khoảng trắng
- Độ dài dòng tối đa: 100 ký tự
- Sử dụng tên biến có ý nghĩa
- Thêm comment cho logic phức tạp

### Notebook Style

- Tiêu đề phần rõ ràng (# Heading)
- Một khái niệm mỗi cell (khi có thể)
- Comment cell mô tả
- Không có imports không sử dụng
- Định dạng output nhất quán

### Git Commits

- Sử dụng thì hiện tại: "Add feature" không phải "Added feature"
- Sử dụng cách mệnh lệnh: "Move cursor to..." không phải "Moves cursor..."
- Giới hạn 72 ký tự cho dòng chủ đề
- Tham chiếu issues: "Fixes #123"

---

## Quy trình Phát hành

### Đánh số Phiên bản

Sử dụng semantic versioning: `MAJOR.MINOR.PATCH`

- `MAJOR`: Thay đổi phá vỡ
- `MINOR`: Tính năng mới (tương thích ngược)
- `PATCH`: Sửa lỗi

### Checklist Phát hành

- [ ] Cập nhật VERSION
- [ ] Cập nhật CHANGELOG
- [ ] Cập nhật tài liệu
- [ ] Chạy toàn bộ test suite
- [ ] Tag release trên GitHub
- [ ] Tạo GitHub release notes

---

## Nhận Trợ giúp

### Có câu hỏi?

- Kiểm tra các issues/discussions hiện có
- Đọc tài liệu
- Mở một discussion (không phải issue)
- Liên hệ maintainers

### Tài nguyên

- [PyTorch Documentation](https://pytorch.org/docs/)
- [xarray Documentation](https://xarray.pydata.org/)
- [Python Style Guide](https://pep8.org/)
- [Git Guide](https://git-scm.com/book/)

---

## Ghi nhận

Những người đóng góp được ghi nhận trong:

- Phần Contributors của README.md
- Trang "Contributors" trên GitHub
- Release notes

Cảm ơn bạn đã đóng góp! 🎉

---

**Có câu hỏi?** Mở một discussion hoặc issue!

**Chúc đóng góp vui vẻ!** 🚀
