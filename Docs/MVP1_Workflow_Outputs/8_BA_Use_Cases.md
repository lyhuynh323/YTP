# Danh sách Use Cases (Use Case Catalog) - MVP V1

Tài liệu này (được soạn bởi Global Business Analyst) cung cấp danh sách chi tiết tất cả các Use Cases (Ca Sử Dụng) trong hệ thống ShareWind MVP V1. Tài liệu này đóng vai trò là cơ sở (tham chiếu) để đội QA/QC (Tester) xây dựng các Test Cases, Test Scenarios sau này.

---

## 1. Phân hệ Quản trị viên (Actor: Admin)

### UC-AD-01: Quản lý Tài khoản End-User (End-User Management)
- **Mô tả:** Admin thực hiện tạo mới, chỉnh sửa, vô hiệu hóa tài khoản của người dùng cuối (Mobile App), và trực tiếp cấu hình quyền xác thực AI (AI Validation).
- **Tiền điều kiện:** Admin đã đăng nhập vào Web Portal thành công.
- **Luồng Cơ Bản (Basic Flow):**
  1. Admin chọn menu "Quản lý Người dùng".
  2. Hệ thống hiển thị danh sách người dùng phân trang.
  3. Admin chọn "Thêm mới" hoặc "Chỉnh sửa" một user.
  4. Admin nhập/cập nhật: Email, Full Name, Trạng thái (Active/Inactive), và Bật/Tắt (Toggle) `Bắt buộc Xác thực AI`.
  5. Admin nhấn Lưu.
  6. Hệ thống báo thành công và cập nhật Database.
- **Luồng Thay Thế / Ngoại Lệ (Alternate/Exception Flows):**
  - *Email trùng lặp:* Hệ thống báo lỗi email đã tồn tại. Yêu cầu nhập email khác.
- **Hậu điều kiện:** Dữ liệu End-user được cập nhật. Nếu thay đổi status Active/Inactive, User lập tức bị văng khỏi App (nếu Inactive).

### UC-AD-02: Cấu hình API và Storage (Storage & API Settings)
- **Mô tả:** Admin khai báo thông số kết nối nơi lưu trữ video từ App (Storage, NAS, Drive) và các API Keys liên quan.
- **Tiền điều kiện:** Admin đăng nhập Web Portal.
- **Luồng Cơ Bản:**
  1. Admin mở tab "Cấu hình Hệ thống".
  2. Admin nhập các trường: Storage Provider (S3/Drive), Endpoint URL, Access Token / Secret Key.
  3. Admin nhấn "Lưu".
  4. Hệ thống mã hóa các Token (Encrypt) và lưu vào bảng Database Config.
- **Hậu điều kiện:** App Mobile khi có File Upload sẽ tự fetch các cấu hình này để có link Pre-signed upload.

### UC-AD-03: Xem Dashboard và Thống kê (Dashboard Monitoring)
- **Mô tả:** Admin giám sát số lượng kênh, user và các chỉ số thống kê lấy từ YouTube API.
- **Tiền điều kiện:** Admin đăng nhập.
- **Luồng Cơ Bản:**
  1. Admin vào "Dashboard".
  2. Hệ thống gọi API lấy Data Aggregate (Tổng views, likes, dislikes) và render biểu đồ, DataTable.
  3. Admin lọc dữ liệu theo Người dùng hoặc Kênh YouTube.

---

## 2. Phân hệ Người Dùng Cuối (Actor: End-User / Content Creator)

### UC-EU-01: Auto-Trigger Kịch bản LLM (First Login Auto-Generate)
- **Mô tả:** Khi User lần đầu trải nghiệm App, chưa có kịch bản nào, hệ thống tự động gọi AI rải sẵn 5 kịch bản trending.
- **Tiền điều kiện:** User đăng nhập App (Login thành công).
- **Luồng Cơ Bản:**
  1. App truy vấn API lấy danh sách Script.
  2. API ném luồng kiểm tra `count(script) == 0`.
  3. API tự chuyển qua Background Task gọi LLM sinh 5 Script.
  4. App hiện Progress "Đang thiết lập AI...".
  5. Background trả 5 Kịch bản mới (Trạng thái `New`), App render danh sách.
- **Alternate Flows:**
  - *Lỗi LLM timeout:* App báo lỗi "Hệ thống AI đang bận. Vui lòng bấm Cập nhật lại" và hiển thị danh sách rỗng.
- **Hậu điều kiện:** Database có 5 Kịch bản `New` gán cho User_ID này.

### UC-EU-02: Làm mới Kịch bản (Refresh / Overwrite Scripts)
- **Mô tả:** User chủ động đổi Trend quốc gia khác, hoặc yêu cầu 5 bài mới thay cho đống bài `New` chưa xài.
- **Tiền điều kiện:** App đang hiển thị Dashboard.
- **Luồng Cơ Bản:**
  1. User bấm thẻ Dropdown Location, chọn "Việt Nam", nhấn "Cập nhật Trending".
  2. Cảnh báo hiển thị: API tiến hành gen 5 bài mới.
  3. API check Database: XÓA tất cả Script đang là `New` của User này. 
  4. API lưu 5 script vừa gen.
  5. App tự động render đè lên các bản `New` cũ. Các bản `Processing` và `Completed` nằm ngoài cùng và giữ nguyên.
  
### UC-EU-03: Đổi một Kịch bản cụ thể (Single Reshuffle)
- **Mô tả:** User chỉ không thích 1 trong 5 Script, ấn icon Xí ngầu để đổi đúng 1 thẻ đó.
- **Tiền điều kiện:** Kịch bản đó phải có status = `New`.
- **Luồng Cơ Bản:**
  1. User bấm Reshuffle vào thẻ kịch bản A.
  2. API tạo 1 kịch bản khác thay cho Kịch bản A, delete bản cũ.
  3. App cập nhật lại thẻ UI liền mạch.

### UC-EU-04: Thao tác Camera theo Hướng dẫn (Guided Camera Controls)
- **Mô tả:** Core action. User bấm Record/Pause/Stop cho 1 step.
- **Tiền điều kiện:** Bấm vào 1 script $\rightarrow$ App load Camera UI.
- **Luồng Cơ Bản:**
  1. App mở Camera, hiện text yêu cầu đè lên trên.
  2. User nhấn Record.
  3. User nhấn Pause, thay đổi góc máy, nhấn Resume để quay tiếp (Nối file Video).
  4. User nhấn Stop. Trình Review Video mở lên phát lại nội dung.
- **Alternate Flows:**
  - *Cancel:* User thấy hỏng, nhấn Cancel trước khi Stop $\rightarrow$ App drop file nháp, phục hồi về bước ban đầu của step.
  - *Refuse:* Xem Review xong User không ưng ý, bấm Nút "Quay Lại" $\rightarrow$ Thùng file cũ bị vứt, làm lại Step đó.
- **Hậu điều kiện:** File video tốt nhất chờ chuyển đi. Được lưu vào thư viện Camera Roll thiết bị (`Local Storage`).

### UC-EU-05: Chốt Step và Xác thực Bất đồng bộ (Async AI Validation & Bypass)
- **Mô tả:** Người dùng xác nhận lưu bước quay, phụ thuộc vào Config Validation của Admin mà đi thẳng hay đợi Kháng cáo.
- **Tiền điều kiện:** User hài lòng bản quay của Step N, ấn "BƯỚC TIẾP THEO (Next Step)".
- **Luồng Cơ Bản (Validation ON):**
  1. Hệ thống ghi nhận file, lưu cứng vào Cục bộ.
  2. Ngay lập tức UI chuyển sang Step N+1 cho user chuẩn bị làm tiếp.
  3. Ở Background, App tự đẩn file Step N về Server API.
  4. API cho AI soi hình ảnh. Kết quả trả về = "FAIL" (Vì góc máy thiếu ánh sáng).
  5. API call Notification socket về App. App (đang ở màn Step N+1) bắn Snackbar ĐỎ: "Step N Xác thực Thất Bại."
  6. User bấm nút "Không Đồng Ý" (Bypass) trên Thông báo đỏ đó.
  7. Lệnh Bypass bay lên API, giữ lại file, đánh dấu cờ `Flagged`. Cho qua, App báo "Đã dùng Kháng Cáo thành công".
- **Luồng Cơ Bản (Validation OFF):**
  1. Backend không cần chạy Background Verify.
  2. UI chuyển sang Step N+1. Backend tự set `status = Passed`.
- **Hậu điều kiện:** Đi qua được Step.

### UC-EU-06: Đóng gói và Tải lên hệ thống (Submit & Chunked Upload)
- **Mô tả:** Khi đẩy đủ 100% list step, nộp thành quả về kho lưu trữ.
- **Tiền điều kiện:** Hoàn thành toàn bộ các Step (Có thể pass hoặc flagged).
- **Luồng Cơ Bản:**
  1. Nút "SUBMIT TOÀN BỘ" sáng lên.
  2. User nhấn Submit. Tiến trình Background Upload Bar chạy (Chia nhỏ HTTP Chunked để chống rớt mạng).
  3. API lưu `status = Completed`.
  4. Sau khi Upload 100% thành công, Màn hình App hiện một Dialog bự: "Giải phóng bộ nhớ? Data đã được lưu, bạn có muốn xóa file trong Thư viện điện thoại không (Tiết kiệm 1.2GB)?"
  5. User bấm XÓA. App gọi system API wipe các file gốc đó.
- **Hậu điều kiện:** Storage của Admin nhận trọn vẹn thư mục `/userX/date/script/` có đủ video các Step. Màn hình App reset.

### UC-EU-07: Quản lý Video (My Videos & YouTube Stats)
- **Mô tả:** Màn hình Liệt kê tác phẩm đã Upload và xem độ Viral.
- **Tiền điều kiện:** User ở Dashboard, tab "Đã Hoàn Thành".
- **Luồng Cơ Bản:**
  1. Hiển thị thumbnail, ngày up.
  2. User nhấn vào 1 Video.
  3. App fetch dữ liệu view, like, dislike thật tế truyền từ YouTube Data API (do Admin setup linkage đằng sau).
  4. Trình hiện biểu đồ đơn giản.
