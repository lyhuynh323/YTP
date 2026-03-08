# Phân tích Gap, Đánh giá (Audit) & UX Best Practices cho MVP V1

Tài liệu này được biên soạn dưới góc nhìn của **Product Manager (PM)** và **Business Analyst (BA)** cấp Global, nhằm rà soát (Audit), đánh giá rủi ro (Gap Analysis) và đề xuất các giải pháp nâng cao Trải nghiệm Người dùng (UX) cho bộ tài liệu thiết kế MVP V1 của dự án AI Digital Content Platform.

---

## 1. Audit Tổng Thể MVP V1 (Đánh giá Hiện Trạng)
Nhìn chung, phạm vi MVP V1 đã được tinh chỉnh rất tốt, tập trung vào giá trị cốt lõi: **Khởi tạo kịch bản Trending tự động $\rightarrow$ Quay phim theo Step có hướng dẫn $\rightarrow$ Xác thực nội dung bằng AI $\rightarrow$ Lưu trữ & Quản lý linh hoạt**.
- **Điểm sáng**: Cơ chế `AI Validation ON/OFF` cho từng User linh hoạt; Cơ chế tự động ghi đè kịch bản thông minh (bỏ qua `Processing/Completed`); Luồng quay phim có Control pause/stop/cancel rất thân thiện.
- **Rủi ro rình rập**: Trải nghiệm gián đoạn do tốc độ mạng (upload video verify); Tràn bộ nhớ điện thoại (Local Storage); User bị "kẹt" do AI nhận diện sai (False Positive).

---

## 2. Phân Tích Gap (Khoảng Trống) & Đề xuất Best Practices

Dưới đây là các khoảng trống trong luồng thiết kế hiện tại và giải pháp khắc phục ngay trong giai đoạn MVP1 hoặc chuẩn bị cho V1.1.

### Gap 1: Nút thắt cổ chai Upload & Verify (Mạng 4G/Wifi yếu)
- **Hiện trạng**: Ở mỗi Step, User phải upload hẳn file video về Backend để AI verify, chờ Passed mới được đi tiếp. Lỡ quay một đoạn dài 30s-1p, thời gian upload và chờ AI xử lý có thể mất 30s-1 phút. Việc bắt User chờ đợi nhìn màn hình loading sau MỖI STEP sẽ giết chết trải nghiệm quay phim (Flow state).
- **UX Best Practice**: 
  - **Asynchronous Verification (Xác thực Không đồng bộ)**: Khi User ấn `Next Step`, thiết kế để file tự động chạy Upload ngầm (Background Upload) về Backend. Trong lúc đó, App vẫn MỞ KHÓA ngay lập tức Step tiếp theo để User đọc hướng dẫn và Set up góc máy. Khi AI verify xong Step trước, bắn một Toast/Snackbar nhỏ màu xanh/đỏ ("Step 1 đã đạt" / "Lỗi Step 1: Thiếu góc quay cận").
  - Nếu Step 1 bị lỗi, hiện icon cảnh báo (Warning) tại danh sách Step, cho phép User quay lại quay đè lên Step 1 bất cứ lúc nào trước khi ấn nút `Submit` cuối cùng.

### Gap 2: Tràn dung lượng lưu trữ cục bộ (Local Storage Overload)
- **Hiện trạng**: App tự động lưu mọi source quay vào Local Device để chống mất dữ liệu. Đối với Content Creator, quay video 4K/1080p nhiều sẽ lập tức làm đầy máy (Full Storage).
- **UX Best Practice**: 
  - Sau khi User thực hiện thành công bước chót: "Bấm nút Submit / Tải lên toàn bộ file về Storage Admin/Drive", ứng dụng nên xuất hiện một Modal (Popup) thân thiện hỏi: *"Quá trình đẩy dữ liệu lên Storage đã hoàn tất 100%. Bạn có muốn xóa các file gốc trên máy để giải phóng [1.5 GB] dung lượng không?"*. 
  - Điều này cho phép user dọn dẹp bộ nhớ ngay lập tức, giữ máy nhẹ nhàng.

### Gap 3: False Positive từ AI Validation (AI chấm sai)
- **Hiện trạng**: AI không phải lúc nào cũng hoàn hảo. Sẽ có trường hợp User làm đúng góc quay, đúng sản phẩm nhưng AI vẫn đánh Fail và bắt quay lại, gây ức chế tột độ. Dù Admin có chức năng Tắt (OFF) Validate, việc báo Admin lúc đang quay ở ngoài đường là bất khả thi.
- **UX Best Practice**: 
  - **Luồng "Bypass/Appeal" (Kháng cáo tạm thời)**: Nếu AI đánh Fail 1 Step quá 2 lần, App tự động hiện nút: *"Bỏ qua cảnh báo & Vẫn sử dụng Source này"*. 
  - Source này sẽ bị gán mác `Flagged`, cho phép User vẫn đi tiếp và Submit thành công. Sau này Admin/Editor khi lấy Source từ Storage sẽ thấy file này và quyết định xem User đúng hay AI đúng. (Sẽ không làm User bị "Block tiến độ" một cách vô lý).

### Gap 4: Chọn Location khi Cập Nhật Kịch Bản Thủ Công
- **Hiện trạng**: Khi User bấm nút cập nhật để lấy 5 script mới, tài liệu ghi "theo Location mới". Nếu để User gõ tay tên quốc gia (Manual Input), sẽ dễ bị lỗi Typo, Backend API (Youtube Trend) có thể không hiểu chữ (Ví dụ gõ "VN", "Viet Nam", "Vietnam").
- **UX Best Practice**: 
  - Thay vì gõ tay, hãy cung cấp một **Dropdown thả xuống (BottomSheet)** với các Location/Region được hỗ trợ cứng từ Backend (Ví dụ: 🇻🇳 Việt Nam, 🇺🇸 United States, 🇯🇵 Japan, 🌐 Global). Có cờ (Flags) đi kèm để tăng tính trực quan.

### Gap 5: Cơ chế Ghi đè chỉ giải quyết Script `New`, bỏ rơi chất lượng Script
- **Hiện trạng**: AI nhả ra 5 Script. App ghi đè các script đang `New`. Nhưng nếu User xem cả 5 Script `New` này và thấy KHÔNG THÍCH cái nào (không phù hợp gu/kênh của họ)? Họ bấm Update Location thì vĩnh viễn AI vẫn có thể quanh quẩn các topic đó.
- **UX Best Practice**: 
  - Ở màn hình Danh sách Kịch bản có 1 nút **"Đổi Kịch Bản Khác" (Regenerate - Dạng icon Xí ngầu/Refresh)** ngay trên từng thẻ Script `New`. Nếu không thích 1 kịch bản cụ thể, họ ấn nút đó, hệ thống chỉ Replace duy nhất Kịch bản đó bằng 1 Topic Trending khác. Rất linh hoạt.

### Gap 6: UI đứt gãy trong quá trình "Submit Cuối Cùng" 
- **Hiện trạng**: Nút Submit đẩy toàn bộ thư mục về Storage. Tùy vào mạng, việc đẩy 5-10 file Video (vài GB) có thể mất hàng chục phút.
- **UX Best Practice**: 
  - Phải có giao diện quản lý **Upload Manager**. Kéo Notification Drawer của điện thoại xuống thấy thanh tiến trình (Progress Bar). 
  - App hỗ trợ Resume Upload: Nếu mạng rớt, App tự động ngưng, có mạng lại tự động bơm tiếp (Chunked Upload). Màn hình Dashboard hiện trạng thái `Uploading (45%)` thay vì chỉ `Completed` ngay lập tức.

---

## 3. Kiến nghị Hành Động (Action Item) cho Team Dev

Từ các phân tích trên, đội ngũ PO, PM và BA Đề Xuất cập nhật (hoặc nốt lại backlog) cho Đội Dev các nghiệp vụ sau khi implement cấu trúc MVP1:
1. **Frontend/App Dev**: Bắt buộc triển khai **Progress Bar / Background Service** cho việc Upload để giữ chân người dùng trong App, không được Lock (khóa) màn hình bắt chờ.
2. **Backend Dev**: Viết API Verify (Check AI) dạng Asynchronous (nhận request -> chạy message queue -> trả kết quả Socket/FCM Notification về App sau) thay vì API REST đồng bộ bắt Request chờ HTTP 1 phút (Timeout HTTP).
3. **Database**: Không cần thêm cột, nhưng State UI của Mobile App cần xử lý tinh tế các cờ (Flagged, Uploading) để UX không bị vấp.

*(Note: Nếu bạn đồng ý với các điểm trên, chúng ta có thể bổ sung nhanh các từ khóa này vào `MVP_V1_Features.md` / `Activity_Diagram` để Fix triệt để.)*
