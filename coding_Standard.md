# TÀI LIỆU QUY ƯỚC LẬP TRÌNH (CODING GUIDELINES & API CONTRACT)
**Dự án:** Ứng dụng Thiết kế Hệ dẫn động thùng trộn (Đồ án Đa ngành)

---

## 1. Style Guide & Quy ước đặt tên (Naming Conventions)
Quy tắc này áp dụng chung cho cả code Frontend và Backend để đảm bảo tính đồng nhất, tránh xung đột (collision) khi merge các module. Khuyến khích sử dụng tiếng Anh cho các biến số học để chuẩn hóa theo ngành.

| Thành phần | Quy tắc | Ví dụ không hợp lệ (❌) | Ví dụ hợp lệ (✅) |
| :--- | :--- | :--- | :--- |
| **Biến (Variables)** | `camelCase`<br>*(Tên có nghĩa, không viết tắt đánh đố)* | `congsuat`, `P`, `checkData` | `requiredPower`, `congSuatYeuCau`, `powerP` |
| **Hàm (Functions)** | `camelCase`<br>*(Bắt đầu bằng một động từ)* | `tinh_toan()`, `Data()` | `calculateMotorPower()`, `validateInput()` |
| **Lớp (Classes) & Model** | `PascalCase`<br>*(Viết hoa chữ cái đầu của mọi từ)* | `dongCoModel`, `gear_box` | `ElectricMotor`, `GearBoxController` |
| **Hằng số (Constants)** | `UPPER_SNAKE_CASE`<br>*(Dùng cho giá trị vật lý/hệ số cố định)* | `pi`, `Gravity`, `max_speed` | `PI_VALUE`, `MAX_ROTATION_SPEED` |

---

## 2. Nguyên tắc Quản lý Dữ liệu (State Management)
Để đảm bảo tính toàn vẹn của dữ liệu trong hệ thống, mọi thành viên cần tuân thủ nghiêm ngặt nguyên tắc **Single Source of Truth (Nguồn dữ liệu duy nhất)**:

* **Tách biệt Logic và UI:** Toàn bộ UI (giao diện) tuyệt đối KHÔNG chứa code xử lý logic tính toán.
* **Luồng dữ liệu (Data Flow):** Dữ liệu sau khi nhập từ Form sẽ được đẩy thẳng vào một kho chung (Global State). 
* **Quyền truy xuất:** Các module tính toán (Động cơ, Bánh răng...) chỉ được phép "đọc" (read) dữ liệu từ kho chung này để xử lý. Tuyệt đối không làm thay đổi hoặc ghi đè trực tiếp lên Input gốc.

---

## 3. Phân chia Kiến trúc Hệ thống (Architecture)
Hệ thống được chia tách rõ ràng vai trò giữa Frontend và Backend (Backend được thiết kế để sử dụng chung cho cả App và Web).

### Frontend (App/Web)
* Phụ trách hiển thị giao diện (UI) và quản lý State.
* Chịu trách nhiệm chạy các logic tính toán cơ khí (hoạt động offline).
* Thực hiện tra cứu bảng thông số linh kiện thông qua cơ sở dữ liệu SQLite (local).

### Backend (Server)
* Quản lý lưu trữ lịch sử và đồng bộ dữ liệu (Sync API).
* Xử lý xác thực người dùng (Authentication).
* Cung cấp kết nối và xử lý logic với Chatbot AI.

---

## 4. Quy trình Triển khai (Workflow)
1. **Đọc tài liệu:** Chi tiết API Contract và cấu trúc thư mục đã được định nghĩa trong folder `docs/`. Yêu cầu tất cả thành viên đọc kỹ trước khi code.
2. **Triển khai:** Quá trình code đồng loạt chỉ chính thức bắt đầu **sau khi** team chốt xong toàn bộ cấu trúc JSON payload.