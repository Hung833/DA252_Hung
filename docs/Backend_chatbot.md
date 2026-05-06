# 🛠️ HƯỚNG DẪN TRIỂN KHAI CODE AI CHATBOT (RAG + GEMINI)
**Dự án:** Ứng dụng Thiết kế Hệ dẫn động thùng trộn
**Mô hình:** RAG (Retrieval-Augmented Generation) kết hợp MongoDB Atlas Vector Search.

Tài liệu này hướng dẫn chi tiết các bước code cho cả Frontend và Backend để tích hợp Trợ lý AI tư vấn cơ khí.

---

## 🖥️ PHẦN 1: TRIỂN KHAI BACKEND (NODE.JS)

### 1. Nạp kiến thức cho AI (Data Ingestion)
AI mặc định không có dữ liệu bảng tra của môn Chi tiết máy. Chúng ta cần "dạy" nó bằng cách băm nhỏ dữ liệu và lưu thành dạng Vector.

* **File:** `server/scripts/ingest_data.js`
* **Nhiệm vụ:** Đọc file JSON/CSV chứa thông số động cơ, dùng `LangChain.js` chuyển thành Vector và lưu lên MongoDB.
* **Lưu ý:** Script này **chỉ chạy 1 lần duy nhất** (bằng lệnh `node scripts/ingest_data.js`) khi set up dự án hoặc khi có file dữ liệu cơ khí mới.

### 2. Xây dựng Trái tim của AI (RAG Controller)
Đây là nơi xử lý logic tìm kiếm tài liệu và giao tiếp với Gemini.

* **File:** `server/controllers/chatController.js`
* **Nhiệm vụ:**
  1. Nhận câu hỏi từ Frontend.
  2. Dùng LangChain tìm 3 đoạn tài liệu cơ khí trong MongoDB khớp với câu hỏi nhất.
  3. Ghép tài liệu + Câu hỏi + System Prompt để gửi cho Gemini.
  4. Trả câu trả lời dạng Text về cho Frontend.

**📌 QUAN TRỌNG: System Prompt (Chỉ thị hệ thống)**
Bắt buộc phải truyền đoạn text này vào cấu hình của LangChain/Gemini để AI biết cách bắt lỗi vật lý mà không cần viết lệnh `if/else`:

> "Bạn là một Chuyên gia Cơ khí tại Đại học Bách Khoa. Nhiệm vụ của bạn là tư vấn thiết kế hệ dẫn động dựa trên tài liệu (Context) được cung cấp.
> **Luật bắt buộc:**
> 1. Nếu người dùng cung cấp Công suất (P) hoặc Tốc độ (n) <= 0, hãy từ chối tư vấn và giải thích rõ rằng số liệu vật lý không khả thi.
> 2. Chỉ đề xuất tối đa 2 mã động cơ phù hợp nhất và giải thích lý do ngắn gọn.
> 3. Tuyệt đối KHÔNG tự bịa ra thông số nếu không có trong tài liệu."

### 3. Mở cổng kết nối (API Route)
* **File:** `server/routes/chatRoutes.js`
* **Nhiệm vụ:** Tạo endpoint để Frontend gọi lên.
```javascript
const express = require('express');
const router = express.Router();
const { handleChat } = require('../controllers/chatController');

// Endpoint: POST /api/v1/chat
router.post('/', handleChat);

module.exports = router;
