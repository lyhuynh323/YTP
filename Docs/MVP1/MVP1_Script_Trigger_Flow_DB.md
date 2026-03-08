# Luồng Khởi Tạo Kịch Bản AI & Cơ Chế Ghi Đè (MVP V1)

Tài liệu này định nghĩa chi tiết thiết kế CSDL (Database Schema) tối thiểu và Sơ đồ Tuần tự (Sequence Flow) cho cơ chế tự động Trigger khởi tạo kịch bản AI ngay trên End-User Mobile App (Thay vì Admin trigger như thiết kế ban đầu).

## 1. Minimal Database Schema (ERD)

Mô hình dữ liệu tinh gọn tập trung vào việc lưu trữ trạng thái của Kịch Bản (`Scripts`) và thiết lập cấu hình.

```mermaid
erDiagram
    USERS {
        uuid id PK
        string email
        string full_name
        boolean is_active
        datetime created_at
    }

    SCRIPTS {
        uuid id PK
        uuid user_id FK "Người sở hữu kịch bản"
        string title
        text content "Nội dung, các Step yêu cầu quay"
        string location "Khu vực lấy trend"
        string status "Enum: 'New', 'Processing', 'Completed'"
        datetime created_at
        datetime updated_at
    }

    ADMIN_STORAGE_CONFIGS {
        uuid id PK
        string provider "S3, GCS, NAS"
        string base_path "/{user_id}/{YYYYMMDD}/{script_name}"
        text access_token_encrypted "Khóa truy cập bảo mật"
    }

    USERS ||--o{ SCRIPTS : "sở hữu (has many)"
```

- **Lưu ý Status của Script**: 
  - `New`: Kịch bản mới gen, User chưa thực hiện quay chụp gì. **ĐƯỢC PHÉP GHI ĐÈ**.
  - `Processing`: User đã bắt đầu bấm quay ở 1 vài Step bên trong. **KHÔNG ĐƯỢC GHI ĐÈ**.
  - `Completed`: User đã quay xong toàn bộ Step và Submit. **KHÔNG ĐƯỢC GHI ĐÈ**.

---

## 2. Sequence Flow: Khởi tạo tự động (First Login Auto-Trigger)

Luồng này diễn ra khi User đăng nhập thành công vào màn hình chính của Mobile App lần đầu tiên.

```mermaid
sequenceDiagram
    participant App as Mobile App (End-user)
    participant API as BFF / API Gateway
    participant DB as Database
    participant AI as AI / ML Service (LLM)
    participant YT as YouTube Data API

    App->>API: GET /api/v1/scripts (Fetch User Scripts)
    API->>DB: Query: SELECT * FROM scripts WHERE user_id = ID
    DB-->>API: Return Empty Array (Count = 0)
    
    note over API,DB: Hệ thống phát hiện User mới, chưa có kịch bản nào
    
    API->>API: Initialize First-time Trigger
    API->>YT: GET /trends?location=default
    YT-->>API: Return Top Topics / Keywords
    
    API->>AI: POST /generate_scripts { topics, count: 5 }
    note right of AI: AI LLM phân tích và sinh ra 5 kịch bản phân rã theo Step 
    AI-->>API: Return 5 JSON Scripts
    
    API->>DB: INSERT 5 records INTO scripts (status: 'New', user_id: ID)
    DB-->>API: Insert Success
    
    API-->>App: Return 5 New Scripts
    App->>App: Render danh sách Kịch Bản Dashboard
```

---

## 3. Sequence Flow: Làm mới thủ công / Ghi đè (Manual Update)

Luồng này diễn ra khi User chủ động nhấn nút "Cập nhật Kịch Bản" trên App để lấy Trend mới nhất khu vực hiện tại.

```mermaid
sequenceDiagram
    participant App as Mobile App (End-user)
    participant API as BFF / API Gateway
    participant DB as Database
    participant AI as AI / ML Service (LLM)
    participant YT as YouTube Data API

    App->>API: POST /api/v1/scripts/trigger (location: 'VN')
    
    note over API,DB: Bước 1: Gọi AI gen kịch bản mới
    API->>YT: GET /trends?location=VN
    YT-->>API: Return Trending Topics in VN
    
    API->>AI: POST /generate_scripts (topics, count: 5)
    AI-->>API: Return 5 New JSON Scripts (VN Context)
    
    note over API,DB: Bước 2: Cơ chế Ghi đè (Overwrite Rule)
    API->>DB: Query: SELECT id FROM scripts WHERE user_id = ID AND status = 'New'
    DB-->>API: Return List of Identifiers (ID1, ID2, ...)
    
    alt If Target Scripts Exist
        API->>DB: UPDATE/DELETE các Script_IDs (hoặc ghi đè nội dung mới)
    else No 'New' Scripts (Tất cả đều Processing/Completed)
        API->>DB: Bỏ qua bước ghi đè (Hoặc báo lỗi App: "Hoàn thiện script cũ trước khi cập nhật")
    end
    
    API->>DB: INSERT (Số lượng script mới tương ứng để đảm bảo có đủ 5 script sẵn sàng)
    
    API-->>App: Return Updated Script List
    App->>App: Re-render UI: Hiển thị kịch bản Trend mới, giữ nguyên Processing/Completed
```

### Thuyết minh cơ chế:
1. **Kiểm tra trạng thái**: Backend luôn kiểm tra cột `status` trong `SCRIPTS`.
2. **Hard Replace / Soft Update**: Với các Scripts `status = 'New'`, hệ thống có thể hoàn toàn gạch bỏ (Hard Delete) và thay thế bằng Record mới từ LLM trả về, bảo đảm UI của User luôn có 5 Kịch bản tươi mới (Fresh Scripts) mỗi lần ấn Fetch.
3. Việc lấy location truyền trực tiếp từ App lên API giúp Gen kịch bản sát sườn với tệp Youtube Viewers mục tiêu của Kênh.
