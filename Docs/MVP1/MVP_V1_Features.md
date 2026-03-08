# MVP Version 1 (V1) Features & Workflows

Dựa trên yêu cầu tối giản hóa của Product Owner (PO), Project Manager (PM), và Business Analyst (BA), dưới đây là danh sách các chức năng Tối thiểu (Minimum Viable Product - MVP) ở Version 1, tập trung cốt lõi vào luồng phân phối kịch bản từ Admin đến End-user.

## 1. Mục tiêu của MVP (V1)
Xây dựng một khung hệ thống cơ bản để xác thực luồng cung cấp kịch bản AI cho End-user qua Mobile App, đồng thời cho phép Admin quản lý người dùng và theo dõi chỉ số tối thiểu của kênh YouTube.

## 2. Luồng Vận Hành (Operational Workflow)
1. **Tạo End-user (Admin)**: Admin tạo tài khoản cho End-user sử dụng Mobile App.
2. **Build App & Tải lên Storage**: Quản trị viên (hoặc hệ thống CI/CD) build Mobile App (APK/IPA) và upload file cài đặt hoặc resource liên quan lên Storage/NAS dùng chung.
3. **Trigger AI (End-User/Auto)**: 
   - Khi End-user đăng nhập lần đầu (nếu chưa có script nào), hệ thống tự động gọi AI nhận diện trending YouTube và tạo ra 5 kịch bản (Scripts) cung cấp ngay trên app.
   - Nếu User đã có script, User tự bấm nút cập nhật thủ công để lấy script theo location mới.
   *(Ghi chú: Admin không còn thực hiện thao tác Trigger AI/Gửi kịch bản).*
4. **Push Notification**: Hệ thống đẩy thông báo (Push Notification) thông qua các event kịch bản mới hoặc notification nhắc nhở.
5. **End-user App**: End-user mở Mobile App, nhận thông báo, xem kịch bản.
6. **Dashboard Management**: Admin quản lý User, danh sách Kênh, Video và xem thống kê tối thiểu cho từng kênh, video trên YouTube.

...
## 3. Chi tiết Features MVP V1

### 3.1. Admin Portal (Web - Dashboard)
- **Quản lý User**: 
  - Thêm, sửa, xóa, khóa (Deactivate) End-User.
  - Reset mật khẩu cho User.
  - **Cấu hình AI Validation (Mới)**: Cho phép cấu hình Bật (ON) / Tắt (OFF) quá trình AI verify hình ảnh/video riêng biệt cho từng End-user.
- **Quản lý Cấu hình Storage & API**:
  - Giao diện Admin để cấu hình các endpoint kết nối đến Storage, NAS hoặc Cloud (Google Drive, AWS S3).
  - Khai báo và lưu trữ các tham số cấu hình API keys, Access Tokens dùng để đẩy file.
- **Quản lý Kênh YouTube**: 
  - Thêm danh sách các kênh (Chỉ lưu Link hoặc ID kênh).
  - Gắn kênh với một User hoặc Team cụ thể.
- **Quản lý Danh sách Video**: 
  - Bảng danh sách các video đã publish hoặc quản lý nội bộ.
  - Lưu link Storage/NAS của video.
- **Statistic Dashboard (Tối thiểu)**:
  - Hiển thị Views, Likes, Comments cơ bản lấy từ YouTube Data API v3 cho từng Kênh / từng Video được track.

### 3.2. Mobile App (End-User)
- **Đăng nhập**: User sử dụng tài khoản do Admin cấp (Email/Pass).
- **Xem & Khởi tạo Kịch bản, Trend**: 
  - Lịch sử nhận Push Notifications.
  - **Khởi tạo tự động (First Login Auto-Trigger)**: Nếu hệ thống nhận diện việc User đăng nhập mà chưa có bất kỳ kịch bản nào, Backend sẽ tự động gọi API lấy trending YouTube, tạo 5 kịch bản và cung cấp quyền truy cập ngay trên màn hình này.
  - **Làm mới thủ công (Manual Cập nhật)**: Khi User đã có script, app cung cấp nút làm mới/cập nhật thủ công để lấy trending và tạo 5 kịch bản theo Location mới (**sử dụng UI Dropdown chọn quốc gia** thay vì gõ tay để tránh lỗi typo).
  - **Cơ chế Ghi đè (Overwrite)**: Khi tải về kịch bản mới, hệ thống sẽ tự động ghi đè lên các kịch bản đang có trạng thái là `New`.
  - **Làm mới Kịch bản Đơn lẻ (Regenerate)**: Bổ sung nút "Đổi Kịch Bản Khác" (Reshuffle) trên từng thẻ Script `New`. Cho phép AI replace duy nhất kịch bản đó bằng Topic khác nếu User không thích.
- **Quay phim & Chụp ảnh theo Kịch bản (Guided Creation)**:
  - User chọn 1 kịch bản từ danh sách.
  - Ứng dụng hiển thị UI chia kịch bản thành từng Step nhỏ.
  - Trong mỗi Step, tự động mở Camera kèm **yêu cầu, hướng dẫn chụp/quay, và góc quay best practice**, giúp user dễ dàng quay đúng định dạng để sau đó dựng thành video YouTube.
  - **Thao tác linh hoạt (Camera Controls)**: Trong quá trình thao tác ở mỗi step, user có thể bấm:
    - *Tạm dừng (Pause)* quay.
    - *Dừng hẳn (Stop)* để review đoạn vừa quay.
    - *Hủy (Cancel)* để xóa bỏ đoạn vừa rồi và quay/chụp lại từ đầu nếu chưa hài lòng.
  - **Lưu trữ cục bộ (Local Storage)**: Ở các bước quay/chụp này, sau khi user ok lưu tạm, tất cả hình ảnh và video source sẽ được tự động lưu vào bộ nhớ thiết bị nội bộ (Local Device) của end-user để phòng ngừa mất dữ liệu.
  - **Verify Từng Bước (Step-by-Step Validation)**: 
    - Dựa theo cấu hình Admin set cho User (ON/OFF Validate):
    - **Nếu OFF (Không Validate)**: App tự động chốt và cho qua Step kế tiếp mà không cần verify.
    - **Nếu ON (Có Validate)**: 
      - App kích hoạt **Upload Asynchronous (Chạy ngầm)** file video ngắn về Backend API và **lập tức Mở khóa (Unlock)** cho phép User sang Step kế tiếp thực hiện setup, không bắt User phải nhìn màn hình loading chờ đợi.
      - Hệ thống (AI Check) sẽ tiến hành verify ngầm. Khi có kết quả:
        - Nếu *đã đúng*: Trả về Snackbar báo "Pass".
        - Nếu *chưa đúng*: Có popup thông báo lỗi ở Step trước. Cấp tuỳ chọn **Luồng Bypass (Kháng cáo)**: Nếu AI đánh Fail, cho phép User "Bỏ qua & Vẫn dùng Source này" (Đánh cờ *Flagged*) để tránh kẹt tiến độ vô lý rủi ro False Positive từ mô hình Computer Vision.
  - **Submit & Upload**: Sau khi hoàn tất toàn bộ Step, hệ thống xuất hiện nút "Submit". Dữ liệu được đẩy về Storage (Admin config hoặc User drive).
    - **Upload Manager**: Hỗ trợ Chunked upload, có thành tiến trình (Progress Bar), hỗ trợ Tạm dừng/Bơm tiếp khi rớt mạng để ứng phó với hàng Giga file gốc trơn tru.
    - **Dọn dẹp Local (Cleanup Popup)**: Sau khi Upload 100% thành công, ứng dụng bật Popup thân thiện yêu cầu xóa các file Video/Ảnh RAW trên thiết bị để giúp User **giải phóng dung lượng máy**.
- **Quản lý Video của User (My Videos & Stats)**:
  - User quản lý danh sách các nội dung đã sản xuất.
  - **Trạng thái video**: "Mới" (chưa upload), "Đã Upload".
  - **Xem chi tiết**: Bấm vào một video đã upload (nếu được link với YouTube bởi hệ thống), hiển thị các chỉ số chi tiết: **Lượt xem (Views), Lượt thích (Likes), Lượt không thích (Dislikes)**.
- **Nhận Notification**: Tích hợp FCM hoặc APNs để popup thông báo ngay cả khi app đang đóng.

### 3.3. Hệ thống Background / Storage
- **Storage/NAS Integration**: Nơi lưu file Build App (APK/IPA) hoặc file nội bộ, Admin có thể quản trị đường dẫn và End-user có thể download bản update từ link này (hoặc upload video sau này).
- **Cấu trúc lưu trữ nội dung Upload**: Việc upload nội dung từ Mobile App (do system server hoặc NAS/Drive do Admin setup) sẽ tự động tạo cấu trúc thư mục khoa học, có format cụ thể:
  - `/{Tên hoặc ID User}/{YYYYMMDD}/{Tên Kịch Bản}` 
  - *Ví dụ:* `/lyhuynh_123/20260308/review_iphone_15_pro/video1.mp4`
- **Media Processing / Upload API**: Backend hỗ trợ API nhận file ảnh/video, tạo thư mục động theo format trên, và lưu metadata vào CSDL.

## 4. Các thành phần loại bỏ ở Phase 1 (Out of Scope for MVP V1)
Để hệ thống gọn nhất có thể, các chức năng **không có trong bản MVP**:
- ❌ **Tự động hóa hoàn toàn (Auto-Scheduling)**: AI không tự động chạy, Admin mới là người Trigger định kỳ.
- ❌ **Auto-Edit Video & Timeline JSON**: Tạm gác luồng AI tự động edit video với Timeline JSON. Editor sẽ tự dùng kịch bản để tự edit bằng tay.
- ❌ **AI QC / Legal Scan**: Không quét bản quyền/khuôn mặt bằng Computer Vision.
- ❌ **OAuth YouTube rườm rà**: Việc link tự động OAuth để upload sẽ có, nhưng User không tự Auth, Admin dùng API key/Tokens tổng hoặc User tự up bằng tay.
- ❌ **Quản lý thanh toán & Doanh thu chi tiết**: Không tự chia tiền, không kết nối e-wallet (Momo/Paypal).
- ❌ **Hệ sinh thái Chủ Quán (Camera)**: Tạm gác tính năng Camera Partner.
