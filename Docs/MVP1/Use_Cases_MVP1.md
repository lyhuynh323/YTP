# Use Cases - MVP Version 1

Dưới đây là các Use Cases đặc tả cụ thể cho phiên bản MVP1, bám sát theo luồng Tự động/Thủ công khởi tạo kịch bản, quay camera Guided Creation với chế độ Bật/Tắt AI Verify được quy định bởi Admin.

## 1. Actor: Admin
### UC-AD-01: Quản lý End-User
- **Mô tả**: Admin tạo, chỉnh sửa, khóa tài khoản người dùng Mobile App.
- **Quy trình chính**: 
  1. Admin thêm mới End-user hoặc cập nhật. 
  2. Admin **bật hoặc tắt** tính năng `AI Validation (Verify hình ảnh/video)` cho riêng User này.
  3. Cung cấp thông tin login cho User.

### UC-AD-02: Cấu hình Storage & API Endpoints
- **Mô tả**: Admin thiết lập đường dẫn Storage (Drive, S3, NAS) làm máy chủ đích nhận video từ App.
- **Quy trình chính**: 
  1. Admin điền thông tin endpoint, Secret Key/Token.
  2. Hệ thống mã hóa thông tin. Mobile App dùng API lấy Config này để đẩy file lên trong quá trình Submit.

### UC-AD-03: Theo dõi Dashboard
- **Mô tả**: Admin theo dõi danh sách Kênh Youtube, User, Video và lấy dữ liệu số liệu (Views, Likes, Dislikes) cơ bản qua YouTube API.

---

## 2. Actor: End-User (Creator sử dụng Mobile App)

### UC-EU-01: Khởi tạo Kịch bản AI (First Login Auto & Manual Trigger)
- **Mô tả**: Sinh kịch bản Trending tự động cho việc quay.
- **Tiền điều kiện**: User đăng nhập thành công.
- **Quy trình chính**:
  1. **Khởi tạo lần đầu**: Login lần 1, backend phát hiện chưa có script $\rightarrow$ Backend tự gọi LLM gen 5 Kịch bản $\rightarrow$ App render danh sách.
  2. **Thủ công (Location-based)**: Từ Dropdown chọn Quốc Gia (Location), chọn "Cập nhật Trending" $\rightarrow$ Backend gọi LLM gen 5 Script mới $\rightarrow$ Backend **ghi đè** các script cũ trạng thái `New`.
  3. **Làm mới Đơn lẻ**: User bấm đổi kịch bản trên một thẻ cụ thể $\rightarrow$ Backend tạo 1 kịch bản khác thay vào chỗ trống.

### UC-EU-02: Quay phim & Thao tác Camera theo Guideline (Camera Controls)
- **Mô tả**: User chọn Step, App bung Camera kèm yêu cầu Text tĩnh.
- **Quy trình chính**:
  1. User đọc Guideline (Dạo cảnh 5s góc 45 độ, ...).
  2. User nhấn **Record**. 
  3. User nhấn **Pause / Resume** tùy thích.
  4. Nếu bị hỏng, User nhấn **Cancel**, xóa file rác, App trở lại trạng thái sẵn sàng Record ở cùng Step đó.
  5. Khi nhấn **Stop**, App phát Preview. Nếu User hài lòng thì chuyển qua xác nhận lưu, nếu không hài lòng thì quay lại thao tác. 

### UC-EU-03: Chuyển tiếp & Xác Thực (AI Step-by-Step Validation)
- **Mô tả**: Xác minh nội dung trước khi sang Next Step.
- **Quy trình chính**:
  1. User bấm `Next Step` để khóa cảnh quay hiện tại.
  2. App kiểm tra cấu hình User.
  3. **Nếu Config = OFF**: Backend chốt source, cho phép load UI của Step kế tiếp.
  4. **Nếu Config = ON**:
     - App tự động chốt cảnh và **chạy ngầm (Background Async Upload)** file video lên API.
     - Đồng thời, UI ngay lập tức **mở khóa Step kế tiếp** để User thao tác tiếp.
     - Hé thống AI Review trả kết quả về App qua Event Socket/Message:
       - Fail $\rightarrow$ Bắn Snackbar báo lỗi ở Step cũ. Cho phép User "Kháng cáo (Bypass)" nếu AI làm khó (File bị gắn cờ *Flagged*).
       - Pass $\rightarrow$ Bắn Snackbar "Step X Đạt".

### UC-EU-04: Lưu Local Device và Submit Upload
- **Mô tả**: Lưu file tạm thời dưới máy và Gửi tổng thể toàn bộ khi Hoàn thành.
- **Quy trình chính**:
  1. Mọi source do User bấm Stop và Hài Lòng ở mỗi Step sẽ được lưu ngay vào Gallery thư viện của điện thoại (`Local Device Storage`).
  2. Khi Hoàn thành toàn bộ Step (100%), App xuất hiện nút **Submit / Tải Lên**.
  3. System đóng gói folder: Truyền tải Chunked Mode, kèm Progress Bar an toàn nếu rớt mạng. Push về Storage Admin hoặc Cá Nhân User.
  4. Sau khi Done 100%, bật Modal hỏi người dùng có muốn **Xóa Source Local (Giải phóng bộ nhớ)** không để bảo vệ dung lượng thiết bị.

### UC-EU-05: Quản lý thư viện My Videos
- **Mô tả**: Xem trạng thái và thông số.
- **Quy trình chính**:
  1. Vào danh sách Video.
  2. Video mới quay/chờ up = Trạng thái `Mới`.
  3. Video đã link lên Youtube $\rightarrow$ Bấm xem chi tiết hiển thị Like, Dislikes, Lượt xem thực tế từ Backend.
