# Trực quan hóa Giao diện Người dùng (Interactive UI Visuals) - MVP V1

Tài liệu này cung cấp các hình ảnh trực quan (Visual Mockups) minh họa các luồng thao tác (flows) và ca sử dụng (use cases) bám sát thực tế nhất của End-User và Admin trong hệ thống ShareWind MVP V1.

*(Được render dựa trên ngôn ngữ thiết kế: Premium Dark Mode, Glassmorphism)*

## 1. Hành trình Người Dùng Cuối (End-User App Flow)

Hành trình từ lúc người dùng đăng nhập, nhận kịch bản, thực hiện quay phim có hướng dẫn, dính lỗi thẩm định AI và quy trình upload cuối cùng.

### Bước 1: Đăng nhập (UC-EU-01)
Màn hình đầu tiên người dùng tiếp xúc, yêu cầu sự tinh gọn và khơi gợi cảm hứng sáng tạo.
![Login Screen](/Users/lyhuynh/.gemini/antigravity/brain/24ae1efb-53d2-4ae3-af5b-25f918316a8e/app_login_screen.png)

### Bước 2: Quản lý Kịch bản Trending (UC-EU-02 & UC-EU-03)
Khi đăng nhập xong, App hiển thị danh sách các Kịch bản. User có thể gạt Location để Update toàn bộ, hoặc bấm icon cục xí ngầu để Reshuffle 1 thẻ `New` bất kỳ.
![Dashboard Scripts](/Users/lyhuynh/.gemini/antigravity/brain/24ae1efb-53d2-4ae3-af5b-25f918316a8e/app_dashboard_scripts.png)

### Bước 3: Thao tác Máy Quay Đọc Lệnh (UC-EU-04)
Khi chọn 1 thẻ, Ứng dụng chèn Text hướng dẫn của Step 1 lên Viewfinder. Nút Record màu đỏ nổi bật ở giữa, dễ bấm không cần nhìn.
![Camera Guided Step](/Users/lyhuynh/.gemini/antigravity/brain/24ae1efb-53d2-4ae3-af5b-25f918316a8e/app_camera_guided_step.png)

### Bước 4: Xử lý Xác thực AI Thất bại (UC-EU-05)
Trong lúc User đã sang Step tiếp theo, Background Upload báo Step trước đó AI chấm Rớt (Cảnh bị tối). Snackbar đỏ hiện lên ngay lập tức có đính kèm nút Kháng Cáo (Bypass) để User chủ động quyết định bỏ qua hay quay lại.
![AI Validation Warning](/Users/lyhuynh/.gemini/antigravity/brain/24ae1efb-53d2-4ae3-af5b-25f918316a8e/app_validation_warning.png)

### Bước 5: Upload Thành công & Dọn Dẹp (UC-EU-06)
Hoàn thành 100% kịch bản. App tiến hành Chunked Upload có Progress Ring. Vừa upload xong Server báo Ok, App nhảy Popup nhắc nhở Xóa local storage cứu bộ nhớ ngay.
![Upload Manager & Cleanup](/Users/lyhuynh/.gemini/antigravity/brain/24ae1efb-53d2-4ae3-af5b-25f918316a8e/app_upload_manager.png)

---

## 2. Hành trình Quản trị Viên (Admin Portal Flow)

Giao diện Web tối ưu hiển thị danh sách dài (Data-dense), tập trung thao tác 1-click (Ví dụ: Toggle AI Validation).

### Màn hình 1: Dashboard Thống kê Chung (UC-AD-03)
Overview số lượng Người dùng, Kênh YouTube và View/Like aggregate.
![Admin Dashboard](/Users/lyhuynh/.gemini/antigravity/brain/24ae1efb-53d2-4ae3-af5b-25f918316a8e/web_admin_dashboard.png)

### Màn hình 2: Quản lý End-User (UC-AD-01)
Nơi Admin thiết lập tạo tài khoản Creator và quan trọng nhất: Cót Tắt/Bật tính năng Khắt Khe `AI Validation` cho luồng quay phim của họ thông qua 1 nút Toggle gạt.
![Admin User Management](/Users/lyhuynh/.gemini/antigravity/brain/24ae1efb-53d2-4ae3-af5b-25f918316a8e/web_admin_user_management.png)
