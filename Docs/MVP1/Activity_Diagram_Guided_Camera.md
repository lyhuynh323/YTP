# Activity Diagram: Guided Camera Workflow

Biểu đồ Activity dưới đây (dưới dạng Mermaid UML) mô tả chi tiết luồng User thực hiện việc quay video, lưu trữ thiết bị, upload để AI xác thực, và hoàn thiện một kịch bản trên ứng dụng Mobile App (End-User) theo định nghĩa của MVP V1.

```mermaid
graph TD
    classDef startend fill:#000,stroke:#333,stroke-width:2px,color:#fff,shape:circle;
    classDef state fill:#e3f2fd,stroke:#1565c0,stroke-width:2px;
    classDef decision fill:#fff3e0,stroke:#e65100,stroke-width:2px,shape:diamond;
    classDef ai fill:#fce4ec,stroke:#ad1457,stroke-width:2px;

    Start((Bắt đầu)):::startend --> SelectScript[User chọn một Kịch bản có sẵn]:::state
    SelectScript --> InitStep[Hệ thống load UI Step số N của Kịch bản]:::state
    
    InitStep --> SetupCamera[App tự động bật Camera và Hiển thị yêu cầu góc quay Best Practice]:::state
    
    SetupCamera --> WaitAction{Thao tác User}:::decision
    WaitAction -->|Bấm Record| Recording[Đang Ghi hình]:::state
    
    Recording --> InRecordControl{Điều khiển Quá trình quay}:::decision
    
    InRecordControl -->|Bấm Pause| Paused[Tạm dừng]:::state
    Paused -->|Bấm Resume| Recording
    
    InRecordControl -->|Bấm Cancel| CancelRec[Xóa file vừa quay, Bỏ qua]:::state
    CancelRec --> SetupCamera
    
    InRecordControl -->|Bấm Stop| ReviewStage[Dừng & Phát luồng Review kết quả]:::state
    
    ReviewStage --> IsSatisfied{User hài lòng kết quả?}:::decision
    IsSatisfied -->|Chưa hài lòng, Bỏ đi| CancelRec
    IsSatisfied -->|Hài lòng| SaveLocal[Lưu Source vào Local Device Storage]:::state
    
    SaveLocal --> NextAction{Bấm chuyển Next Step?}:::decision
    NextAction -->|Bấm| CheckConfig{Hệ thống kiểm tra Config \n Validate ON hay OFF?}:::decision
    
    CheckConfig -->|Config ON| UploadAction[Upload hình ảnh/video của Step này về Backend API]:::state
    
    UploadAction --> VerifyCheck{Hệ thống AI Verify kịch bản góc quay}:::ai
    
    VerifyCheck -->|Verify Thất bại| AlertError[Gửi cảnh báo lỗi về App Cần quay lại]:::state
    
    AlertError --> AllowBypass{AI làm khó Bấm Bỏ Qua Kháng cáo}:::decision
    AllowBypass -->|Không Bỏ qua Quay lại| SetupCamera
    AllowBypass -->|Bấm Bỏ Qua| FlagAndPass[Lưu Backend với cờ Flagged để Admin check tay sau này]:::state
    FlagAndPass --> SaveAPI
    
    VerifyCheck -->|Verify Thành công| SaveAPI[Backend chốt Source cho Step hiện tại]:::state
    CheckConfig -->|Config OFF| SaveAPI
    
    SaveAPI --> HasNextStep{Còn Step nào chưa quay?}:::decision
    
    HasNextStep -->|Còn Step| LoadNextStep[Load UI thông tin của Step kế tiếp]:::state
    LoadNextStep --> SetupCamera
    
    HasNextStep -->|Hết Step 100%| EnableSubmit[App thiết lập trạng thái Hoàn Thành Kịch Bản và Mở Nút Submit]:::state
    
    EnableSubmit --> ClickSubmit{User bấm Submit?}:::decision
    ClickSubmit -->|Bấm| FinalUpload[Hệ thống đẩy toàn bộ cấu trúc file về Drive hoặc Admin Storage]:::state
    
    FinalUpload --> End((Kết thúc)):::startend
```

### Thuyết minh quy trình
1. **Camera Controls**: Biểu đồ miêu tả rõ vòng lặp Record -> Pause/Stop/Cancel, giúp user hoàn toàn kiểm soát đoạn video thô ngay cục bộ trước khi quyết định review.
2. **Lưu Local**: App chỉ tiến hành lưu vào Local Device sau khi người dùng hài lòng với đoạn video/hình ảnh vừa quay (`SaveLocal`).
3. **Chốt chặn AI Verify (Async và Bypass)**: Phụ thuộc vào cấu hình User (ON/OFF). Nếu ON, luồng Upload sẽ chạy ngầm không khoá màn hình chờ. Nếu Verify sai, App báo lỗi, User có thể chọn Kháng Cáo (Bypass) để đi thẳng thay vì bị kẹt lại vô lý (Tránh lỗi AI đánh nhầm False Positive).
4. **Final Submit và Cleanup**: Sau khi các Step kết thúc, trạng thái Ready for Submit được kích hoạt qua phương thức Chunked Upload (Thanh Progress bar). Sau khi đẩy Storage thành công 100%, Device sẽ bung popup hỏi giải phóng lưu trữ cục bộ để tránh rác dung lượng cho User.
