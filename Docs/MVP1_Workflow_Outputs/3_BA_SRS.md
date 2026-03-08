# Tài Liệu Đặc Tả Yêu Cầu Phần Mềm (SRS) - MVP V1

## 1. Giới thiệu
Tài liệu SRS này mô tả chi tiết các yêu cầu kỹ thuật và đặc tả phần mềm cho hệ thống ShareWind MVP V1, bao gồm Backend API, Admin Web Dashboard và End-User Mobile App.

## 2. Kiến trúc Hệ thống Mục tiêu
- **Mobile App:** Phát triển bằng framework Hybrid (gợi ý React Native / Flutter) để build ra Android (APK) và iOS (IPA).
- **Web Portal (Admin):** Single Page Application (React.js / Next.js) giao tiếp với Backend qua RESTful APIs.
- **Backend:** API Gateway / Microservices quản lý luồng dữ liệu (Scripts, Users, Validation Events).
- **Cơ sở dữ liệu (Database):** CSDL Quan hệ (PostgreSQL) lưu trữ cấu hình Admin, metadata User, và trạng thái Script (`New`, `Processing`, `Completed`).
- **Lưu trữ (Storage):** Endpoint linh hoạt do Admin cấu hình (AWS S3, Google Drive API, Local NAS).

## 3. Đặc tả Yêu cầu Chi tiết (Detailed Specifications)

### 3.1. Phân hệ Quản trị (Admin System)
- **API `POST /admin/users`**: Tạo tài khoản mới. Yêu cầu các trường `email`, `password`, `full_name`, và flag `ai_validation_enabled` (boolean).
- **API `PUT /admin/configs/storage`**: Cập nhật thông số Endpoint cho Cloud Storage (Lưu trữ an toàn, mã hóa Access Token ở DB).
- **API `GET /admin/stats`**: Trả về tổng hợp chỉ số kênh YouTube, map theo User_ID.

### 3.2. Phân hệ Kịch bản AI (Script Engine)
- **Luồng First Login Trigger:**
  - Client gửi Login thành công $\rightarrow$ Backend kiểm tra `SELECT count(1) FROM scripts WHERE user_id = :id`.
  - Nếu count = 0, Backend bất đồng bộ gọi service LLM tạo 5 JSON scripts (Context: Default location).
- **Thuật toán Ghi đè (Smart Overwrite Rule):**
  - Khi User bấm "Cập nhật (Location=🇻🇳)", API `POST /api/v1/scripts/trigger` được gọi.
  - LLM trả về 5 kịch bản mới. Backend thực hiện `DELETE FROM scripts WHERE status='New' AND user_id=:id`.
  - Insert lại 5 kịch bản mới vào DB. (Tuyệt đối không đụng vào `status='Processing'` hay `Completed`).
  
### 3.3. Phân hệ Quay phim & Xác thực (Guided Camera & Validation)
- **UI State Machine:** Camera overlay phải thay đổi trạng thái theo Data Model truyền xuống (VD: Step 1 nội dung là gì, góc máy yêu cầu như thế nào).
- **Background Upload Task:**
  - Khi User ấn Next Step (Và Config `ai_validation_enabled = true`), Native Thread của App (hoặc Web Worker giả lập) lập tức đóng gói đoạn Video/Ảnh vừa quay gửi lên API `POST /api/v1/validation/verify`.
  - Trong lúc file rác đang tải lên mạng di động, React Navigation CHUYỂN NGAY sang màn hình (hoặc index) của Step tiếp theo. Không Block UI.
- **AI Feedback Socket/Notification:**
  - Kết quả từ mô hình AI trả về (Nhận diện đồ vật/sản phẩm) sẽ được Backend đẩy xuống App (Qua WebSocket hoặc FCM Data Payload ẩn).
  - App bắt event: Nếu kết quả = "FAIL", xuất Snackbar "Cảnh báo Step [X]", kèm theo Button "Kháng Cáo (Bypass)".
  - Action "Bypass" gọi API `PUT /api/v1/validation/flag/{step_id}` báo cáo Backend lưu dấu vết cờ đỏ của file này nhưng vẫn set `is_completed=true` cục bộ.

### 3.4. Phân hệ Tải lên (Chunked Upload Manager)
- **Yêu cầu File Transfer:** Yêu cầu thư viện Multi-part Chunked Upload (VD: `react-native-background-upload`).
- Cấu trúc thư mục động (Dynamic Pathing): `/lyhuynh_123/20260308/review_iphone_15_pro/video_step_1.mp4`.
- **Cơ chế Dọn dẹp (Local Cleanup):** Listeners bắt sự kiện "Upload Success 100%". Trigger Modal: "Dữ liệu đã lên Cloud. [Xóa File Gốc]". Nếu User đồng ý, gọi API Native xóa trực tiếp thư mục lưu tạm trên ROM máy.

## 4. Đặc tả Giao diện (Thẩm mỹ & UI/UX)
- Giao diện Admin: Chuẩn Dashboard quản trị (Data-dense Layout, Bảng biểu rõ ràng dạng DataTable).
- Giao diện Mobile App: Thiết kế "Premium Dark Mode", tích hợp "Glassmorphism" (các phần tử khói mờ), Gradient điểm xuyết. 
  - Khung camera không bị khuất bởi Text quá lớn. 
  - Vị trí nút Pause/Stop phải to bản để hạn chế bấm trượt.
  
## 5. Ràng buộc Tích hợp Hệ thống (Integrations)
- **YouTube Data API v3:** Dùng để query lượt view, like theo định kỳ (Scheduled Cronjob ở Backend). Không yêu cầu User phải tự xác thực OAuth trên App để cho phép Post video, Admin sẽ up thủ công tập trung.
- **AI/ML Service (LLM):** OpenAI GPT-4o hoặc Gemini (hoặc tương đương) để phân rã kịch bản thành các mảng JSON có cấu trúc (Steps, Instructions, Locations).
