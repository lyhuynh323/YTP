# Tài Liệu Yêu Cầu Nghiệp Vụ (BRD) - MVP V1

## 1. Tổng Quan Dự Án
**Tên dự án:** ShareWind (AI Digital Content Platform) MVP V1
**Mục tiêu:** Xây dựng nền tảng cốt lõi giúp tự động hóa khâu lên ý tưởng (qua AI kịch bản) và hướng dẫn quay phim (Guided Creation) cho Content Creator (End-User), đồng thời cung cấp công cụ quản trị tập trung cho Admin.

## 2. Các Bên Liên Quan (Stakeholders)
- **Product Owner (PO):** Quyết định tính năng ưu tiên, phê duyệt MVP.
- **Project Manager (PM) / Business Analyst (BA):** Điều phối, phân tích nghiệp vụ và đảm bảo chất lượng tài liệu.
- **Admin (Hệ thống):** Quản trị viên quản lý tệp người dùng, bật/tắt quyền AI.
- **End-User (Content Creator):** Người dùng cuối sử dụng Mobile App để sáng tạo nội dung.

## 3. Quy Trình Nghiệp Vụ Cốt Lõi (Business Process)
1. **Quản lý Tài khoản & Phân quyền:** Admin tạo tài khoản cho End-user và chủ động thiết lập ON/OFF tính năng Verification AI (Computer Vision) cho từng User.
2. **Khởi tạo Kịch bản (Auto/Manual):**
   - **Tự động:** Đăng nhập lần đầu $\rightarrow$ Backend gọi LLM sinh 5 Script $\rightarrow$ Trả về App.
   - **Thủ công:** User chọn Quốc gia $\rightarrow$ Cập nhật Trending $\rightarrow$ Sinh & Ghi đè các Kịch bản cũ.
3. **Quay phim theo Hướng dẫn (Guided Recording):** 
   - User chọn kịch bản $\rightarrow$ App mở giao diện Step-by-Step UI.
   - User thao tác linh hoạt (Quay/Dừng/Xóa tệp nháp) và chốt cảnh quay.
4. **Xác thực AI (Asynchronous Validation):** 
   - Nếu User bị bắt buộc xác thực (ON) $\rightarrow$ Backend Upload ngầm và gọi AI chấm (Pass/Fail). Nếu Fail, User được quyền "Bypass" (Đánh cờ Flagged) để đi tiếp mà không bị kẹt.
5. **Đóng gói & Phân phối (Submit & Storage):**
   - Hoàn thành toàn bộ kịch bản $\rightarrow$ Ứng dụng Chunked Upload toàn bộ file lên thư mục `/lyhuynh_123/20260308/scenario_1/` trên Storage của Admin (Drive/S3).
   - Hỏi người dùng "Xóa Local Storage" để tiết kiệm bộ nhớ điện thoại.

## 4. Yêu Cầu Chức Năng (Functional Requirements)

### 4.1. Admin Web Portal
- **FR_AD_01 (User Management):** Admin có thể tạo, sửa, vô hiệu hóa, và thay đổi trạng thái AI Validation của từng User.
- **FR_AD_02 (Storage Config):** Admin có thể nhập Endpoint, API Key / Access Token để trỏ Mobile App upload video về đúng kho lưu trữ tập trung.
- **FR_AD_03 (Dashboard & Tracking):** Bảng điều khiển hiển thị danh sách kênh YouTube, User hiện hành, và các số liệu cơ bản (Views, Likes, Dislikes) qua YouTube Data API.

### 4.2. Mobile App (End-User)
- **FR_EU_01 (Authentication):** Luồng đăng nhập bằng Email/Password (do Admin cấp).
- **FR_EU_02 (Script Engine):** Cơ chế hiển thị 5 thẻ kịch bản, nút Generate lại toàn bộ, nút Reshuffle (Icon Xí ngầu) 1 kịch bản đơn lẻ.
- **FR_EU_03 (Camera UI):** Giao diện quay phim (Camera overlay) hiển thị hướng dẫn Text cho từng Step (Kịch bản).
- **FR_EU_04 (Camera Controls):** Cung cấp các lệnh Record, Pause, Stop, Cancel.
- **FR_EU_05 (Validation Logic):** Nếu AI Validation = ON, chạy upload ngầm (Background Task). Cấp quyền Bypass (Kháng cáo) nếu AI chấm Fail trên cảnh báo Snackbar.
- **FR_EU_06 (Upload Manager):** Khi ấn "Submit", đẩy thư mục chứa các Step qua Chunked Upload (có Progress bar, có cơ chế Resume khi rớt mạng).
- **FR_EU_07 (Local Cleanup):** Mở Popup nhắc User xóa dữ liệu nháp sau khi Upload 100% thành công.
- **FR_EU_08 (Statistics):** Hiển thị danh sách Video đã upload và bộ chỉ số tương ứng lấy từ Backend.

## 5. Yêu Cầu Phi Chức Năng (Non-Functional Requirements)
- **NFR_01 (Hiệu suất UI):** Giao diện Camera không được phép bị "Đóng băng" (Freeze) trong lúc chờ AI Verify (Asynchronous Upload).
- **NFR_02 (Lưu trữ Cục bộ):** Tối ưu hóa việc ghi file hỏng (Cancel) để không làm đầy ROM thiết bị vô cớ.
- **NFR_03 (Bảo mật):** App lấy Config API Key từ Backend (Dynamic payload) thay vì Hardcode trong App, chống lộ thông tin Storage của Admin.
- **NFR_04 (Khả năng chịu lỗi Upload):** Ứng dụng phải hỗ trợ Upload Resume (Chunked) vì file source thô (RAW video) rất nặng.
