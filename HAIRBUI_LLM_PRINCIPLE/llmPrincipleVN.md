# Nguyên lý hoạt động của các mô hình ngôn ngữ lớn (LLMs)

Các mô hình ngôn ngữ lớn (LLMs) là một loại trí tuệ nhân tạo được thiết kế để hiểu và tạo ra văn bản giống con người. Đây là một giải thích chi tiết về cách chúng hoạt động:

## Bước 1: Xử lý đầu vào

### Phân đoạn từ

- **Mục đích**: Chuyển đổi văn bản thành các phần có thể quản lý được. Điều này giúp mô hình xử lý từ vựng đa dạng một cách hiệu quả.
- **Quy trình**:
		- Tách câu thành các token, có thể là từ hoặc các phần của từ.
		- Sử dụng các kỹ thuật như Mã hóa cặp byte (BPE) để xử lý các từ không xác định bằng cách chia chúng thành các đơn vị phụ đã biết.

### Nhúng

- **Mục đích**: Chuyển đổi các token thành các vector số mà mô hình có thể xử lý.
- **Quy trình**:
		- Mỗi token được ánh xạ thành một vector đa chiều.
		- Các vector này nắm bắt các mối quan hệ ngữ nghĩa giữa các từ.

## Bước 2: Kiến trúc mô hình

### Các lớp Transformer

#### Cơ chế tự chú ý

- Tính toán điểm chú ý để tập trung vào các phần khác nhau của chuỗi đầu vào.
- Cho phép mô hình đánh giá tầm quan trọng của mỗi token so với các token khác.
- Sử dụng truy vấn, khóa và giá trị để tính toán các biểu diễn có trọng số.

#### Mạng truyền xuôi

- Áp dụng các biến đổi phi tuyến tính cho đầu ra của cơ chế chú ý.
- Bao gồm các lớp tuyến tính và hàm kích hoạt như ReLU.

#### Chuẩn hóa lớp

- Ổn định và tăng tốc quá trình đào tạo bằng cách chuẩn hóa đầu ra trong mỗi lớp.

## Bước 3: Đào tạo

### Tiền đào tạo

- **Mục tiêu**: Học hiểu ngôn ngữ tổng quát
- **Nhiệm vụ**:
		- Mô hình hóa ngôn ngữ: Dự đoán từ tiếp theo trong một chuỗi
		- Mô hình hóa ngôn ngữ có che giấu: Dự đoán các từ bị thiếu trong một câu.
- **Dữ liệu**: Đào tạo trên các kho ngữ liệu khổng lồ từ sách, bài báo, trang web, v.v.

### Tinh chỉnh

- **Mục tiêu**: Điều chỉnh mô hình cho các nhiệm vụ cụ thể.
- **Quy trình**: Sử dụng các bộ dữ liệu nhỏ hơn tập trung vào các nhiệm vụ như phân tích cảm xúc hoặc dịch thuật.
- **Lợi ích**: Cải thiện hiệu suất trên các ứng dụng chuyên biệt.

## Bước 4: Suy luận

### Hiểu ngữ cảnh

- Mô hình sử dụng các mẫu đã học để diễn giải đầu vào và dự đoán đầu ra.
- Duy trì ngữ cảnh trong các văn bản dài, cho phép tạo ra các phản hồi mạch lạc.

### Tạo từng token

- Dự đoán token tiếp theo dựa trên ngữ cảnh hiện tại.
- Tiếp tục cho đến khi đạt được tiêu chí dừng (ví dụ: kết thúc câu).

## Bước 5: Đầu ra

### Giải mã

- Chuyển đổi các vector token dự đoán trở lại thành từ hoặc cụm từ.
- Sử dụng các kỹ thuật như tìm kiếm chùm hoặc lấy mẫu để tinh chỉnh chất lượng đầu ra.

### Xử lý hậu kỳ

- Làm sạch và định dạng văn bản để đảm bảo tính mạch lạc và dễ đọc.
- Có thể bao gồm sửa lỗi ngữ pháp và điều chỉnh dấu câu.
