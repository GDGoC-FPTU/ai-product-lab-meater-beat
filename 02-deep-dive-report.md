# Phase 3 & Phase 5: Deep-Dive Report & Evaluation

## 👥 THÀNH VIÊN NHÓM
* **Tên nhóm:** Vin Smart Future - Squad A
* **Thành viên:**
  1. Nguyễn Văn A (MSSV: 20231234)
  2. Trần Thị B (MSSV: 20235678)
  3. Lê Hoàng C (MSSV: 20239012)

---

## 🗳️ QUYẾT ĐỊNH LỰA CHỌN BÀI TOÁN
Nhóm quyết định chọn bài toán: **Xanh SM Xử lý sự cố sạc pin thực địa (Quick Problem Card #1)** làm trọng tâm để thực hiện Deep-Dive.

### Lý do lựa chọn và loại bỏ các đề xuất khác:
* **Lý do chọn:** Sự cố hết pin/pin yếu thực địa ảnh hưởng trực tiếp đến SLA (Service Level Agreement) và doanh thu thời gian thực của GSM. Việc giảm thời gian chờ đợi của tài xế giúp tăng tỷ lệ sẵn sàng đón khách và trực tiếp nâng cao doanh số.
* **Lý do loại bỏ Vinhomes CSKH:** Rủi ro pháp lý liên quan đến tranh chấp căn hộ và thông tin tài chính của cư dân là rất cao, đòi hỏi hệ thống kiểm soát phức tạp hơn (Rule-based kết hợp RAG).
* **Lý do loại bỏ Vinmec Discharge Summary:** Môi trường y khoa đòi hỏi tính chính xác tuyệt đối (100% không hallucination), quy trình phê duyệt của bác sĩ rất nghiêm ngặt và cần kiểm định y tế lâm sàng lâu dài trước khi tích hợp AI.

---

## 🏗️ Phase 3 — DEEP-DIVE

### 3.1. Problem Statement (6-field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) tại Trung tâm Điều vận Xanh SM. |
| **2. Current Workflow** | Khi tài xế gọi điện/gửi yêu cầu khẩn cấp báo pin yếu, Điều phối viên tra cứu thủ công vị trí GPS của xe trên bản đồ, mở dashboard quản lý trạm sạc VinFast để tìm trụ sạc tương thích còn trống trong phạm vi di chuyển an toàn, soạn thảo thủ công nội dung hướng dẫn kèm tọa độ và gửi qua App cho tài xế. Nếu pin dưới 5%, điều phối xe sạc pin di động (Mobile Charging Vehicle). |
| **3. Bottleneck** | Bước tra cứu thủ công tình trạng trụ trống của trạm sạc và soạn thảo tin nhắn hướng dẫn bằng tiếng Việt (mất khoảng 10-12 phút trên tổng số 15 phút xử lý). |
| **4. Business Impact** | Mỗi ngày có trung bình ~80 sự cố pin thực địa tại Hà Nội. Gây lãng phí ~20 giờ làm việc/ngày của team điều vận. Đồng thời tăng thời gian dừng xe của tài xế, gây sụt giảm khoảng 15% hiệu năng phục vụ khách của đội xe. |
| **5. Success Metric** | 1. Giảm thời gian xử lý trung bình mỗi ca từ 15 phút xuống **dưới 3 phút**.<br>2. Tỷ lệ đề xuất chính xác trạm sạc trống và phù hợp loại cổng sạc đạt **trên 98%**. |
| **6. Operational Boundary** | **AI được phép:** Tự động gọi API lấy vị trí xe, tra cứu trạm sạc trống tương thích, soạn thảo nháp (draft) tin nhắn.<br>**TUYỆT ĐỐI CẤM:** AI tự ý gửi thẳng tin nhắn chỉ đường cho tài xế mà không qua kiểm duyệt (Bắt buộc phải có tag `[DRAFT_ONLY]` ở đầu để Dispatcher duyệt). Nếu pin dưới 5%, cấm đề xuất trạm sạc xa > 5km, bắt buộc phải trigger yêu cầu điều xe cứu hộ pin di động. |

### 3.2. Future-State Flow & AI Fit
* **Mức độ ứng dụng AI:** **LLM Feature** (Hệ thống có cấu trúc dữ liệu rõ ràng, không cần agent tự trị hoàn toàn để đảm bảo an toàn).
* **Sơ đồ quy trình tương lai (Future-State Workflow):**

```text
┌─────────────────┐       ┌─────────────────────────┐       ┌─────────────────────────┐
│ Bước 1:         │       │ Bước 2:                 │       │ Bước 3:                 │
│ Nhận yêu cầu    │ ───>  │ 🔵 AI tự động gọi API   │ ───>  │ 🔵 AI soạn thảo draft   │
│ sự cố từ tài xế │       │ vị trí & trạm sạc trống │       │ tin nhắn hướng dẫn      │
└─────────────────┘       └─────────────────────────┘       └─────────────────────────┘
                                                                         │
                                                                         ▼
                                                            ┌─────────────────────────┐
                                                            │ Bước 4:                 │
                                                            │ 🟢 Dispatcher duyệt     │
                                                            │ tin nhắn & gửi đi       │
                                                            └─────────────────────────┘
                                                                         │
                                                                         ▼
                                                            ↩️ Fallback khi AI lỗi:
                                                            Hệ thống chuyển sang chế
                                                            độ soạn thảo thủ công
                                                            truyền thống của Dispatcher.
```

---

## 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist:
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test? (Có log tọa độ GPS và lịch sử trạm sạc).
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát? (Có, vì có bước kiểm duyệt của Dispatcher - HITL và cơ chế cảnh báo cạn pin dưới 5%).
3. [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? (Đội điều phối và tài xế rất ủng hộ giải pháp rút ngắn thời gian chờ đợi).

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype)**

**Justification (Lý giải quyết định):**
Bài toán có phạm vi (scope) rất rõ ràng, dữ liệu đầu vào có cấu trúc tốt (tọa độ GPS, danh sách trạm sạc thông qua API của VinFast). Chi phí vận hành LLM thấp vì mỗi lượt gọi chỉ tốn dưới 1,000 token, ước tính chi phí API cho 80 ca/ngày chưa tới 0.1 USD. ROI (Return on Investment) cực kỳ cao nhờ giảm 80% thời gian xử lý của điều phối viên và tăng tính sẵn sàng của tài xế trên đường phố Hà Nội.
