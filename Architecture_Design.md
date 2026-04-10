# Kiến trúc được chia làm 4 lớp

## LỚP 1: UI / PRESENTATION LAYER (Giao diện hiển thị)

### Nhiệm vụ
Chỉ chứa các nút bấm, ô nhập liệu, biểu đồ. **Tuyệt đối KHÔNG chứa công thức tính toán.**

### Cấu trúc
- **Screen_NhapLieu**: Giao diện cho Module 1.
- **Screen_TinhDongCo**: Giao diện cho Module 2.
- **Screen_TinhBanhRang**: Giao diện cho Module 3.
- **Screen_Dashboard**: Màn hình vẽ biểu đồ tổng kết và nút xuất PDF.

---

## LỚP 2: STATE MANAGEMENT LAYER (Trạm trung chuyển dữ liệu)

### Nhiệm vụ
Lưu trữ dữ liệu tạm thời (Input của người dùng và Output của các công thức) trong lúc App đang mở. Đây chính là cái "file khác" mà em nhắc tới.

### Cấu trúc (Dùng Zustand hoặc Redux)
- `store/projectState.js`: Chứa biến `input_data` (P, n, L) và `calculated_results` (Động cơ, Đai, Bánh răng). Mọi màn hình ở Lớp 1 đều gửi và lấy dữ liệu từ trạm này.

---

## LỚP 3: BUSINESS LOGIC LAYER (Bộ não tính toán)

### Nhiệm vụ
Chứa các file toán học, công thức cơ lý thuyết. Nhận số từ Lớp 2, tính toán, và trả kết quả lại cho Lớp 2.

### Cấu trúc
- `utils/validation.js`: Hàm kiểm tra P, n, L có hợp lệ không.
- `utils/calc_motor.js`: Chứa công thức tính công suất cần thiết, phân phối tỉ số truyền.
- `utils/calc_gear.js`: Chứa công thức tính module, số răng bánh răng.

---

## LỚP 4: DATA & NETWORK LAYER (Lưu trữ và Đồng bộ)

### Nhiệm vụ
Quản lý Database offline trên máy và gọi API lên Server.

### Cấu trúc (Frontend)
- `database/sqlite_local.js`: Kết nối database bảng tra cơ khí trên máy tính/điện thoại.
- `services/api_sync.js`: Hàm quét các dự án đã xong để gửi lên Server.

### Cấu trúc (Backend - Server)
- `NodeJS_Server/routes`: Hứng API từ FE.
- `NodeJS_Server/controllers`: Xử lý lưu lịch sử vào MongoDB/PostgreSQL, kết nối Chatbot Gemini.

> *Với cấu trúc này, sau này anh em muốn rõ rộng ra làm Web, anh em chỉ cần giữ nguyên Lớp 2, Lớp 3, Lớp 4. Và chỉ việc code lại Lớp 1 thôi!*
