# Phase 1-2 — Problem Scan & Quick Problem Cards

**Học viên:** Trung  
**Đơn vị:** Vin Smart Future  
**Bối cảnh chọn:** vận hành xe điện Xanh SM tại Hà Nội

## Phase 1 — SCAN

| # | Công ty thành viên | Lens | Bài toán/bottleneck thực tế |
|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Khi xe báo pin thấp, điều phối viên phải tra GPS, tìm trạm còn chỗ và soạn hướng dẫn thủ công; khoảng 15 phút/sự cố. |
| 2 | VinFast | Lặp lại | Nhân viên tài chính đối chiếu dữ liệu sạc từ nhiều đối tác với hóa đơn hằng tuần; dễ lệch mã phiên sạc và mất nhiều giờ. |
| 3 | Vinhomes | AI có thể tốt hơn | Phản ánh cư dân về nước, điện, thang máy và tiếng ồn đang được phân loại thủ công rồi chuyển cho ban quản lý. |
| 4 | Vinpearl | Pain từ người khác | Quản lý phải đọc review nhiều kênh để phát hiện phàn nàn nghiêm trọng về phòng, vệ sinh hoặc thái độ phục vụ. |
| 5 | Vinmec | Tốn thời gian | Bác sĩ tổng hợp bệnh án, xét nghiệm và thuốc để soạn tóm tắt xuất viện, thường mất 20-30 phút/bệnh nhân. |
| 6 | Xanh SM | Pain từ người khác | Cuộc gọi hủy chuyến và ghi chú của tài xế chứa nhiều lý do khác nhau nhưng chưa được tổng hợp thành nhóm nguyên nhân. |

## Phase 2 — QUICK-ASSESS

### Quick Problem Card #1 — Hỗ trợ sự cố pin Xanh SM

- **Công ty:** Xanh SM
- **Bài toán:** Tài xế báo pin thấp/hết pin; điều phối viên cần tìm phương án sạc an toàn và gửi hướng dẫn.
- **Actor đang đau:** Tài xế phải chờ; dispatcher bị quá tải trong giờ cao điểm.
- **Workflow thủ công:** Nhận cuộc gọi -> tra vị trí GPS -> tra trạm phù hợp/còn chỗ -> viết tin nhắn chỉ đường -> gọi cứu hộ nếu cần.
- **Bottleneck:** Tra trạm và soạn hướng dẫn, khoảng 10 phút trong tổng 15 phút/sự cố.
- **AI hỗ trợ:** Đọc dữ liệu vị trí, lọc trạm theo loại cổng và khoảng cách, draft tin nhắn tiếng Việt.
- **Metric:** Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút; độ chính xác trạm phù hợp >=98%.
- **Kiến trúc:** LLM Feature kết hợp API và Rule/State Machine.

### Quick Problem Card #2 — Phân loại phản ánh cư dân Vinhomes

- **Công ty:** Vinhomes
- **Bài toán:** Phân loại và chuyển phản ánh tự do của cư dân đến đúng bộ phận/tòa nhà.
- **Actor đang đau:** Nhân viên CSKH và cư dân chờ phản hồi.
- **Workflow thủ công:** Nhận tin trên app -> đọc nội dung -> xác định nhóm -> tra tòa/bộ phận -> chuyển ticket và soạn phản hồi.
- **Bottleneck:** Đọc và phân loại các tin viết không theo mẫu, khoảng 5 phút/ticket.
- **AI hỗ trợ:** Trích xuất tòa nhà, loại sự cố, mức khẩn cấp; đề xuất route và draft phản hồi.
- **Metric:** 90% ticket được route đúng trong 30 giây; giảm SLA phản hồi đầu tiên từ 12 giờ xuống 2 giờ.
- **Kiến trúc:** LLM Feature có Rule kiểm tra nhóm nhạy cảm và HITL.

### Quick Problem Card #3 — Tóm tắt review Vinpearl

- **Công ty:** Vinpearl
- **Bài toán:** Gom review đa kênh và phát hiện phàn nàn cần quản lý xử lý sớm.
- **Actor đang đau:** Hotel manager và đội chăm sóc khách hàng.
- **Workflow thủ công:** Mở từng kênh -> sao chép review -> đọc -> gắn nhãn cảm xúc/chủ đề -> lập báo cáo.
- **Bottleneck:** Đọc và gắn nhãn hàng trăm review, khoảng 2 giờ/ngày/cơ sở.
- **AI hỗ trợ:** Chuẩn hóa, tóm tắt, gắn chủ đề và đánh dấu rủi ro; quản lý duyệt trước khi gửi.
- **Metric:** Giảm thời gian tổng hợp từ 120 phút xuống 20 phút/ngày; recall phàn nàn khẩn cấp >=95%.
- **Kiến trúc:** LLM Feature; Rule cho từ khóa an toàn và định tuyến.

## Quyết định chọn

Chọn **Quick Problem Card #1 — Hỗ trợ sự cố pin Xanh SM** để deep-dive vì có quy trình cụ thể, metric đo được và giá trị thời gian thực rõ ràng. Ranh giới an toàn có thể kiểm soát bằng quy tắc khoảng cách, loại cổng sạc và bắt buộc dispatcher duyệt trước khi gửi.
