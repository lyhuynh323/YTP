# Đặc tả Giao diện Lập trình Ứng dụng (API Specifications) - MVP V1

Tài liệu này xác định các endpoints API (RESTful) chính (Core Endpoints) dùng cho giao tiếp giữa Frontend (Mobile App/Web Portal) và Backend. Định dạng trao đổi dữ liệu tiêu chuẩn là JSON (`application/json`). Authentication sử dụng chuẩn Bearer Token (JWT).

## 1. Auth & Admin APIs

### 1.1 Đăng nhập (Login)
- **POST** `/api/v1/auth/login`
- **Mô tả:** Authenticate user (Admin hoặc End-user), cấp Access Token.
- **Request Body:**
  ```json
  {
    "email": "user@example.com",
    "password": "secure_password"
  }
  ```
- **Response (200 OK):**
  ```json
  {
    "token": "ey...",
    "user": {
      "id": "uuid",
      "role": "end_user",
      "full_name": "Ly Huynh"
    }
  }
  ```

### 1.2 Quản lý Cấu hình Storage (Admin Only)
- **PUT** `/api/v1/admin/config/storage`
- **Mô tả:** Admin thiết lập credentials để kết nối Storage.
- **Request Body:**
  ```json
  {
    "provider": "aws_s3",
    "bucket_name": "sharewind-media",
    "region": "ap-southeast-1",
    "access_key": "***",
    "secret_key": "***"
  }
  ```
- **Response (200 OK):** `{"message": "Storage Configuration Saved Successfully"}`

### 1.3 Quản lý Người dùng & AI Validation (Admin Only)
- **PUT** `/api/v1/admin/users/{user_id}`
- **Mô tả:** Bật hoặc tắt tính năng AI Validation cho riêng lẻ User.
- **Request Body:**
  ```json
  {
    "ai_validation_enabled": false
  }
  ```
- **Response (200 OK):** `{"message": "User validation settings updated."}`

---

## 2. Kịch Bản APIs (Script Engine)

### 2.1 Fetch Kịch bản (Trigger Tự khởi tạo Lần đầu)
- **GET** `/api/v1/scripts`
- **Mô tả:** Khi App gọi (thường là sau Login), nếu Backend check database không có script nào, DB sẽ block request 2-3s chạy AI sinh ra 5 cái mới rồi mới đẩy xuống JSON mảng các Scripts. Tái sử dụng để Get Script list bình thường.
- **Response (200 OK):**
  ```json
  {
    "scripts": [
      {
        "id": "uuid-script-1",
        "title": "Unboxing iPhone 15 Pro Max",
        "status": "New",
        "steps": [
          {"step_order": 1, "instruction": "Quay cận cảnh hộp...", "angle": "45 degree"}
        ]
      }
    ]
  }
  ```

### 2.2 Trigger Làm Mới Kịch Bản (Cơ chế Ghi đè)
- **POST** `/api/v1/scripts/trigger`
- **Mô tả:** End-User cung cấp mảng location, Backend đè lên các bản Record có trạng thái `New` ở DB.
- **Request Body:**
  ```json
  {
    "location": "VN",
    "category_pref": "tech"
  }
  ```
- **Response (200 OK):** Trả lại 5 Scripts mới tinh tương tự `2.1`.

---

## 3. Upload & Camera Validation APIs

### 3.1 Xin URL Tải lên Tạm thời (Pre-Signed URL)
- **POST** `/api/v1/media/presign-upload`
- **Mô tả:** Giúp Client có thể bắn file Raw cực to (vài trăm MB) thẳng hướng AWS/GCS mà không chui qua Node.js làm nghẽn.
- **Request Body:**
  ```json
  {
    "script_id": "uuid-script",
    "step_order": 1,
    "file_name": "vid_raw_step1.mp4",
    "mime_type": "video/mp4"
  }
  ```
- **Response (200 OK):**
  ```json
  {
    "upload_url": "https://s3.amazonaws.com/bucket/user1/abcd.mp4?signature=..."
  }
  ```

### 3.2 Báo cáo Video Upload Xong (Trigger AI)
- **POST** `/api/v1/validation/trigger`
- **Mô tả:** Mobile App báo Client Up trực tiếp lên S3 xong rồi. Server bắt đầu chạy Job Background thẩm định video bằng Computer Vision. Gửi 202 ngay tức thì.
- **Request Body:**
  ```json
  {
    "script_id": "uuid-script",
    "step_order": 1,
    "s3_object_key": "user1/abcd.mp4"
  }
  ```
- **Response (202 Accepted):** `{"message": "AI validation queued."}`

### 3.3 Bỏ qua Lỗi (Bypass Validation)
- **PUT** `/api/v1/validation/flag/{script_id}`
- **Mô tả:** AI báo đỏ (Fail), người dùng bực ấn nút "Kháng cáo". Gọi API này để Force Pass cảnh quay.
- **Request Body:**
  ```json
  {
    "step_order": 1,
    "user_message": "AI sai rôi, tôi cầm đúng sp rồi"
  }
  ```
- **Response (200 OK):** `{"message": "Flagged and Bypassed. Step Unlocked."}`
