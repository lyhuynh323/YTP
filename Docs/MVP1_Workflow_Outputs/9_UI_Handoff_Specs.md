# Hướng dẫn Thiết kế UI & Bàn giao Giao diện (UI Mockups & Developer Handoff) - MVP V1

Tài liệu này do **UI Designer** phụ trách, bao gồm các nguyên tắc thiết kế và mô tả giao diện (Wireframes/Mockups) dành cho Frontend Developer. Giao diện được thiết kế tuân thủ các nguyên tắc trải nghiệm người dùng (UX) hiện đại: "Premium Dark Mode", "Glassmorphism", và sự tiện lợi tuyệt đối (Minimal & Accessible) cho nền tảng Mobile App và Web Admin.

---

## 1. Hệ thống Ngôn ngữ Thiết kế (Design System)

### 1.1 Khúc mắc về Màu sắc (Color Palette)
- **Background (Nền chính):** `#0A0A0A` (Đen tuyền - Tiết kiệm pin OLED) hoặc `#121212` (Đen xám cho Web Admin).
- **Surface (Khối nổi bật):** `#1E1E1E` (Xám tối).
- **Primary Color (Nổi bật / Hành động chính):** Gradient tuân theo phổ Cyber/Tech (Blue-Cyan-Purple). Ví dụ: `linear-gradient(90deg, #00C6FF 0%, #0072FF 100%)`.
- **Status (Trạng thái):** 
  - *Thành công (Passed/Completed):* `#00E676` (Xanh lá sáng).
  - *Báo lỗi/Cảnh báo (Failed/Cancel):* `#FF5252` (Đỏ mờ).
  - *Đang tiến hành (Processing/Uploading):* `#FFA000` (Cam hổ phách).

### 1.2 Font chữ (Typography)
- Giao diện Admin (Data-heavy): Sử dụng `Inter` hoặc `Roboto` - Rõ ràng, dễ đọc khi hiển thị danh sách dài.
- Giao diện Mobile App (Trendy/Creator): Sử dụng `Outfit` hoặc `Poppins` cho các Heading/Title. `Inter` cho text hướng dẫn (Instruction Text).

### 1.3 Hiệu ứng Glassmorphism (Kính mờ)
- Thẻ Card, Nút bấm (Button nổi) không dùng Solid Color mà dùng Backgound trong suốt: 
  - `background: rgba(255, 255, 255, 0.05);`
  - `backdrop-filter: blur(10px);`
  - `border: 1px solid rgba(255, 255, 255, 0.1);`

---

## 2. Đặc tả Cấu thành Giao diện Mobile App (End-User)

### 2.1 Màn hình Đăng nhập (Login View)
- **Mô tả:** Đơn giản, tinh tế, tập trung trực tiếp vào Form Đăng nhập.
- **Component:** 
  - Brand Logo (Center Top).
  - 2 x Input Text (Email, Password) bo góc tròn `12px`, nền Glassmorphism.
  - 1 x Nút Login (Primary Gradient) bo góc tròn `24px` hoặc `pill-shape`.
- **UX:** Khi bấm Input, bàn phím hiện lên không che khuất Form.

### 2.2 Màn hình Dashboard (Trang Chủ / Kịch Bản)
- **Mô tả:** Màn hình quản trị 5 kịch bản sau khi đã tự động tạo.
- **Component:**
  - **Header:** "Xin chào, Ly Huynh!" + Avatar nhỏ + Dropdown chọn Location (hiệu ứng Bottom Sheet rất mượt thả xuống từ màn hình dưới thay vì popup khô cứng ở giữa).
  - **Hero Action:** Nút "Làm mới Kịch Bản (Sync with Trending)" với icon Refresh quay mượt mà.
  - **List Container:** Danh sách 5 thẻ (Card). 
  - **Card Item:** 
    - Nhãn (Tag) trạng thái `New` (Màu xanh nước biển), `Processing` (Cam).
    - Tiêu đề béo (Bold). 
    - Nút "Reshuffle" (Biểu tượng xúc xắc nhỏ) cho từng thẻ `New` nằm ở góc phải trên cùng.

### 2.3 Màn hình Guided Camera (Cốt lõi)
- **Mô tả:** Viewfinder chiến gần 100% diện tích màn hình, không bị che bởi thanh công cụ đen mờ. Các nút là Float.
- **Component:**
  - **Thanh Progress Bar (Top):** Thanh mỏng (2px) vuốt dọc mép trên cùng thể hiện màu gradient. (Ví dụ: Đã đi được `3/5 Step`).
  - **Instruction Overlay (Top-center):** Bảng Glassmorphism chứa text hướng dẫn to, dễ đọc. (Ví dụ: "Hãy cầm sản phẩm nghiêng 45 độ và xoay từ trái sang phải 30s").
  - **Control Bar (Bottom):** 
    - 🔴 Nút Record siêu to đùng (Red Circle) ở giữa. 
    - ⏹ Khi đang Record, nút Record biến thành nút Vuông (Stop). Bên phải xuất hiện nút ⏸ (Pause), bên trái hiện ❌ (Cancel / Xóa file đang quay).
  - **Snackbar (Trạng thái Validation ngầm):** Nếu Step trước bị AI Fail, hiện dòng Cảnh báo nền đỏ ở DƯỚI CÙNG mép màn hình: "Step 2 góc máy chưa tốt. (Nút Kháng Cáo)".

### 2.4 Pop-up & Quản lý Upload (Upload Manager)
- **Mô tả:** Tiến trình diễn ra khi Submit 100% kịch bản.
- **Component:**
  - Chữ "Đang Tải Lên Đám Mây" kèm Progress Ring (Vòng tròn %) mượt nằm giữa. 
  - Hiển thị Text "Không được đóng ứng dụng hoàn toàn" bên dưới ngầm định.
  - **Cleanup Modal:** "Upload xong 100%. Tiết kiệm bộ nhớ máy!". Nút Primary: "Xóa File Gốc", Nút Secondary (Dạng Text): "Để tôi tự quản lý".

### 2.5 Màn hình Thống kê Video (My Videos)
- **Mô tả:** Chứa các thẻ dọc hoặc Grid hệ thống Media của User.
- **Component:**
  - Video Thumbnail. Ngày Upload.
  - Ba biểu tượng nhỏ ngang nhau dưới mỗi thẻ YouTube (Màu gốc): 👁 X View - 👍 Y Likes - 👎 Z Dislikes.

---

## 3. Đặc tả Cấu thành Giao diện Admin (Web Portal)

*(Responsive: Hoàn thiện nhất trên Desktop / Tablet ngang)*

### 3.1 Sidebar (Thanh Điều hướng Trái)
- Màu Gradient than chì (`#1E1E1E`) tĩnh.
- Các menu: "Dashboard Thống kê", "Người Dùng Mobile", "Cấu hình API/Storage", "Log Hệ Thống AI".

### 3.2 UI: Bảng Quản lý User (User Management DataTable)
- Bảng rộng (`Full Width`), lưới border siêu mỏng.
- Các cột: Email | Tên | Trạng Thái | AI Validation.
- **Micro-Interaction:** 
  - Cột *AI Validation* không dùng Text ("Bật/Tắt") mà dùng *Toggle Switch Component* kiểu dáng iOS. Admin bấm một phát là nó chạy Backend ngầm luôn không cần nút "Lưu (Save)" rườm rà.
  - Mỗi khi Toggle, hiện một Toast Xanh Góc Phải trên cùng ("Đã cập nhật trạng thái Verify").

### 3.3 UI: System Config Form
- **Mô tả:** Giao diện mật, ẩn đi các Passwords.
- **Component:**
  - Inputs (Provider, Buckets, Endpoint URL) to, phân chia Margin xa, dễ nhìn lướt.
  - Ô "Access Secret/Key" dùng Type `password`, có icon `Con mắt` (Show/Hide) ở góc phải Input để Admin có thể kiểm tra xem copy-paste đúng không.
  - Action Footer dưới cùng màn dính chặn (Sticky Bar): Nút "Lưu Cấu Hình (Save Configuration)".

---

## 4. Hướng dẫn cho Developer (Bàn giao Handoff)
- **Tài nguyên Hình ảnh / SVG:** Khuyến nghị dùng thư viện Icon chuẩn `Lucide Icons` (hoặc Phosphor Icons).
- **Animation (Mobile):** Yêu cầu đội Dev (React Native/Flutter) chú ý vào Animation "chuyển Step" -> không được Faded Out toàn bộ màn hình, chỉ Faded Out cái bảng Text Hướng dẫn để User không bị giật mình.
- **Hiệu năng Camera:** Không đẩy filter AR (Augmented Reality) gì trên Camera vì sẽ vắt kiệt RAM, giữ nguyên luồng Native Camera Feed sạch 100%.
