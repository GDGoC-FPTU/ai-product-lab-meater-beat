Mai Việt Anh 2A202601083
Nguyễn Quang Huy 2A202601873
Trương Đình Khoa 2A202601297
Vũ Quang Tùng 2A202601545
Trần Tuấn Trung 2A202601769
Lương Đăng Doanh 2A202601209
# Báo cáo Phân tích Sâu: Dự án AI Smart Charging Emergency Dispatcher (Xanh SM)

---

## 🏛️ Quyết định lựa chọn bài toán của Nhóm

Sau khi thảo luận và phản biện độc lập các thẻ bài toán cá nhân, nhóm thống nhất chọn bài toán:
**"Xử lý sự cố sạc pin thực địa cho tài xế Xanh SM" (GSM - Smart Charging Emergency Dispatcher)**.

### Lý do lựa chọn và loại bỏ các bài toán khác:
* **VinFast — Phân loại mã lỗi từ mô tả của khách hàng (AI-upgrade):** Khối lượng dữ liệu kỹ thuật khổng lồ và mức độ đa dạng của các dòng xe (VF5, VF8, VF9,...) yêu cầu thời gian thu thập và chuẩn hóa dữ liệu mô tả thực tế lớn. Chưa phù hợp để làm prototype nhanh trong giai đoạn này.
* **Vinpearl — Tự động hóa xử lý đặt phòng đoàn từ Email (Tốn thời gian):** Quy trình phụ thuộc vào đối tác lữ hành gửi file không đồng nhất, độ rủi ro sai lệch thông tin phòng cao ảnh hưởng trực tiếp đến doanh thu và trải nghiệm đặt chỗ. Cần tích lũy dữ liệu email thực tế nhiều hơn.
* **Xanh SM — Xử lý sự cố sạc pin thực địa (Lựa chọn của nhóm):** Đây là bài toán có tần suất lặp lại cao hàng ngày (~80 sự cố/ngày tại Hà Nội), trực tiếp ảnh hưởng đến SLA vận hành của tài xế và sự hài lòng của hành khách. Ranh giới vận hành (`Operational Boundary`) rõ ràng và rủi ro được kiểm soát tuyệt đối qua bước duyệt của điều phối viên (Human-in-the-loop).

---

## 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

### 3.1. Current-State Workflow Mapping
Quy trình hiện tại xử lý sự cố hết pin thực địa của tài xế:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ Tra cứu định │     │ Tra cứu trạm │     │ Soạn văn bản │
│ gọi sự cố    │ ──→ │ vị GPS xe    │ ──→ │ sạc VinFast  │ ──→ │ hướng dẫn    │
│              │     │              │     │ còn trụ trống│     │ gửi tài xế   │
│ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │     │ Ai: Dispatch │
│ ⏱ 2 phút     │     │ ⏱ 2 phút     │     │ ⏱ 5 phút 🔴  │     │ ⏱ 5 phút 🔴  │
│ In: Điện thoại│     │ In: Biển số  │     │ In: Vị trí GPS│     │ In: Raw data │
│ Out: Log     │     │ Out: Toạ độ  │     │ Out: Địa chỉ  │     │ Out: SMS     │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                       │
                                                                       ▼
                                                                ┌──────────────┐
                                                                │ Bước 5       │
                                                                │ Gọi xe cứu   │
                                                                │ hộ (nếu cần) │
                                                                │ Ai: Dispatch │
                                                                │ ⏱ 1 phút     │
                                                                └──────────────┘

🔴 Bottleneck: Bước 3 và Bước 4 chiếm tới 10 phút xử lý thủ công (tra cứu trụ sạc tương thích còn trống trên Dashboard và soạn thảo tin nhắn chỉ đường chi tiết bằng tiếng Việt).
⏱ Tổng thời gian xử lý thủ công hiện tại: 15 phút/lượt.
```

---

### 3.2. Problem Statement (6-field) & Metrics

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) tại Trung tâm Điều vận Xanh SM. |
| **2. Current Workflow** | Tài xế gọi điện báo sự cố sạc/hết pin. Dispatcher tra cứu tọa độ xe trên bản đồ nội bộ, kiểm tra trạng thái các trạm sạc VinFast gần đó xem còn trụ trống tương thích không, soạn thảo thủ công tin nhắn hướng dẫn/chỉ đường qua App tài xế, hoặc liên hệ cứu hộ pin di động nếu pin dưới 5%. |
| **3. Bottleneck** | Bước 3 & 4 (mất ~10 phút): Tra cứu thủ công trụ sạc trống phù hợp với dòng xe và soạn thảo nội dung tin nhắn hướng dẫn rõ ràng, chính xác. |
| **4. Business Impact** | Trung bình ~80 sự cố pin/ngày tại Hà Nội. Gây lãng phí 20 giờ làm việc/ngày của team điều vận. Tăng thời gian chờ đợi ngoài đường của tài xế, gây sụt giảm doanh thu ~15% do xe không thể hoạt động và gây ức chế tâm lý cho tài xế. |
| **5. Success Metric** | 1. Giảm tổng thời gian xử lý sự cố từ **15 phút xuống dưới 3 phút** (giảm 80% thời gian xử lý).<br>2. Đảm bảo tỷ lệ gợi ý đúng trạm sạc tương thích và còn trụ trống đạt **98%**. |
| **6. Operational Boundary** | **AI ĐƯỢC PHÉP:** Đọc dữ liệu GPS, API trạm sạc để tự động tra cứu và soạn thảo tin nhắn nháp (Draft).<br>**CẤM TUYỆT ĐỐI:** AI không được tự động gửi tin nhắn đến tài xế mà chưa có sự phê duyệt của Dispatcher (Bắt buộc HITL). Nếu pin xe dưới 5%, AI cấm đề xuất trạm sạc cách xa > 5km, bắt buộc phải trả ra JSON yêu cầu điều xe cứu hộ pin. |

---

### 3.3. Future-State Flow & AI Fit

* **Phân loại AI Fit:** **LLM Feature** (Quy trình có cấu trúc cố định và ranh giới rõ ràng, không cần sử dụng Agent tự trị để tránh rủi ro tự ý ra quyết định sai lệch).
* **Future-State Workflow Diagram:**

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ 🔵 Auto-pull │     │ 🔵 AI draft  │     │ 🟢 Dispatch  │
│ gọi sự cố    │ ──→ │ vị trí &     │ ──→ │ SMS chỉ dẫn  │ ──→ │ click duyệt  │
│              │     │ trạm sạc trống│    │ & chỉ đường  │     │ & gửi tài xế │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                       │
                                                                       ▼
                                                                ↩️ Fallback:
                                                                Nếu AI draft lỗi
                                                                hoặc mất kết nối,
                                                                Dispatcher tự nhập
                                                                tay lại như cũ.
```

* **Mô tả các bước chính:**
  1. **Bước 1:** Tài xế gọi báo sự cố, hệ thống ghi nhận biển số và định vị xe.
  2. **Bước 2 (AI Step - Auto-pull):** Hệ thống tự động gọi API lấy tọa độ GPS xe và lọc danh sách các trạm sạc VinFast còn trụ trống trong bán kính 5km.
  3. **Bước 3 (AI Step - Draft SMS):** LLM nhận thông tin đầu vào, nếu pin dưới 5% và trạm sạc > 5km, tự động đề xuất kích hoạt xe cứu hộ qua cấu trúc JSON. Nếu pin an toàn, tự động soạn thảo bản nháp chỉ dẫn chi tiết kèm tag `[DRAFT_ONLY]`.
  4. **Bước 4 (Human Step - HITL):** Điều phối viên xem bản nháp trên màn hình Dashboard, kiểm tra tính hợp lý, chỉnh sửa nếu cần và nhấn "Phê duyệt & Gửi".
  5. **Phương án dự phòng (Fallback):** Nếu hệ thống AI hoặc API gặp sự cố, hệ thống tự động chuyển sang chế độ thủ công (Fallback), hiển thị bản đồ truyền thống để điều phối viên tra cứu bằng tay như quy trình cũ.

---

# 🏁 Phase 5 — EVALUATE (Nhóm)

### 3.1. AI Readiness Checklist:
* [x] **Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?** -> *Có, VinFast cung cấp API thời gian thực về vị trí xe, dung lượng pin và danh sách trạng thái trụ sạc VinFast.*
* [x] **Rủi ro khi AI sai có nằm trong tầm kiểm soát?** -> *Có, thiết lập ranh giới bắt buộc phải có Điều phối viên kiểm tra và duyệt tin nhắn nháp (HITL) trước khi gửi đi.*
* [x] **Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?** -> *Có, ban điều hành GSM đang thúc đẩy số hóa mạnh mẽ để giảm tải áp lực cho trung tâm điều vận và tài xế.*

---

### 3.2. Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:

[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp (Thử nghiệm ban đầu tại khu vực Hà Nội).

---

### 3.3. Justification (Luận điểm kỹ thuật & Ước lượng chi phí):

#### A. Luận điểm kỹ thuật:
1. **Khả thi cao (High Feasibility):** Giải pháp sử dụng mô hình **Gemini 2.5 Flash** kết hợp hệ thống API có sẵn của VinFast/GSM. Logic xử lý là dạng trích xuất và sinh văn bản có cấu trúc ngắn, mô hình xử lý rất nhanh và chính xác.
2. **Kiểm soát rủi ro tuyệt đối:** Ranh giới vận hành được cài đặt trực tiếp trong Prompt System và được double-check bằng code logic (Rule-based). Sự hiện diện của con người (HITL) loại bỏ hoàn toàn khả năng AI gửi tin nhắn sai lệch cho tài xế.

#### B. Ước lượng chi phí chi tiết (Cost Estimation):
1. **Chi phí API (Gemini 2.5 Flash):**
   * *Đơn giá:* $0.075 / 1 triệu input tokens; $0.30 / 1 triệu output tokens.
   * *Ước tính token cho mỗi lượt xử lý:* Input ~1,500 tokens (bao gồm System Prompt + GPS data + Logs); Output ~300 tokens (bản nháp SMS).
   * *Chi phí trên mỗi cuống xử lý:* 
     $$\text{Cost} = (1,500 \times \frac{0.075}{1,000,000}) + (300 \times \frac{0.30}{1,000,000}) = 0.0001125 + 0.00009 = 0.0002025 \text{ USD/lượt} \approx 5 \text{ VNĐ/lượt}$$
   * *Chi phí API vận hành tại Hà Nội (~80 sự cố/ngày):*
     $$80 \text{ lượt/ngày} \times 30 \text{ ngày} \times 5 \text{ VNĐ} = 12,000 \text{ VNĐ/tháng} \approx 0.48 \text{ USD/tháng}$$
   * *Chi phí API khi scale rộng toàn quốc (~1,000 sự cố/ngày):*
     $$1,000 \text{ lượt/ngày} \times 30 \text{ ngày} \times 5 \text{ VNĐ} = 150,000 \text{ VNĐ/tháng} \approx 6 \text{ USD/tháng}$$
     *(Chi phí API cực kỳ tối ưu, gần như bằng không).*

2. **Chi phí Nhân sự & Vận hành phát triển (One-time):**
   * Đội ngũ gồm 2 AI Engineer của Vin Smart Future làm việc trong 1 tháng để tích hợp hệ thống API và xây dựng Dashboard: **100,000,000 VNĐ** ($4,000).
   * Chi phí vận hành hạ tầng Cloud/Hosting phụ trợ: ~1,000,000 VNĐ/tháng.

3. **Hiệu quả đầu tư (ROI Estimation):**
   * *Trước khi áp dụng AI:* Xử lý 80 sự cố mất 20 giờ làm việc/ngày của Dispatcher. Chi phí nhân sự điều vận tương đương ~30,000,000 VNĐ/tháng. Xe cứu hộ/xe kéo kéo xe cạn pin do hướng dẫn chậm tốn trung bình 2,000,000 VNĐ/vụ.
   * *Sau khi áp dụng AI:* Thời gian xử lý giảm 80% (từ 15 phút xuống 3 phút), giải phóng 16 giờ làm việc/ngày của Dispatcher (tiết kiệm ~24,000,000 VNĐ/tháng chi phí nhân sự). Giảm thiểu các vụ xe hết pin dọc đường nhờ cảnh báo và điều xe cứu hộ kịp thời, ước tính tiết kiệm ít nhất 30,000,000 VNĐ/tháng tiền cứu hộ và giảm thất thoát doanh thu chạy xe của tài xế.
   * *Thời gian hoàn vốn đầu tư (Payback Period):* Dưới 2 tháng vận hành thực tế.
