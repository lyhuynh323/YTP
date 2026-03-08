# UI Mockups & Visual Wireframes (MVP Version 1)

Tài liệu này cung cấp bản thiết kế UI Mockup và Wireframes cho phiên bản **MVP V1** của hệ thống Content Creator Platform, bao gồm Mobile App (Hybrid Android & iOS) dành cho End-User và Web Portal dành cho Admin. Các thiết kế này tuân thủ các nguyên tắc UX Best Practices đã được phân tích ở tài liệu Gap Analysis.

---

## 1. Mobile App (End-User: Content Creator)
*Style guide: Premium Dark Mode, Glassmorphism, Gradient Accents.*

### 1.1. Màn hình Dashboard (Quản lý Kịch bản)
Màn hình chính khi End-User mở App. Nơi đây hiển thị danh sách các Kịch bản (Scripts) do AI tạo ra.

**UI/UX Best Practices áp dụng:**
- **Nút "Cập Nhật (Refresh Trending)"** to bản kèm icon Dropdown chọn quốc gia trực quan.
- **Phân loại Tab**: Kịch bản được chia rõ ràng thành `New` (Mới), `Processing` (Đang quay), `Completed` (Đã hoàn thành).
- Phân tách màu sắc trạng thái rõ ràng, dễ chạm trên thiết bị di động.

![Mobile App Dashboard](/Users/lyhuynh/GitHub/YTP/images/app/app_dashboard.png)

---

### 1.2. Màn hình Camera Guided Creation (Step-by-Step)
Màn hình lõi thao tác quay phim của User. Overlay đè lên luồng Camera thực tế.

**UI/UX Best Practices áp dụng:**
- **Hướng dẫn Text to, rõ ràng** hiển thị ngay mép trên màn hình: (VD: "Step 1: Record product at 45 degree angle for 5s").
- **Thanh Progress Bar**: Báo hiệu tiến độ quay của Step hiện tại so với tổng kịch bản (VD: "Step 1/6").
- **Camera Controls siêu to**: Các nút Record, Pause, Cancel dạng Glassmorphism nổi bật, tránh ấn nhầm khi đang rảnh tay quay.
- Giao diện không che khuất khung hình chính giúp thao tác dễ dàng.

![Guided Camera Recording View](/Users/lyhuynh/GitHub/YTP/images/app/app_camera_step.png)

*(Lưu ý: Sau khi bấm Next Step, app sẽ trigger luồng Upload Asynchronous (Chạy ngầm) chạy nền và báo Pass/Fail bằng Snackbar, không làm khoá màn hình chờ của user).*

---

## 2. Admin Web Portal (Quản trị hệ thống)
*Style guide: Clean, Professional, Data-dense Dashboard.*

### 2.1. Màn hình Quản lý Người Dùng (End-User Management)
Bảng điều khiển để Admin kiểm soát tiến độ và phân quyền Validation cho người dùng dưới app.

**UI/UX Best Practices áp dụng:**
- **Data Table Trực Quan**: Hiển thị rõ danh sách Email, Role, Trạng thái (Active/Pending).
- **Cột cấu hình AI Validation (ON/OFF)**: Admin dễ dàng bật tắt luồng rẽ nhánh Verify Computer Vision với thao tác Toggle 1-click cho mỗi User.
- Layout rộng (Full-width), điều hướng Sidebar trái quen thuộc.

![Admin Web Portal - User Management](/Users/lyhuynh/GitHub/YTP/images/web-portal/web_admin_users.png)

---

## 3. Quy chuẩn Design System 
Đề xuất sử dụng Design System chung cho các UI Component sắp tới của Team Frontend:
- **Font**: Inter hoặc Roboto cho hiển thị thông tin dày đặc trên Web; Outfit hoặc Poppins cho Title/Heading trên Mobile App để mang lại cảm giác Creator/Trending.
- **Colors**: 
  - Dark Mode: Background `#121212`, Surface `#1E1E1E`, Primary Blueprint (Gradient Cyan/Purple).
  - Validation Alerts: `#FF4444` (Fail/Kháng cáo), `#00E676` (Pass).
- **Framework Deploy**: React Native / Flutter cho Mobile Hybrid App và React.js / Next.js (TailwindCSS) cho Web Portal để bám sát UI này nhất.
