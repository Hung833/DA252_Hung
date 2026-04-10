# DA252_Hung
# 📂 Kiến Trúc Dự Án (Hybrid / Offline-First Monorepo)

Dự án được chia làm 2 phần chính: **Frontend (Client Mobile App)** đóng vai trò là não bộ tính toán chính (hỗ trợ Offline), và **Backend (Server)** đóng vai trò lưu trữ đồng bộ và xử lý AI.

## 📱 A. Thư mục `client/` (Frontend React Native & Expo)

Giao diện ứng dụng Mobile tích hợp luôn bộ xử lý logic nghiệp vụ và cơ sở dữ liệu cục bộ (Local DB) để đảm bảo App có thể hoạt động 100% khi không có mạng. Quản lý trạng thái bằng Zustand. Hiện sử dụng Expo SDK 54.

### Cấu trúc thư mục:
- `src/components/`: Các UI components dùng chung (button, input form, popup...).
- `src/screens/`: Các màn hình chính (Login, Nhập liệu, Tính toán động cơ, Lịch sử...).
- `src/navigation/`: Cấu hình chuyển trang (Navigators).
- `src/store/`: Quản lý biến dữ liệu toàn cục (Zustand) - Lưu trữ input ($P, n, L$) và kết quả tính toán tạm thời.
- `src/logic/` (MỚI 🚀): Chứa toàn bộ công thức tính toán cơ khí cốt lõi (Động cơ, Bánh răng, Đai). Tách biệt hoàn toàn khỏi UI để dễ dàng tái sử dụng.
- `src/database/` (MỚI 🚀): Chứa Local DB (SQLite / file JSON) lưu trữ các Bảng tra cơ lý thuyết (danh mục động cơ, tiêu chuẩn ổ lăn). Giúp truy vấn tức thời không cần gọi API.
- `src/services/`: Cài đặt `api.ts` dùng Axios để gọi API đồng bộ lên Backend khi có mạng.

---

## 🖥️ B. Thư mục `server/` (Backend Node.js)

Xử lý đồng bộ dữ liệu (Sync), xác thực người dùng (Auth) và làm trạm trung chuyển kết nối AI. Áp dụng chuẩn MVC kết hợp design pattern Singleton.

### Cấu trúc thư mục:
- `config/`: Kết nối cơ sở dữ liệu trên mây MongoDB (`db.js` áp dụng Singleton).
- `controllers/`: Logic xử lý nghiệp vụ (Ví dụ: Đăng ký/Đăng nhập, Nhận cục data từ Mobile để lưu Lịch sử, Gọi API Chatbot Gemini).
- `models/`: Cấu trúc bảng lưu trữ dữ liệu (Database Schema: User, History).
- `routes/`: Đường dẫn API (Ví dụ: `api/v1/sync`, `api/v1/auth`, `api/v1/chatbot`).
- `middlewares/`: Bộ lọc kiểm tra quyền truy cập (Ví dụ: Check token JWT).
- `server.js`: Trái tim khởi chạy server.

---

## 🔄 2. Luồng Đồng Bộ Dữ Liệu (Data Flow) - ĐỌC KỸ TRƯỚC KHI CODE

Dự án áp dụng nguyên tắc **Local-Write, Cloud-Sync** (Lưu cục bộ trước, Đồng bộ mây sau):

### Khi Offline:
- User nhập liệu → `src/logic/` tính toán → Tra cứu linh kiện tại `src/database/` → Kết quả lưu tạm vào thiết bị với cờ `is_synced = false`.

### Khi Online:
- App phát hiện có mạng → Gọi `api/v1/sync` trong thư mục `services/` → Gửi toàn bộ các dự án `is_synced = false` lên Backend → Backend lưu vào MongoDB và trả về HTTP 200 → Client chuyển cờ thành `is_synced = true`.

---
## 🌳 Cây Thư Mục Tổng Thể (Directory Tree)

```
DADN/
│
├── docs/                           # 📚 TÀI LIỆU DỰ ÁN (Mọi người đọc trước khi code)
│   ├── 01_Architecture_Design.md   # Thiết kế kiến trúc Offline-First
│   ├── 02_API_Contract.md          # Thỏa thuận cấu trúc JSON giữa FE & BE
│   └── 03_Coding_Standard.md       # Quy tắc đặt tên biến (camelCase...)
│
├── server/                         # 🖥️ BACKEND (Node.js + MongoDB)
│   ├── config/                     # File kết nối Database (db.js)
│   ├── controllers/                # Xử lý logic API (Lưu lịch sử, Auth, AI Chatbot)
│   ├── middlewares/                # Bộ lọc an ninh (Check Token, Error Handler)
│   ├── models/                     # Schema Database trên mây (User, History)
│   ├── routes/                     # Định tuyến API (auth.route, sync.route...)
│   ├── .env                        # Biến môi trường (KHÔNG PUSH LÊN GITHUB)
│   ├── .gitignore                  # Bỏ qua node_modules, .env
│   ├── package.json
│   └── server.js                   # Điểm khởi chạy Server
│
└── client/                         # 📱 FRONTEND (React Native/Expo) - Trái tim hệ thống
    ├── assets/                     # Hình ảnh, Fonts, Icon
    ├── src/
    │   ├── components/             # UI Reusable (Nút bấm, Form, Dialog...)
    │   ├── database/               # 📦 LỚP DATA: Chứa SQLite/JSON (Bảng tra ổ lăn, động cơ...)
    │   ├── logic/                  # 🧠 LỚP NÃO: Các hàm toán học tính toán cơ khí (Offline)
    │   ├── navigation/             # Điều hướng chuyển màn hình (React Navigation)
    │   ├── screens/                # 🎨 LỚP UI: Giao diện màn hình (NhapLieu, DongCo, BanhRang)
    │   ├── services/               # Cấu hình gọi API đồng bộ lên Backend (Axios/api.ts)
    │   └── store/                  # 🔄 LỚP STATE: Quản lý biến toàn cục P, n, L (Zustand)
    │
    ├── App.js                      # Điểm khởi chạy App Mobile
    ├── app.json                    # Cấu hình Expo
    ├── package.json
    └── .gitignore                  # Bỏ qua node_modules, build files
```

