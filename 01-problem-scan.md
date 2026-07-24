# 01 - Problem Scan (Personal)

Author: Nguyễn Quang Huy - MSSV: 2A202601873

## Scan Table (5 opportunities)
1. VF Service Queue Management — Lặp lại / Tốn thời gian
2. EV Emergency Charging Dispatch — AI-upgrade / Stakeholder Pain
3. Parts Inventory Reconciliation — Tốn thời gian / Lặp lại
4. Customer Appointment Confirmation — Lặp lại
5. Warranty Claim Triage — Stakeholder Pain

## 3 Quick Problem Cards

### Quick Card 1
- Tên bài toán: EV Emergency Charging Dispatch (VinFast)
- Actor: Điều phối viên vận hành (Operator)
- Current Workflow: Khách gọi báo pin yếu → điều phối viên tìm trạm hoặc gửi cứu hộ thủ công
- Bước tốn thời gian: Xác định trạm phù hợp và đánh giá an toàn (8-15 phút)
- Bước AI có thể làm: Tự động quyết định dispatch mobile charger hoặc điều hướng ngắn
- Metric: Giảm thời gian phản hồi từ 10 phút xuống < 2 phút
- Kiến trúc sơ bộ: Agentic (LLM) + Rules (battery threshold)

### Quick Card 2
- Tên bài toán: Service Queue Prioritization
- Actor: Quản lý xưởng dịch vụ
- Current Workflow: Xem danh sách thủ công, ưu tiên bằng kinh nghiệm
- Bước tốn thời gian: Sắp xếp, phân bổ kỹ thuật viên
- Bước AI: Gợi ý ưu tiên dựa trên SLA và độ khó
- Metric: Tăng độ chính xác phân bổ 20%
- Kiến trúc sơ bộ: LLM Feature + Rules

### Quick Card 3
- Tên bài toán: Parts Inventory Reconciliation
- Actor: Nhân viên kho
- Current Workflow: Đếm tay, so khớp sổ sách
- Bước tốn thời gian: Kiểm tra loại trùng và thiếu hụt
- Bước AI: Phân tích mẫu để dự đoán lỗi ghi nhận
- Metric: Giảm sai sót 30%
- Kiến trúc sơ bộ: Rule + ML
