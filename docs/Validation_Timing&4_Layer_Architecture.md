# 1. Chiến lược kiểm tra số liệu hai giai đoạn (Validation Timing)

Về cơ chế kiểm tra tính hợp lệ của dữ liệu đầu vào, nhóm thống nhất từ bỏ phương pháp kiểm tra thụ động để chuyển sang **Chiến lược kiểm tra hai giai đoạn (2-Stage Validation)** nhằm tối ưu hóa trải nghiệm người dùng:

## Giai đoạn 1: Kiểm tra tức thời (Inline Validation)
- Diễn ra ngay tại thời điểm người dùng đang gõ phím.
- Lớp giao diện sẽ bắt các sự kiện thay đổi dữ liệu để kiểm tra ngay các quy tắc cơ bản:
  - Không được để trống
  - Sai định dạng kiểu chữ
  - Nhập số âm
- Nếu vi phạm, ô nhập liệu lập tức hiện viền đỏ kèm dòng cảnh báo bên dưới mà không cần chờ người dùng nhấn nút.

## Giai đoạn 2: Kiểm tra tính khả thi vật lý (On-Submit Validation)
- Diễn ra khi người dùng nhấn nút **"Kiểm tra & Tính toán"**.
- Lớp giao diện sẽ đóng gói toàn bộ gói dữ liệu đã "sạch" định dạng, truyền xuống lớp logic.
- Bộ kiểm tra sẽ truy vấn cơ sở dữ liệu nội bộ (Local DB) để đối chiếu sự kết hợp của các thông số (ví dụ: công suất P và số vòng quay n) với giới hạn tiêu chuẩn cơ khí.
- Tùy thuộc vào mã lỗi trả về (VD: vượt ngưỡng tỷ số truyền), ứng dụng sẽ bật hộp thoại (Popup) đề xuất giải pháp kỹ thuật thay thế.
- Nếu dữ liệu hoàn toàn khả thi, hệ thống sẽ tự động luân chuyển sang module tính toán động cơ điện.

---

# 2. Triển khai Kiến trúc 4 Lớp (4-Layer Architecture) cho Module 1

Nhóm tuân thủ chặt chẽ mô hình bốn lớp để đảm bảo tính độc lập và dễ bảo trì:

### **Lớp 1 - Giao diện (Presentation Layer)**
- Đặt tại: `src/screens/Screen_NhapLieu`
- Chức năng:
  - Hoàn toàn "mù" về cơ lý thuyết
  - Hiển thị biểu mẫu
  - Thực hiện Inline Validation (Giai đoạn 1)
  - Bắt sự kiện người dùng

### **Lớp 2 - Trạm trung chuyển (State Layer)**
- Đặt tại: `src/store/projectState.js`
- Sử dụng thư viện Zustand để tạo một không gian nhớ tạm (RAM)
- Liên tục hứng và lưu trữ các biến đầu vào (P, n, L) để cung cấp dữ liệu xuyên suốt cho các màn hình khác

### **Lớp 3 - Não bộ tính toán (Business Logic Layer)**
- Đặt tại: `src/logic/validation.js`
- Độc lập hoàn toàn với UI
- Nhận dữ liệu từ Zustand để chạy các thuật toán xác thực tính khả thi vật lý (Giai đoạn 2) và tính toán công thức cơ khí

### **Lớp 4 - Dữ liệu cục bộ (Local Data Layer)**
- Đặt tại: `src/database/sqlite_local.js`
- Áp dụng Design Pattern Singleton để nạp các bảng tra cơ lý thuyết vào RAM duy nhất một lần
- Lớp logic (Lớp 3) sẽ truy vấn vào đây để lấy thông số linh kiện với tốc độ phản hồi dưới 1ms, hiện thực hóa hoàn toàn mục tiêu tính toán Offline

