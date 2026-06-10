[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=24112955&assignment_repo_type=AssignmentRepo)

# Day 10 Lab: Data Pipeline & Data Observability

**Student ID:** AI20K-2A202600693  
**Student Email:** 26ai.bangnk@vinuni.edu.vn  
**Name:** Nguyễn Khánh Bằng

## Mô tả

Dự án xây dựng một ETL pipeline tự động để xử lý dữ liệu sản phẩm từ file
JSON và xuất kết quả sạch sang CSV. Pipeline gồm bốn bước:

1. **Extract:** đọc các bản ghi sản phẩm từ `raw_data.json`.
2. **Validate:** loại các bản ghi có `price <= 0` hoặc thiếu `category`.
3. **Transform:** chuẩn hóa `category` thành Title Case, tính
   `discounted_price = price * 0.9` và thêm timestamp `processed_at`.
4. **Load:** lưu dữ liệu đã xử lý vào `processed_data.csv`.

Pipeline cũng cung cấp thông tin observability trên terminal, gồm số bản ghi
được đọc, giữ lại, loại bỏ và lưu thành công. Dự án có thêm stress test để minh
họa ảnh hưởng của dữ liệu kém chất lượng đến câu trả lời của một AI agent đơn
giản.

## Yêu cầu

- Python 3.8 trở lên
- `pandas`
- `pytest` để chạy bộ kiểm thử

## Cài đặt

Tạo và kích hoạt môi trường ảo:

```bash
python -m venv venv
```

Trên Windows:

```powershell
.\venv\Scripts\activate
```

Trên macOS/Linux:

```bash
source venv/bin/activate
```

Cài đặt thư viện:

```bash
pip install pandas pytest
```

## Cách chạy

### Chạy ETL pipeline

Từ thư mục gốc của dự án, chạy:

```bash
python solution.py
```

Sau khi hoàn tất, pipeline tạo hoặc cập nhật file `processed_data.csv` và in
tóm tắt quá trình xử lý trên terminal.

### Chạy stress test Clean Data và Garbage Data

Tạo bộ dữ liệu có lỗi chất lượng:

```bash
python generate_garbage.py
```

Trong `agent_simulation.py`, đặt đường dẫn của Clean Data thành
`processed_data.csv`, sau đó chạy:

```bash
python agent_simulation.py
```

Stress test so sánh câu trả lời của agent khi dùng dữ liệu sạch với dữ liệu có
duplicate ID, sai kiểu dữ liệu, outlier và giá trị null. Kết quả và phân tích
được ghi trong `experiment_report.md`.

### Chạy kiểm thử tự động

```bash
python -m pytest tests/test_autograder.py -v
```

## Cấu trúc thư mục

```text
.
├── solution.py              # ETL pipeline chính
├── raw_data.json            # Dữ liệu sản phẩm đầu vào
├── processed_data.csv       # Dữ liệu sạch do pipeline tạo ra
├── generate_garbage.py      # Tạo dữ liệu lỗi cho stress test
├── agent_simulation.py      # Mô phỏng AI agent với Clean/Garbage Data
├── experiment_report.md     # Báo cáo và phân tích stress test
├── tests/
│   └── test_autograder.py   # Bộ kiểm thử tự động
└── README.md                # Tài liệu dự án
```

## Kết quả ETL

Với dữ liệu hiện tại trong `raw_data.json`:

| Chỉ số | Kết quả |
|---|---:|
| Bản ghi đầu vào | 5 |
| Bản ghi hợp lệ được giữ lại | 3 |
| Bản ghi không hợp lệ bị loại | 2 |
| Bản ghi được xuất ra CSV | 3 |

Hai bản ghi bị loại gồm:

- `id = 3`: giá bằng `-10`, vi phạm điều kiện `price > 0`.
- `id = 4`: trường `category` rỗng.

Dữ liệu đầu ra gồm các sản phẩm Laptop, Chair và Monitor. Category được chuẩn
hóa thành `Electronics` hoặc `Furniture`; giá sau giảm lần lượt là `1080.0`,
`40.5` và `270.0`. Mỗi bản ghi đều có trường `processed_at` để theo dõi thời
điểm pipeline xử lý dữ liệu.

## Data Observability

Các tín hiệu observability được triển khai:

- Log trạng thái bắt đầu và kết thúc pipeline.
- Log tổng số bản ghi extract thành công.
- Log số bản ghi hợp lệ và số bản ghi bị loại.
- Log ID cùng nguyên nhân của từng bản ghi bị loại.
- Gắn timestamp `processed_at` vào dữ liệu đầu ra.
- Log số bản ghi được load thành công và đường dẫn output.

## Kết luận

Pipeline bảo đảm các quy tắc chất lượng cơ bản trước khi dữ liệu được sử dụng
bởi hệ thống phía sau. Việc validation, logging và timestamp giúp phát hiện lỗi,
truy vết quá trình xử lý và giảm nguy cơ AI agent đưa ra câu trả lời sai do dữ
liệu không hợp lệ.
