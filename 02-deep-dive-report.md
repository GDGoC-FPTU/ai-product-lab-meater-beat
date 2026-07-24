# 02-deep-dive-report.md — Deep Dive Report

## Nhóm và thành viên
- Nhóm: AI Product Squad
- Thành viên: Nguyễn Văn A (MSSV: 00000000)
- Thành viên: Trần Thị B (MSSV: 00000000)

## 1. Quyết định lựa chọn bài toán
Nhóm quyết định chọn bài toán **"Xanh SM xử lý sự cố hết pin thực địa"** vì đây là một vấn đề vận hành thời thực với tác động trực tiếp đến hiệu suất điều phối và trải nghiệm tài xế. Bài toán có dữ liệu định vị rõ ràng, cần quyết định nhanh và phù hợp với ranh giới AI có thể kiểm soát được.

## 2. Problem Statement (6-field)
| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Điều phối viên Trung tâm Điều vận Xanh SM và tài xế xe điện đang gặp sự cố pin. |
| **2. Current Workflow** | Khi tài xế báo hết pin, điều phối viên tra cứu vị trí xe trên bản đồ, mở dashboard trạm sạc VinFast để tìm trạm còn trống phù hợp với loại xe, viết tin nhắn chỉ dẫn và gửi cho tài xế. Quy trình hoàn toàn thủ công, mất khoảng 15 phút mỗi sự cố. |
| **3. Bottleneck** | Bước tra cứu trạm sạc phù hợp và soạn thảo hướng dẫn chi tiết. Đây là bước mất nhiều thời gian nhất và dễ sai nếu chọn nhầm trạm hoặc loại cổng sạc. |
| **4. Business Impact** | Trung bình 70-90 sự cố pin mỗi ngày ở khu vực Hà Nội gây lãng phí 18-22 giờ công/ngày của đội điều vận, làm tăng thời gian chờ của tài xế và giảm hiệu suất đón khách. |
| **5. Success Metric** | Giảm thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút; đạt độ chính xác trạm sạc phù hợp tối thiểu 98%; tỷ lệ yêu cầu mobile charger chỉ xuất hiện khi pin dưới 5% và trạm xa hơn 5km. |
| **6. Operational Boundary** | AI được phép phân tích vị trí xe và trạng thái trạm sạc, tạo bản nháp hướng dẫn. AI không được tự động gửi tin nhắn cho tài xế, không được đề xuất trạm sạc xa quá 5km khi pin < 5%, và không được tự ý bỏ qua kiểm tra loại cổng sạc. |

## 3. Current-State Workflow
Nhóm đã mô tả quy trình hiện tại như sau:

1. Tài xế gọi tổng đài báo hết pin.
2. Điều phối viên xem vị trí xe và loại xe.
3. Tìm trạm sạc VinFast trống phù hợp.
4. Soạn tin nhắn hướng dẫn đường đi và gửi cho tài xế.
5. Nếu cần, điều phối tiếp đội cứu hộ pin di động.

- Bước 3: Tra cứu trạm sạc phù hợp (⏱ 5 phút, Bottleneck)
- Bước 4: Soạn tin nhắn chỉ dẫn (⏱ 5 phút, Bottleneck)
- Tổng thời gian: khoảng 15 phút/lượt.

## 4. Future-State Flow & AI Fit
**AI Fit:** LLM Feature. Giải pháp ưu tiên một trợ lý LLM để tự động hoá việc phân tích vị trí, lựa chọn trạm phù hợp và tạo bản nháp tin nhắn.

**Quy trình tương lai:**

1. Nhận báo cáo hết pin từ tài xế.
2. Hệ thống tự động lấy tọa độ xe và trạng thái trạm sạc.
3. AI tạo bản nháp chỉ dẫn trạm sạc phù hợp.
4. Điều phối viên kiểm tra và duyệt bản nháp.
5. Gửi tin nhắn đã được phê duyệt cho tài xế.

**Đặc điểm:**
- 🔵 Bước AI: chọn trạm và soạn hướng dẫn.
- 🟢 HITL: điều phối viên duyệt bản nháp trước khi gửi.
- ↩️ Fallback: nếu AI không chắc chắn hoặc vi phạm ranh giới, quay về quy trình thủ công và yêu cầu điều phối viên xử lý bằng tay.

## 5. Evaluate
### AI Readiness Checklist
- [x] Có dữ liệu vị trí xe và trạng thái trạm sạc để kiểm thử.
- [x] Rủi ro khi AI sai được kiểm soát bằng Human-in-the-loop và ranh giới không cho phép gửi tự động.
- [x] Stakeholders vận hành Xanh SM chấp nhận quy trình hỗ trợ AI với bản nháp cần duyệt.

### Quyết định cuối cùng
- [x] **GO** (Bắt đầu xây dựng Prototype)
- [ ] NOT YET
- [ ] NO-GO

**Justification:** Bài toán có ranh giới vận hành rõ ràng, metric cụ thể, và rủi ro có thể kiểm soát được bằng cơ chế human review. Giải pháp giúp tiết kiệm thời gian lớn cho đội điều vận, tăng tốc xử lý sự cố pin và giảm nguy cơ xe bị bỏ dở giữa đường. Nghĩa vụ AI chỉ ở mức tạo bản nháp giúp giảm thiểu sai sót và giữ trách nhiệm phê duyệt trong tay con người.

## 6. Tệp liên quan
- 04-workflow-diagram.png (sơ đồ quy trình hiện tại)
- starter-code/prompt_prototype.py (file prototype prompt)
