# 01-problem-scan.md — Scan & Quick Cards

## Phase 1 — SCAN: Quét cơ hội AI cho Vin Smart Future

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | Xanh SM | Lặp lại | Điều phối viên phải tra cứu, so sánh và xác định trạm sạc VinFast trống cho xe điện bị cạn pin trên đường nhiều lần mỗi ngày. |
| 2 | Xanh SM | Tốn thời gian | Phân tích lý do khách hàng hủy chuyến dựa trên ghi chú tài xế và nhật ký cuộc gọi để tìm pattern vận hành. |
| 3 | VinFast | Lặp lại | So khớp hóa đơn sạc và đối chiếu số liệu trạm sạc với bảng công nợ hàng tuần bằng Excel thủ công. |
| 4 | Vinhomes | AI-upgrade | Phân loại và soạn thảo trả lời tự động cho phản hồi/khiếu nại của cư dân trên app Vinhomes Resident. |
| 5 | Vinmec | Pain từ người khác | Bác sĩ và điều dưỡng mất quá nhiều thời gian viết tóm tắt hồ sơ xuất viện bằng tay cho mỗi bệnh nhân. |

---

## Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

### QUICK PROBLEM CARD #1

Bài toán: Tài xế Xanh SM báo hết pin giữa đường, điều phối viên phải tìm trạm sạc phù hợp và viết chỉ dẫn gửi cho tài xế.

Công ty thành viên: [x] Xanh SM (GSM)

Ai đang đau? Tài xế chờ đợi và Điều phối viên quá tải xử lý tình huống khẩn cấp.

Workflow thủ công hiện tại (5 bước):
1. Tài xế gọi tổng đài báo hết pin.
2. Điều phối viên tra cứu vị trí xe trên bản đồ.
3. Điều phối viên kiểm tra trạm sạc VinFast còn trống và phù hợp với loại xe.
4. Viết tin nhắn chỉ dẫn đường đi chi tiết gửi qua App tài xế.
5. Nếu cần, liên hệ đội cứu hộ pin.

Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (⏱ 12 phút/lượt)

AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 và 4 — tự động tìm trạm phù hợp theo vị trí, kiểm tra trụ sạc trống và xây dựng bản nháp tin nhắn hướng dẫn.

Đo thành công bằng gì (Metric có số)? Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút; đạt 98% độ chính xác trạm sạc phù hợp.

Quick Architecture: [x] LLM

---

### QUICK PROBLEM CARD #2

Bài toán: Nhân viên CSKH Vinhomes phải đọc từng phản hồi/khiếu nại cư dân và tự viết câu trả lời thủ công.

Công ty thành viên: [x] Vinhomes

Ai đang đau? Nhân viên CSKH và cư dân cư trú.

Workflow thủ công hiện tại (4 bước):
1. Cư dân gửi phản hồi/khiếu nại qua ứng dụng.
2. CSKH mở từng phản hồi và xác định loại yêu cầu.
3. Viết câu trả lời phù hợp theo kịch bản.
4. Gửi phản hồi cho cư dân.

Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ 10 phút/vấn đề)

AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3 — phân loại yêu cầu và soạn thảo bản nháp phản hồi theo ngữ cảnh.

Đo thành công bằng gì (Metric có số)? Giảm thời gian xử lý từ 12 giờ xuống còn dưới 2 giờ cho 80% phản hồi định dạng chuẩn.

Quick Architecture: [x] LLM Feature

---

### QUICK PROBLEM CARD #3

Bài toán: Bác sĩ/điều dưỡng Vinmec dành 20-30 phút cho mỗi bệnh nhân để viết tóm tắt hồ sơ xuất viện.

Công ty thành viên: [x] Vinmec

Ai đang đau? Bác sĩ/điều dưỡng và bộ phận tiếp nhận.

Workflow thủ công hiện tại (4 bước):
1. Bác sĩ đọc lại hồ sơ bệnh án.
2. Ghi chép tóm tắt tình trạng, điều trị và chỉ dẫn xuất viện.
3. Đánh máy văn bản vào hệ thống bệnh án.
4. Kiểm tra lại và ký duyệt.

Bước nào tốn thời gian/lỗi nhất? Bước 2 (⏱ 25 phút/bệnh án)

AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 — tổng hợp thông tin bệnh án và tạo bản nháp tóm tắt.

Đo thành công bằng gì (Metric có số)? Giảm thời gian viết tóm tắt từ 25 phút còn dưới 5 phút/bệnh án; chuẩn độ chính xác nội dung > 95%.

Quick Architecture: [x] LLM
