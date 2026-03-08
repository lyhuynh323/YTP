# Sơ đồ Quy trình Nghiệp vụ (Business Process & Workflow Diagrams) - MVP V1

Tài liệu này cung cấp các sơ đồ luồng (Workflow Diagrams) thể hiện rõ quy trình tương tác giữa Admin và End-User dựa trên các yêu cầu nghiệp vụ đã xác định ở BRD. Sơ đồ được vẽ bằng cú pháp Mermaid.

## 1. Quy trình Tổng thể (End-to-End Core Workflow)

Sơ đồ này mô tả toàn cảnh quá trình từ lúc Admin tạo tài khoản cho đến khi End-user hoàn thành một video kịch bản.

```mermaid
graph TD
    classDef admin fill:#f9e79f,stroke:#d4ac0d,stroke-width:2px;
    classDef system fill:#d6eaf8,stroke:#2e86c1,stroke-width:2px;
    classDef user fill:#d5f5e3,stroke:#28b463,stroke-width:2px;
    
    A[Admin: Tạo Tài khoản & Cấu hình AI Verify]:::admin --> B(Backend: Gửi thông tin Login cho User):::system
    A2[Admin: Bật/Tắt AI Validation]:::admin -.-> B
    
    B --> C{User: Đăng nhập App lần đầu?}:::user
    
    C -- Lần Đầu --> D[Backend: Tự động gọi LLM sinh 5 Kịch bản]:::system
    D --> E(User: Nhận 5 Kịch bản Trending):::user
    
    C -- Đã có Kịch bản --> E
    
    E --> F{User: Cập nhật Kịch bản}:::user
    F -- Đổi Location/Refresh --> G[Backend: Gọi LLM sinh mới, Ghi đè file cũ 'New']:::system
    G --> E
    F -- Hài lòng --> H[User: Chọn 1 Kịch bản để Quay]:::user
    
    H --> I[UI: Hiển thị Camera Step-by-Step]:::system
    
    I --> J[User: Record -> Pause -> Stop -> Cancel]:::user
    
    J -- Chốt Step --> K{Config: AI Validation = ON?}:::system
    
    K -- YES --> L[Backend: Asynchronous Upload ngầm]:::system
    L --> M{AI Verify Kết quả}:::system
    M -- FAIL --> N[User: Bấm Bypass / Kháng cáo]:::user
    N --> O[Backend: Gắn cờ Flagged, vẫn cho qua]:::system
    M -- PASS --> P[Backend: Chốt cảnh quay]:::system
    
    K -- NO --> P
    O --> P
    
    P --> Q{Còn Step nào?}:::system
    Q -- CÒN --> I
    
    Q -- HẾT --> R[User: Bấm Nút Submit]:::user
    
    R --> S[Hệ thống: Tiến trình Chunked Upload lên Storage]:::system
    
    S --> T[UI: Bật Popup Dọn dẹp Local Device]:::system
    T --> U[User: Xác nhận Xóa bộ nhớ đệm]:::user
    
    U --> V((Kết thúc Luồng Sinh Video))
```

## 2. Luồng Khởi tạo Kịch bản & Cơ chế Ghi đè (Script Engine Generation)

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant App as Mobile App
    participant API as Backend (Script Engine)
    participant DB as System Database
    participant AI as LLM Service (OpenAI/Gemini)

    User->>App: Bấm "Cập nhật Trending" (Chọn Location: VN)
    App->>API: POST /api/trigger-scripts { location: "VN" }
    
    API->>AI: Yêu cầu 5 Kịch bản trending tại VN
    AI-->>API: Trả về 5 JSON Kịch bản
    
    API->>DB: Truy vấn Script hiện tại `SELECT * WHERE user_id = X`
    DB-->>API: Trả về danh sách Script hiện tại
    
    API->>API: Lọc Script có status = 'New'
    
    opt Nếu có Script 'New'
        API->>DB: Xóa/Ghi đè (DELETE / UPDATE) các Script cũ này
    end
    
    API->>DB: Lưu 5 Kịch bản mới (status = 'New')
    
    API-->>App: Trả về danh sách kịch bản đã cập nhật
    App-->>User: Hiển thị giao diện Dashboard mới nhất
```

## 3. Luồng Upload Bất đồng bộ và Xác thực AI (Asynchronous AI Validation)

```mermaid
sequenceDiagram
    autonumber
    actor User as End-User
    participant App as Mobile App (UI Thread)
    participant BgWorker as Mobile App (Background Thread)
    participant API as Backend (Validation API)
    participant AI as AI Model

    User->>App: Bấm 'Next Step' (Chốt Scene 1)
    
    App->>BgWorker: Chuyển file video Scene 1 cho Thread chạy ngầm
    App->>App: LẬP TỨC load UI Camera của Scene 2
    User->>App: Bắt đầu đọc hướng dẫn, set up góc quay cho Scene 2
    
    BgWorker->>API: Upload Video Scene 1 (Async)
    API->>AI: Yêu cầu phân tích Video Scene 1
    AI-->>API: Trả về Kết quả (VD: FAIL - Thiếu sáng / Thiếu SP)
    
    API-->>App: Bắn WebSocket/Notification: Result = FAIL
    
    App-->>User: Hiện Snackbar cảnh báo trên UI Scene 2 ("Cảnh báo Step 1")
    
    opt Nếu User chọn Kháng cáo (Bypass)
        User->>App: Bấm Nút [Kháng Cáo]
        App->>API: Báo cáo Bypass Step 1
        API->>API: Đánh dấu File Scene 1 = 'Flagged' (Vẫn chấp nhận)
    end
```
