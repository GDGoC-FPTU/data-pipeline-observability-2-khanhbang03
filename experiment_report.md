# Experiment Report: Data Quality Impact on AI Agent

**Student ID:** AI20K-2A202600693  
**Name:** Nguyễn Khánh Bằng  
**Date:** 2026-06-10

---

## 1. Kết quả thí nghiệm

Chạy `agent_simulation.py` với cùng câu hỏi:
`What is the best electronic product?`

| Scenario | Agent Response | Accuracy (1-10) | Notes |
|----------|----------------|----------------:|-------|
| Clean Data (`processed_data.csv`) | `Agent: Based on my data, the best choice is Laptop at $1200.` | 10 | Agent chọn đúng sản phẩm điện tử có giá cao nhất trong dữ liệu sạch. |
| Garbage Data (`garbage_data.csv`) | `Agent: Based on my data, the best choice is Nuclear Reactor at $999999.` | 1 | Outlier không hợp lý được gắn category `electronics`, khiến Agent chọn kết quả sai và không phù hợp với ngữ cảnh sản phẩm. |

---

## 2. Phan tich va nhan xet

### Tai sao Agent tra loi sai khi dung Garbage Data?

Agent sử dụng quy tắc đơn giản: lọc các bản ghi có category là `electronics`,
sau đó chọn sản phẩm có `price` lớn nhất. Trong Garbage Data, bản ghi
`Nuclear Reactor` có giá `999999` và được gắn category `electronics`. Vì không
có bước validation hoặc phát hiện outlier, Agent coi đây là dữ liệu hợp lệ và
chọn nó thay vì Laptop. Kết quả này đúng theo thuật toán nhưng sai về mặt ý
nghĩa và không hữu ích cho người dùng.

Ngoài outlier gây ra lỗi trực tiếp, Garbage Data còn chứa nhiều vấn đề khác.
Duplicate ID làm hai sản phẩm khác nhau cùng mang `id = 1`, khiến việc truy vết,
cập nhật hoặc liên kết dữ liệu trở nên không đáng tin cậy. Giá trị
`ten dollars` sai kiểu dữ liệu có thể làm lỗi các phép so sánh, sắp xếp hoặc
tính toán giá. Các giá trị null ở ID và category làm mất thông tin nhận dạng,
đồng thời có thể khiến bước lọc dữ liệu bỏ sót hoặc phát sinh lỗi. Những vấn đề
này cho thấy Agent không thể tự sửa dữ liệu đầu vào chỉ bằng một prompt tốt.

### So sánh hai kịch bản

Với Clean Data, ETL pipeline đã loại bản ghi không hợp lệ, chuẩn hóa category
và giữ lại các sản phẩm có giá hợp lý. Vì vậy, Agent có thể áp dụng quy tắc chọn
sản phẩm đắt nhất và trả lời đúng là Laptop. Với Garbage Data, Agent không có
cơ chế kiểm tra chất lượng nên tin tưởng hoàn toàn vào dữ liệu rác và tạo ra
câu trả lời sai. Chất lượng đầu vào vì thế ảnh hưởng trực tiếp đến độ chính xác
và độ tin cậy của kết quả.

---

## 3. Kết luận

**Quality Data > Quality Prompt? Đồng ý.**

Prompt tốt có thể hướng dẫn Agent cách trả lời, nhưng không thể đảm bảo kết quả
đúng nếu nguồn dữ liệu chứa outlier, sai kiểu, duplicate hoặc null. Trước khi
dữ liệu được cung cấp cho Agent, cần thực hiện validation, chuẩn hóa, phát hiện
outlier và theo dõi chất lượng bằng logging. Dữ liệu sạch tạo nền tảng để Agent
đưa ra câu trả lời chính xác, phù hợp và đáng tin cậy.
