# UI Mockups & Visual Wireframes (MVP Version 1)

Tài liệu này cung cấp bản thiết kế UI Mockup và Wireframes cho phiên bản **MVP V1** của hệ thống Content Creator Platform, bao gồm Mobile App (Hybrid Android & iOS) dành cho End-User và Web Portal dành cho Admin. Các thiết kế này tuân thủ các nguyên tắc UX Best Practices đã được phân tích ở tài liệu Gap Analysis.

---

## 1. Mobile App (End-User: Content Creator)
*Style guide: Premium Dark Mode, Glassmorphism, Gradient Accents.*

### 1.1. Màn hình Đăng nhập (Login)
Màn hình đăng nhập dành cho Content Creator (End-User) để truy cập vào hệ thống.

**UI/UX Best Practices áp dụng:**
- **Premium Dark Mode**: Tông màu tối sang trọng, kết hợp gradient cyan/purple.
- **Glassmorphism**: Các khối input và button có hiệu ứng kính mờ (frosted glass) hiện đại.
- **Sleek Typography**: Phông chữ hiện đại, rõ ràng, khơi gợi cảm hứng sáng tạo.

![Mobile App Login](/Users/lyhuynh/GitHub/YTP/images/app/app_login.png)

---

### 1.2. Màn hình Dashboard (Quản lý Kịch bản)
Màn hình chính khi End-User mở App. Nơi đây hiển thị danh sách các Kịch bản (Scripts) do AI tạo ra.

**UI/UX Best Practices áp dụng:**
- **Nút "Cập Nhật (Refresh Trending)"** to bản kèm icon Dropdown chọn quốc gia trực quan.
- **Phân loại Tab**: Kịch bản được chia rõ ràng thành `New` (Mới), `Processing` (Đang quay), `Completed` (Đã hoàn thành).
- Phân tách màu sắc trạng thái rõ ràng, dễ chạm trên thiết bị di động.

![Mobile App Dashboard](/Users/lyhuynh/GitHub/YTP/images/app/app_dashboard.png)

---

### 1.3. Màn hình Camera Guided Creation (Step-by-Step)
Màn hình lõi thao tác quay phim của User. Overlay đè lên luồng Camera thực tế.

**UI/UX Best Practices áp dụng:**
- **Hướng dẫn Text to, rõ ràng** hiển thị ngay mép trên màn hình: (VD: "Step 1: Record product at 45 degree angle for 5s").
- **Thanh Progress Bar**: Báo hiệu tiến độ quay của Step hiện tại so với tổng kịch bản (VD: "Step 1/6").
- **Camera Controls siêu to**: Các nút Record, Pause, Cancel dạng Glassmorphism nổi bật, tránh ấn nhầm khi đang rảnh tay quay.
- Giao diện không che khuất khung hình chính giúp thao tác dễ dàng.

![Guided Camera Recording View](/Users/lyhuynh/GitHub/YTP/images/app/app_camera_step.png)

*(Lưu ý: Sau khi bấm Next Step, app sẽ trigger luồng Upload Asynchronous (Chạy ngầm) chạy nền và báo Pass/Fail bằng Snackbar, không làm khoá màn hình chờ của user).*

---

### 1.4. Màn hình Tải lên & Dọn dẹp (Submit & Upload)
Màn hình xuất hiện sau khi hoàn thành 100% các Step quay phim, quản lý quá trình đẩy file lên server.

**UI/UX Best Practices áp dụng:**
- **Thanh Progress Bar**: Hiển thị tiến trình upload dạng Chunked trực quan.
- **Popup Modal**: Hỏi người dùng có muốn "Xóa Source Local" để giải phóng dung lượng, đặt ở vị trí trung tâm, dễ thao tác.
- **Background**: Vẫn giữ ngôn ngữ thiết kế tối màu gọn gàng của platform.

![Mobile App Submit Upload](/Users/lyhuynh/GitHub/YTP/images/app/app_submit_upload.png)

---

### 1.5. Màn hình Quản lý Video (My Videos & Stats)
Thư viện chứa các video đã quay và đã tải lên, kèm theo thống kê hiệu suất (nếu có).

**UI/UX Best Practices áp dụng:**
- **Card View**: Thumbnails video to, rõ, kèm nhãn trạng thái (New, Uploaded).
- **YouTube Stats**: Tích hợp hiển thị lượt xem, lượt thích, lượt không thích ngay bên dưới video đã upload.
- **Typography mượt mà**: Các chỉ số thiết kế đậm, dễ đọc lướt.

![Mobile App My Videos](/Users/lyhuynh/GitHub/YTP/images/app/app_my_videos.png)

---

## 2. Admin Web Portal (Quản trị hệ thống)
*Style guide: Clean, Professional, Data-dense Dashboard.*

### 2.1. Màn hình Dashboard Thống Kê (Admin Dashboard)
Màn hình tổng quan dành cho Admin theo dõi tính hiệu quả của toàn bộ hệ thống.

**UI/UX Best Practices áp dụng:**
- **Cards Thống Kê**: Overview nhanh số lượt Xem, Thích, Bình luận.
- **Data Table Layout**: Bảng danh sách Kênh YouTube và chỉ số, cột số liệu dễ phân tích.
- **Clean Sidebar**: Điều hướng rõ ràng, tách biệt với nội dung chính.

![Admin Web Portal - Dashboard](/Users/lyhuynh/GitHub/YTP/images/web-portal/web_admin_dashboard.png)

---

### 2.2. Màn hình Quản lý Người Dùng (End-User Management)
Bảng điều khiển để Admin kiểm soát tiến độ và phân quyền Validation cho người dùng dưới app.

**UI/UX Best Practices áp dụng:**
- **Data Table Trực Quan**: Hiển thị rõ danh sách Email, Role, Trạng thái (Active/Pending).
- **Cột cấu hình AI Validation (ON/OFF)**: Admin dễ dàng bật tắt luồng rẽ nhánh Verify Computer Vision với thao tác Toggle 1-click cho mỗi User.
- Layout rộng (Full-width), điều hướng Sidebar trái quen thuộc.

![Admin Web Portal - User Management](/Users/lyhuynh/GitHub/YTP/images/web-portal/web_admin_users.png)

---

### 2.3. Màn hình Cấu hình Storage & API (Settings)
Nơi Admin thiết lập thông số kết nối Server lưu trữ (S3/Drive) và API.

**UI/UX Best Practices áp dụng:**
- **Form rõ ràng**: Các trường Input cho Secret Key, Token, Endpoint phân tách mạch lạc.
- **Nút cấu hình**: Nút "Save Configuration" to, dễ tương tác, đi kèm thông báo lưu thành công.
- **Tối ưu không gian**: Data-dense, hỗ trợ Admin nhập liệu mà không cần cuộn trang nhiều.

![Admin Web Portal - Settings](/Users/lyhuynh/GitHub/YTP/images/web-portal/web_admin_settings.png)

---

## 3. Quy chuẩn Design System 
Đề xuất sử dụng Design System chung cho các UI Component sắp tới của Team Frontend:
- **Font**: Inter hoặc Roboto cho hiển thị thông tin dày đặc trên Web; Outfit hoặc Poppins cho Title/Heading trên Mobile App để mang lại cảm giác Creator/Trending.
- **Colors**: 
  - Dark Mode: Background `#121212`, Surface `#1E1E1E`, Primary Blueprint (Gradient Cyan/Purple).
  - Validation Alerts: `#FF4444` (Fail/Kháng cáo), `#00E676` (Pass).
- **Framework Deploy**: React Native / Flutter cho Mobile Hybrid App và React.js / Next.js (TailwindCSS) cho Web Portal để bám sát UI này nhất.
