# 02 - Deep Dive Report (Group)

Group Name: meater beat
Members:
- Nguyễn Quang Huy - MSSV: 2A202601873

## Selected Problem
EV Emergency Charging Dispatch

## Problem Statement (6-field)
1) Actor
- Điều phối viên vận hành (Dispatcher); secondary actor: Khách hàng / lái xe EV.

2) Current Workflow
- Khách hàng báo sự cố pin yếu qua app hoặc điện thoại.
- Dispatcher nhận vị trí, cố gắng xác định % pin nếu có.
- Dispatcher tìm trạm sạc gần nhất thủ công hoặc gọi đội cứu hộ.
- Tạo tin nhắn/phiên bản draft cho operator/gửi cho đội cứu hộ.

3) Bottleneck
- Thời gian xác định trạm an toàn và quyết định (có thể >8 phút).
- Rủi ro: nếu pin cực thấp, điều hướng tới trạm xa có thể gây nguy hiểm.
- Thông tin không đầy đủ (không có % pin, traffic, tình trạng trạm) gây chậm.

4) Business Impact
- Mất thời gian phản hồi ảnh hưởng trải nghiệm khách hàng, gây tổn thất thương hiệu và rủi ro an toàn.
- Chi phí cứu hộ tăng nếu phản ứng chậm dẫn tới nằm đường hoặc thiệt hại.

5) Success Metric (số cụ thể)
- Thời gian trung bình từ báo cáo tới hành động (dispatch hoặc gợi ý) giảm từ 10 phút xuống < 2 phút.
- 0% các trường hợp pin <5% được đề xuất trạm >5km.
- Tỉ lệ lỗi do đề xuất sai giảm ≥ 90%.

6) Operational Boundary
- Nếu battery < 5%: KHÔNG gợi ý trạm xa >5km; phải dispatch mobile charger.
- Tất cả tin nhắn tự động phải bắt đầu bằng [DRAFT_ONLY] và cần phê duyệt con người trước khi gửi.

## Future-State Flow & AI Fit
- Future flow: Automated triage -> If battery <5% -> Auto-draft dispatch action + human approval -> Dispatch mobile charger
- Else -> Find nearest safe station within 5km -> Auto-draft message for operator (with ETA, distance, station status)

AI Fit:
- Rule: battery threshold (deterministic safety rule)
- LLM Feature: generate draft messages for operator
- Agentic Loop: call dispatch API to create order (requires human approval)
- Human-in-the-loop: final approval before sending any outbound messages or initiating dispatch
- Fallback: If system lacks station data or connectivity, fall back to operator manual flow and display clear warning.

## Evaluate (Checklist + Decision)
Checklist for readiness:
- [x] Data: location + (optionally) battery % available from user input
- [x] Dispatch API available for mobile charger
- [x] Operator UI accepts draft messages and approve/abort actions
- [x] Monitoring and logging in place for safety audits

Decision: GO (with human-in-the-loop)
Rationale: Kỹ thuật khả thi; rule-based safety (battery threshold) cho phép bắt đầu tích hợp; LLM chỉ dùng để viết lời nháp, không quyết định hành động quan trọng.

## Implementation Plan (High level)
1. Build rule-engine module: implement battery thresholds and distance constraints.
2. Implement station lookup service (cache station metadata and status).
3. Integrate dispatch API for mobile chargers (sandbox + production).
4. Implement operator UI to present [DRAFT_ONLY] messages and approval buttons.
5. Add logging, alerting, and safety audits.
6. Run staged field tests and adversarial tests (prompt_prototype.py harness).

## Risks & Mitigations
- Risk: Missing or inaccurate telemetry -> Mitigation: require manual confirmation or conservative default to dispatch.
- Risk: False positives/negatives in distance calculation -> Mitigation: include buffer and explicit human review.
