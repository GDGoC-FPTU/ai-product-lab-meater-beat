# 01 - Problem Scan (Personal)

Author: Nguyễn Quang Huy - MSSV: 2A202601873

## Scan Table (5 opportunities)
1. EV Emergency Charging Dispatch — Loại: AI-upgrade / Stakeholder Pain
2. VF Service Queue Management — Loại: Lặp lại / Tốn thời gian
3. Parts Inventory Reconciliation — Loại: Tốn thời gian / Lặp lại
4. Customer Appointment Confirmation — Loại: Lặp lại
5. Warranty Claim Triage — Loại: Stakeholder Pain

---

## 3 Quick Problem Cards

### Quick Card 1 — EV Emergency Charging Dispatch
- Tên bài toán: EV Emergency Charging Dispatch (VinFast)
- Actor: Điều phối viên vận hành (Operation Dispatcher)
- Current Workflow:
  1. Khách hàng báo sự cố pin yếu qua app hoặc điện thoại.
  2. Dispatcher nhận vị trí, cố gắng xác định % pin nếu có.
  3. Dispatcher tìm trạm sạc gần nhất thủ công hoặc gọi đội cứu hộ.
  4. Tạo tin nhắn/phiên bản draft cho operator/gửi cho đội cứu hộ.
- Bước tốn thời gian/gây lỗi: Bước 3 — xác định trạm phù hợp và đánh giá an toàn (8–15 phút).
- Bước AI có thể làm: Tự động phân tích vị trí và % pin, quyết định dispatch mobile charger nếu pin < threshold, hoặc gợi ý trạm an toàn trong bán kính cho phép.
- Metric đo thành công: Giảm thời gian phản hồi từ 10 phút xuống < 2 phút; 0% các trường hợp pin <5% được đề xuất trạm >5km.
- Đề xuất kiến trúc sơ bộ: Rule-based threshold (battery <5%) + LLM summarizer để tạo draft.

### Quick Card 2 — Service Queue Prioritization
- Tên bài toán: Service Queue Prioritization
- Actor: Quản lý xưởng dịch vụ
- Current Workflow: Nhập xe -> sắp xếp thủ công -> phân bổ kỹ thuật viên.
- Bước tốn thời gian/gây lỗi: Sắp xếp, cân bằng tải kỹ thuật viên (5–20 phút mỗi ca).
- Bước AI có thể làm: Gợi ý ưu tiên dựa trên lịch sử, SLA, kỹ năng kỹ thuật viên.
- Metric: Giảm thời gian chờ trung bình 15–25%.
- Đề xuất kiến trúc sơ bộ: LLM Feature + Rules.

### Quick Card 3 — Parts Inventory Reconciliation
- Tên bài toán: Parts Inventory Reconciliation
- Actor: Nhân viên kho
- Current Workflow: Đếm vật tư, so khớp ERP, xử lý chênh lệch thủ công.
- Bước tốn thời gian/gây lỗi: So sánh và xác minh chênh lệch (30–120 phút).
- Bước AI có thể làm: Phân tích mẫu chênh lệch, đề xuất nguyên nhân và gợi ý hành động.
- Metric: Giảm sai sót ghi nhận 30%.
- Đề xuất kiến trúc sơ bộ: Rule + ML (anomaly detection).
