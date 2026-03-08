# Thiết kế Cơ sở Dữ liệu (Database Schema) - MVP V1

Tài liệu này xác định cấu trúc lưu trữ của hệ thống ShareWind MVP V1 (Sử dụng PostgreSQL DBMS). Thiết kế tôn trọng tính tối giản nhưng vẫn đáp ứng đủ logic Ghi đè AI (Overwrite script) và Phân quyền (Validation ON/OFF).

## 1. Bảng Dữ liệu Chính (Core Tables)

### 1.1. Bảng `users` (Quản lý User & Admin)
Chứa thông tin đăng nhập và Cờ chức năng (Feature Flags).

| Column Name | Data Type | Properties | Description |
| :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | Khóa chính của bảng user. |
| `email` | `VARCHAR(255)` | UNIQUE, NOT NULL | Tên đăng nhập. |
| `password_hash` | `VARCHAR` | NOT NULL | Mật khẩu đã được băm. |
| `role` | `VARCHAR(50)` | DEFAULT 'end_user' | Quyền hạn (`admin`, `end_user`). |
| `ai_validation_enabled` | `BOOLEAN` | DEFAULT true | **Dành riêng cho MVP1:** Flag do Admin thiết lập để ép End-user chạy luồng Verify (ON) hoặc Bypass ngay lập tức (OFF). |
| `is_active` | `BOOLEAN` | DEFAULT true | Trạng thái tài khoản (Login / Deactivated). |
| `created_at` | `TIMESTAMP` | DEFAULT NOW() | Thời điểm tạo. |

---

### 1.2. Bảng `scripts` (Quản lý Kịch bản Trending do LLM đẻ ra)
Điểm giao tiếp giữa kết quả AI gen ra và Mobile App Dashboard.

| Column Name | Data Type | Properties | Description |
| :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | Mã hệ thống. |
| `user_id` | `UUID` | FK `users` | File kịch bản của tài khoản nào. |
| `title` | `VARCHAR` | NOT NULL | Đề bài/Tiêu đề. |
| `steps_json` | `JSONB` | NOT NULL | Lưu linh hoạt các bước quay (Instruction Text, Góc Máy, Text chạy chữ). |
| `location` | `VARCHAR` | DEFAULT 'Global' | Quốc gia làm base lấy Trend. |
| `status` | `VARCHAR(50)` | DEFAULT 'New' | Vòng đời của script: `New` (Chưa quay cảnh nào -> **AI được quyền GHI ĐÈ XÓA BỎ**), `Processing` (Đang quay giữa chừng -> **KHÓA không cho ghi đè**), `Completed` (User đã Submit thư mục lên Server). |
| `created_at` | `TIMESTAMP` | - | Thời gian gen. |
| `updated_at` | `TIMESTAMP` | - | Thời gian user cập nhật thao tác quay cuối cùng. |

---

### 1.3. Bảng `step_validations` / `media` (Lưu lịch sử Tracking Video Của Bước Quay)
Quản trị kết quả Verify (Computer Vision) & File thư mục đích. 

| Column Name | Data Type | Properties | Description |
| :--- | :--- | :--- | :--- |
| `id` | `UUID` | PK | - |
| `script_id` | `UUID` | FK `scripts` | Trực thuộc kịch bản nào. |
| `step_order` | `INTEGER` | NOT NULL | Là Step thứ bao nhiêu trong cái `steps_json` kia. |
| `file_storage_url` | `VARCHAR` | NOT NULL | Đường dẫn S3 / Drive lưu file thực tế (`/user1/2026/script_X/step_1.mp4`). |
| `ai_status` | `VARCHAR(50)` | DEFAULT 'Pending' | Trạng thái AI xác thực: `Pending` (Đang upload/ngầm gọi), `Passed` (Duyệt thành công), `Failed` (Mô hình chê góc máy), `Flagged` (User văng tục AI rồi tự ấn Kháng Cáo / Bypass). |
| `created_at` | `TIMESTAMP` | - | Tgian đẩy file |

---

### 1.4. Bảng `system_configs` (Cấu hình Admin Storage & API)
Nơi lưu các thông số tập trung, chống hardcode dưới App Mobile văng lộ KeyAWS (Thực chất Frontend lấy cái này đẩy file hoặc Backend lấy sinh Pre-signed URL).

| Column Name | Data Type | Properties | Description |
| :--- | :--- | :--- | :--- |
| `key_name` | `VARCHAR` | PK | Tên config: `s3_bucket_name`, `s3_endpoint`, `aws_access_key`. |
| `encrypted_value` | `TEXT` | NOT NULL | Giá trị của setting (Đã Re-encrypted qua KMS / App Secret). |
| `updated_at` | `TIMESTAMP` | - | - |
