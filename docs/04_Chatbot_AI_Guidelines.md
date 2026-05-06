# 🤖 TÀI LIỆU ONBOARDING: TÍCH HỢP AI CHATBOT (RAG + GEMINI)
**Dự án:** Ứng dụng Thiết kế Hệ dẫn động thùng trộn

**Mục tiêu:** Xây dựng một trợ lý AI "chuẩn kỹ sư" có khả năng tư vấn thiết kế cơ khí, bắt lỗi số liệu vật lý vô lý và giải thích chuyên sâu dựa trên tài liệu môn học (Sử dụng công nghệ RAG).

---

## 🏗️ 1. TỔNG QUAN LUỒNG LÀM VIỆC (WORKFLOW)
Chúng ta sẽ áp dụng chiến lược MVP (Sản phẩm khả thi tối thiểu). Chatbot hoạt động theo mô hình Hỏi - Đáp thuần túy, không can thiệp tự động điền form để đảm bảo tính ổn định của hệ thống lõi.

**Công nghệ sử dụng (Tech Stack):**
* **LLM (Bộ não):** Gemini 1.5 Flash API (Miễn phí, tốc độ cao).
* **Vector Database:** MongoDB Atlas Vector Search (Tích hợp sẵn trong DB hiện tại của nhóm).
* **Framework Backend:** `LangChain.js` (Thư viện số 1 hiện nay để làm RAG trên Node.js).

---

## 📂 2. BẢN ĐỒ THƯ MỤC AI (AI FOLDER MAP)
Để tích hợp AI, chúng ta sẽ tạo thêm một số file mới vào cấu trúc hiện tại. Anh em tuân thủ đúng đường dẫn sau:
```text
DADN-Monorepo/
│
├── server/                         # 🖥️ BACKEND (Xử lý AI)
│   ├── data/
│   │   └── mechanical_knowledge.json <-- (MỚI) Chứa file Text/JSON dữ liệu các bảng tra cơ khí để dạy AI.
│   ├── scripts/
│   │   └── ingest_data.js            <-- (MỚI) File chạy 1 lần duy nhất để băm file JSON trên thành Vector lưu vào MongoDB.
│   ├── controllers/
│   │   └── chatController.js         <-- (MỚI) Hứng câu hỏi, gọi LangChain tìm tài liệu, ném cho Gemini và trả kết quả.
│   └── routes/
│       └── chatRoutes.js             <-- (MỚI) Tạo API endpoint: POST /api/v1/chat
│
└── client/                         # 📱 FRONTEND (Giao diện Chat)
    └── src/
        ├── services/
        │   └── chatApi.ts            <-- (MỚI) Hàm gọi API gửi câu hỏi lên Backend.
        └── screens/
            └── ChatbotScreen.jsx     <-- (MỚI) Giao diện nhắn tin giống Messenger/Zalo.
