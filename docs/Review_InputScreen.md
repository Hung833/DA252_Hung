# 📝 ĐÁNH GIÁ TẬP TIN `InputScreen.tsx` DỰA TRÊN QUY CHUẨN KIẾN TRÚC HYBRID VÀ USE-CASE

## 1. Tổng quan (Overview)
Tập tin `InputScreen.tsx` hiện tại đã triển khai được một phần giao diện cơ bản của màn hình nhập liệu. Tuy nhiên, khi đối chiếu với các tài liệu quy chuẩn kiến trúc (4-Layer, Hybrid Offline-First) và Đặc tả Use-case, mã nguồn này đang **vi phạm khá nhiều quy tắc cốt lõi** về phân tách trách nhiệm (Separation of Concerns), xử lý trạng thái (State) và luồng kiểm tra dữ liệu (Validation).

---

## 2. Các điểm vi phạm Kiến trúc & Quy chuẩn (Violations)

### 2.1. Vi phạm Kiến trúc 4 Lớp (4-Layer Architecture)
- **Tình trạng hiện tại:** UI (Giao diện) mang quá nhiều logic. Hàm `validateAndCalculate` hiện tại trong file đang chứa các quy tắc logic kỹ thuật cơ khí, ví dụ như kiểm tra vật lý `power <= 0 || power > 500`.
- **Luật bị vi phạm:** *"Lớp 1 - Giao diện: Hoàn toàn mù về cơ lý thuyết"* (Theo `Validation_Timing&4_Layer_Architecture.md` và `03_Coding_Standard.md`).
- **Hậu quả:** Khó bảo trì, không tái sử dụng được logic kiểm tra nếu sau này làm ứng dụng Web, mã nguồn UI quá cồng kềnh.

### 2.2. Vi phạm Chiến lược Kiểm tra 2 Giai đoạn (Validation Timing)
- **Tình trạng hiện tại:** Mọi thao tác kiểm tra (nhập rỗng, sai định dạng, hay sai ngưỡng vật lý) đều dồn vào lúc bấm nút **"Kiểm tra & Tính toán"** (`validateAndCalculate`). 
- **Luật bị vi phạm:** 
  - **Giai đoạn 1 (Inline Validation):** Cần phải kiểm tra ngay lúc người dùng đang gõ phím đối với các lỗi như: bỏ trống, sai kiểu dữ liệu chữ/số (Hiển thị viền đỏ và text cảnh báo ngay dưới ô input).
  - **Giai đoạn 2 (On-Submit Validation):** Nút bấm mới chạy logic vật lý ở tầng Logic (Lớp 3) và truy xuất database, sau đó mới bung Popup đề xuất lỗi nếu vượt ngưỡng.

### 2.3. Vi phạm Quản lý Trạng thái (Single Source of Truth)
- **Tình trạng hiện tại:** Màn hình sử dụng `useState` cục bộ quá nhiều (`operatingData`, `loadType`, `workShifts`, v.v.). Form sau khi điền không thấy đẩy dữ liệu vào Global State (Zustand).
- **Luật bị vi phạm:** *"Dữ liệu sau khi user nhập từ Screen A sẽ được đẩy thẳng vào một Kho chung (Zustand)"* (Theo `03_Coding_Standard.md`), không khai báo để chết các state tại file UI.

### 2.4. Vi phạm Đặc tả Use-case (Form Inputs)
So chiếu với Đặc tả "Nhập dữ liệu thông số đầu vào", màn hình hiện tại đang thiếu sót và sai lệch nhiều trường thông tin:
- **Sai dữ liệu Dropdown `Đặc tính tải`:** Code đang để `["Tải êm", "Tải va đập nhẹ", "Tải va đập vừa", "Tải va đập nặng"]`.
  👉 *Đặc tả yêu cầu:* `["Tải tĩnh", "Tải va đập nhẹ", "Tải va đập mạnh"]`.
- **Sai kiểu dữ liệu `Số ca làm việc`:** Code đang dùng Dropdown (`workShiftsXOptions`). 
  👉 *Đặc tả yêu cầu:* Dùng **Ô nhập số (Input Number)**.
- **Thiếu các trường thông tin theo Use-case:**
  - **Chiều quay:** Dropdown (Quay 1 chiều / Quay 2 chiều).
  - **Số ngày làm việc trong năm:** Input Number (Ô nhập số).
  - **Số giờ làm việc mỗi ca:** Input Number (Ô nhập số).
  - **Các ràng buộc thiết kế (Hãng ổ lăn):** Dropdown (SKF / NTN) - Đây là thông số tùy chọn.
- *(Lưu ý: Các dữ liệu ngoại vi khác đã có sẵn nhưng không có trong đặc tả như `efficiencyData`, `driveItems`, `bearingItems` thì **giữ nguyên không xóa** theo yêu cầu).*

---

## 3. Định hướng khắc phục & Cấu trúc lại (Refactoring Plan)

Để gỡ lỗi và đưa `InputScreen.tsx` về chuẩn kiến trúc Hybrid, bạn cần thực hiện các bước refactor sau:

### Bước 1: Chuẩn hóa Form Input (Lớp 1 - Presentation)
- Cập nhật lại các mảng Dropdown và thêm các Component `TextInput` mới (Chiều quay, Số ngày, Số giờ/ca, Hãng ổ lăn).
- Bắt sự kiện `onChangeText` để thực hiện **Inline Validation (Giai đoạn 1)**. Nếu người dùng nhập sai kiểu ký tự hoặc để trống các biến lõi như $P, n, L$, form phải kích hoạt viền UI màu đỏ ngay lập tức mà không chờ bấm submit.

### Bước 2: Đồng bộ lên Zustand (Lớp 2 - State)
- Chuyển logic lưu trạng thái từ các `useState` liên quan tới số liệu sang `src/store/projectState.ts` (Zustand/Redux).
- Lớp UI tạo ra một đối tượng form "sạch" và ném dữ liệu vào store: `setP(power), setN(speed), setL(serviceLife)`.

### Bước 3: Tách Logic Vật lý đi nơi khác (Lớp 3 - Logic)
- Xóa bỏ tất cả các dòng check vật lý như `power <= 0 || power > 500` ra khỏi file UI.
- Viết một hàm `validatePhysicalConstraints(data)` trong `src/logic/validation.ts`. Hàm này sẽ lấy data từ Zustand, thực hiện tính toán.

### Bước 4: Chuẩn hóa nút "Kiểm tra & Tính toán" (Giai đoạn 2)
Sau khi bấm Submit, màn hình chỉ đóng vai trò phân luồng như sau:

```typescript
const handleCalculate = async () => {
  // Giai đoạn 2: Gọi bộ não tính toán (từ logic layer đã tách lớp)
  const validationResult = await validatePhysicalConstraints(storeData); 
  
  if (!validationResult.isValid) {
    // Chỉ khi logic báo lỗi mới bung modal
    setShowErrorModal(true);
    setErrorMessage(validationResult.message);
    setErrorSuggestion(validationResult.suggestion);
  } else {
    // Đúng thì sang màn hình kế tiếp
    navigation.navigate("MotorSelectionScreen");
  }
}
```

---

**🔥 KẾT LUẬN:**
File `InputScreen.tsx` hiện tại bị vấn đề "Ôm đồm" (Spaghetti Code) do gánh cả công việc vẽ UI (Lớp 1), lưu dữ liệu tạm (Lớp 2) và kiểm tra toán học (Lớp 3). Bạn nhớ tách triệt để logic ra, và điều chỉnh UI form lại sao cho đủ bộ thông số đầu vào như mô tả Use-case.