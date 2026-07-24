# Phase 3 & 5 — Deep-Dive Report

## Thông tin nhóm

- **Tên nhóm:** Vin Smart Future — Xanh SM Dispatch
- **Thành viên:** Trung (MSSV: chưa được cung cấp)
- **Bài toán chọn:** Draft hướng dẫn xử lý sự cố pin cho tài xế Xanh SM

## 3.1 Current-State Workflow

```text
[1. Tài xế gọi báo sự cố] --handoff--> [2. Dispatcher tra GPS]
          2 phút                         2 phút
                                             |
                                             v
                         🔴 [3. Tra trạm còn chỗ/phù hợp]
                              5 phút --handoff--
                                             |
                                             v
                         🔴 [4. Viết SMS chỉ đường]
                              5 phút --handoff--
                                             |
                                             v
                         [5. Gọi cứu hộ nếu pin quá thấp]
                              1 phút

Tổng: 15 phút/sự cố. 🔴 là bottleneck; mỗi mũi tên là handoff
giữa tài xế, dispatcher và các dashboard vận hành.
```

## 3.2 Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **Actor / Operator** | Dispatcher tại Trung tâm Điều vận Xanh SM; tài xế là người cung cấp vị trí, dòng xe và pin. |
| **Current Workflow** | Dispatcher nhận cuộc gọi, tra GPS, mở dashboard trạm, lọc trạm theo loại cổng/còn chỗ, viết hướng dẫn và gọi cứu hộ khi cần. Quy trình thủ công mất khoảng 15 phút/lượt. |
| **Bottleneck** | Tra cứu trạm và soạn tin nhắn (10 phút); dữ liệu nằm ở nhiều màn hình, dễ chọn nhầm trạm hoặc bỏ sót tình trạng pin. |
| **Business Impact** | Với khoảng 80 sự cố/ngày, quy trình tiêu tốn khoảng 20 giờ dispatcher/ngày, kéo dài thời gian xe không hoạt động và làm tăng nguy cơ hủy chuyến. Các con số là baseline giả định cần đo lại trong pilot. |
| **Success Metric** | Thời gian xử lý trung vị dưới 3 phút/sự cố; >=98% đề xuất đúng loại cổng và trạm; 100% tin nhắn gửi đi có dispatcher duyệt; không có đề xuất trạm >5 km khi pin <5%. |
| **Operational Boundary** | AI chỉ đọc API GPS/trạm, lọc theo rule, tạo draft. AI không được tự gửi tin, tự điều xe cứu hộ, bịa dữ liệu trạm hoặc bỏ qua cảnh báo. Pin <5% và trạm gần nhất >5 km phải tạo yêu cầu `dispatch_mobile_charger`; dispatcher luôn duyệt. Khi thiếu dữ liệu hoặc confidence thấp, chuyển xử lý thủ công. |

## 3.3 Future-State Flow & AI Fit

**AI Fit:** LLM Feature cho ngôn ngữ/draft, kết hợp Rule/State Machine cho ngưỡng pin, khoảng cách, loại cổng và trạng thái phê duyệt. Agent tự trị không phù hợp vì hành động sai có thể làm xe cạn pin.

```text
[Nhận cuộc gọi + nhập biển số/pin]
              |
              v
[Rule: kiểm tra dữ liệu, pin, loại cổng] --thiếu dữ liệu--> [↩️ Fallback thủ công]
              |
              v
[🔵 API lấy GPS và trạm đang hoạt động]
              |
              +-- pin <5% và không có trạm <=5 km --> [🔵 Draft dispatch_mobile_charger]
              |
              +-- có trạm an toàn ------------------> [🔵 LLM draft hướng dẫn]
                                                              |
                                                              v
                                      [🟢 Dispatcher review/edit/approve]
                                             |                 |
                                             v                 v
                                      [Gửi tin]       [↩️ Fallback: gọi cứu hộ,
                                                       tra dashboard thủ công]
```

AI không được biến draft thành hành động gửi. Mọi output đều phải có `[DRAFT_ONLY]` ở đầu.

## 3.4 Phase 5 — Evaluate

| Câu hỏi readiness | Đánh giá | Bằng chứng/việc cần làm |
|---|---|---|
| Có dữ liệu mẫu/log sạch? | Một phần | Có log sự cố, GPS, pin và catalog cổng; cần 2 tuần làm sạch và 200 ca gắn nhãn. |
| Rủi ro sai có kiểm soát? | Có | Rule khoảng cách/pin, HITL bắt buộc, không tự gửi và fallback dispatcher. |
| Stakeholder sẵn sàng? | Có điều kiện | Dispatcher tham gia pilot, UI phải cho sửa draft và ghi audit log. |
| Chi phí kỹ thuật | Chấp nhận được | MVP dùng API nội bộ hiện có, một service Python và Gemini Flash; chi phí API cần benchmark theo số token, chưa chốt khi chưa có volume. |
| Kế hoạch kiểm thử | Đủ cho pilot | Replay log 200 ca, kiểm tra boundary pin <5%, trạm sai cổng, thiếu GPS, prompt injection và timeout API. |

### Quyết định: GO (scope hẹp)

Bắt đầu prototype ở một trung tâm điều vận và chỉ cho phép tạo draft. Đây là lựa chọn có giá trị đo được, có dữ liệu khả dụng và rủi ro được chặn bởi rule/HITL. Chưa cho phép tự động gửi hoặc tự động điều xe. Sau pilot, chỉ mở rộng khi đạt các ngưỡng metric ở trên trong hai tuần liên tiếp và không có incident an toàn.
