# Giai đoạn 1: Chiến lược & Tầm nhìn Sản phẩm (MVP V1)

## 1. Tài liệu Chiến lược Sản phẩm
**Tầm nhìn:** Xây dựng một sản phẩm khả thi tối thiểu (MVP V1) mạnh mẽ để xác thực luồng end-to-end của việc tự động tạo kịch bản AI, hướng dẫn nhà sáng tạo nội dung qua quy trình quay phim có camera hướng dẫn chi tiết từng bước (step-by-step), và quản lý an toàn nội dung video được tải lên.

**Mục tiêu Chiến lược:** Tập trung hoàn toàn vào luồng phân phối và sáng tạo cốt lõi giữa Admin và End-user (Content Creator). Loại bỏ các tính năng ngoài phạm vi như: tự động hoàn toàn lên lịch (auto-scheduling), tự động chỉnh sửa video, quét bản quyền/pháp lý, và hệ thống thanh toán phức tạp để đảm bảo triển khai nhanh chóng và xác thực vòng lặp "Sáng tạo có Hướng dẫn" (Guided Creation).

## 2. Chỉ số Mục tiêu Cốt lõi (North Star Metric)
**Số lượng Video Gửi Thành Công Hàng Tuần:** Tổng số luồng video vượt qua thành công quy trình Guided Camera, được tải lên hệ thống lưu trữ (Storage), và liên kết chính xác với một kịch bản của end-user mỗi tuần.

## 3. Mục tiêu & Kết quả Then chốt (OKRs)

**Mục tiêu 1: Xác thực luồng tạo kịch bản tự động và hành trình người dùng sáng tạo có hướng dẫn cốt lõi.**
- **Kết quả then chốt 1.1:** 100% End-users mới tự động nhận được 5 kịch bản AI trending ngay trong lần đăng nhập đầu tiên mà không cần can thiệp thủ công.
- **Kết quả then chốt 1.2:** Giảm thiểu ma sát do "Nhận diện sai" (False Positive) của AI bằng cách đảm bảo 100% người dùng có quyền truy cập tính năng "Bỏ qua/Kháng cáo" khi các bước quay phim bị từ chối xác thực.
- **Kết quả then chốt 1.3:** Duy trì luồng tải lên không đồng bộ (asynchronous upload workflow) đảm bảo giao diện người dùng không bao giờ bị đóng băng/chờ quá 1 giây giữa các bước quay.

**Mục tiêu 2: Cung cấp quyền kiểm soát linh hoạt nhưng mạnh mẽ cho Admin.**
- **Kết quả then chốt 2.1:** Cho phép Admin bật/tắt (ON/OFF) yêu cầu "Xác thực AI" (AI Validation) riêng biệt cho 100% người dùng cuối (end-users) đã đăng ký.
- **Kết quả then chốt 2.2:** Đảm bảo 100% video tải lên được định tuyến chính xác vào cấu trúc thư mục quy định (`/{user_id}/{YYYYMMDD}/{script_name}`) trên Storage/NAS đã được cấu hình bằng thông tin API bảo mật.

## 4. Danh sách Tính năng Cấp cao (High-Level Features)

### 4.1. Mobile App (Người dùng cuối / End-user)
1. **Tạo Kịch bản Thông minh (Intelligent Script Generation):**
   - Tự động kích hoạt ở lần đăng nhập đầu tiên (tạo 5 kịch bản).
   - Cập nhật thủ công với Dropdown chọn quốc gia/khu vực.
   - Làm mới từng kịch bản đơn lẻ (Regenerate / Reshuffle).
   - Ghi đè thông minh (chỉ thay thế kịch bản "Mới", giữ nguyên kịch bản "Đang xử lý/Hoàn thành").
2. **Quay phim có Hướng dẫn (Guided Camera Creation):**
   - Giao diện quay phim từng bước (step-by-step) chèn lên luồng camera thực tế.
   - Nút điều khiển camera thân thiện: Quay, Tạm dừng, Dừng hẳn, Hủy.
   - Xác thực AI từng bước không đồng bộ (Upload ngầm trong background, lập tức mở khóa bước tiếp theo).
   - Cơ chế Kháng cáo/Bỏ qua (Bypass) cho các xác thực thất bại (lưu file với gắn cờ "Flagged").
3. **Quản lý Tải lên & Lưu trữ (Upload & Storage Management):**
   - Trình quản lý tải lên chia nhỏ (Chunked upload) có khả năng tạm dừng/tiếp tục và thanh tiến trình (progress bar).
   - Tự động lưu trữ vào bộ nhớ cục bộ (Local Device Storage) của thiết bị trong quá trình quay.
   - "Popup Dọn dẹp" (Cleanup Popup) sau khi upload thành công 100% để hướng dẫn người dùng giải phóng dung lượng máy.
4. **Video của Tôi & Thống kê (My Videos & Stats):** Theo dõi trạng thái video (Mới, Đã upload) và các chỉ số YouTube cơ bản (Lượt xem, Thích, Không thích).

### 4.2. Admin Portal (Web Dashboard)
1. **Quản lý Người dùng (User Management):** Thêm/Sửa người dùng và cho phép bật/tắt rõ ràng bước "Xác thực AI" (AI Validation) cho từng user.
2. **Cấu hình Storage & API:** Quản lý bảo mật khóa API, Access Tokens, và endpoint cho các nền tảng AWS S3/Google Drive/NAS.
3. **Theo dõi Kênh & Video YouTube:** Quản lý liên kết kênh và xem các dữ liệu thống kê cơ bản được tích hợp từ YouTube Data API.
