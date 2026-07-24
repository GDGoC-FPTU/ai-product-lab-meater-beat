# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

---

## 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dưới đây là bảng quét cơ hội vận hành tại các công ty thành viên thuộc tập đoàn Vingroup dựa trên **4 Lenses** (Lặp lại, Tốn thời gian, AI-upgrade, Stakeholder Pain) nhằm xác định các điểm nghẽn có thể tối ưu hóa bằng AI.

### Bảng quét cơ hội (SCAN Table):
| # | Subsidiary | Lens | Mô tả ngắn bài toán / Bottleneck thực tế |
|---|------------|------|------------------------------------------|
| 1 | **Vinmec** | Tốn thời gian | Bác sĩ mất quá nhiều thời gian tổng hợp hồ sơ và viết tóm tắt xuất viện bằng ngôn ngữ thông thường cho bệnh nhân khi ra viện (mất 20-30 phút/bệnh nhân). |
| 2 | **VinFast** | AI-upgrade | Khách hàng mô tả lỗi xe bằng tiếng Việt phi cấu trúc ("đi qua gờ kêu cụp cụp", "lúc lùi cam bị sọc"), kỹ thuật viên tốn thời gian tra cứu đối chiếu sang mã lỗi kỹ thuật (DTC/OBD). |
| 3 | **Vinpearl** | Tốn thời gian | Nhân viên phòng đặt phòng (Reservation Agent) đọc thủ công các email đặt phòng theo đoàn (Group Booking) phức tạp từ công ty lữ hành rồi nhập tay từng thông tin vào phần mềm PMS Opera. |
| 4 | **Vinhomes** | Lặp lại | Nhân viên CSKH đọc thủ công hàng trăm phản ánh, khiếu nại của cư dân trên App Vinhomes Resident để phân loại và điều hướng về đúng Ban quản lý tòa nhà/bộ phận kỹ thuật. |
| 5 | **Xanh SM** | Pain từ người khác | Chuyên viên phân tích vận hành phải nghe thủ công file ghi âm cuộc gọi hủy chuyến và đọc ghi chú tài xế để tóm tắt, tìm ra pattern lỗi hệ thống hoặc hành vi tài xế khiến khách hủy chuyến. |
| 6 | **VinFast** | Lặp lại | So khớp hóa đơn sạc điện hàng tuần từ các đối tác trạm sạc liên kết ngoài với dữ liệu sạc điện ghi nhận trên hệ thống backend của VinFast. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn **top 3 bài toán** tiềm năng nhất từ danh sách trên để tiến hành đánh giá nhanh khả năng khả thi và thiết kế sơ bộ:
- **Card #1:** Vinmec — Tóm tắt hồ sơ xuất viện (Discharge Summary)
- **Card #2:** VinFast — Phân loại mã lỗi từ mô tả của khách hàng
- **Card #3:** Vinpearl — Tự động hóa xử lý đặt phòng đoàn từ Email

---

### ┌─────────────────────────────────────────────────────────────┐
### │ QUICK PROBLEM CARD #1                                       │
### │                                                             │
### │ Bài toán: Bác sĩ tốn nhiều thời gian đọc và tổng hợp bệnh   │
### │ án để viết tóm tắt hồ sơ xuất viện cho bệnh nhân khi ra viện.│
### │ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
### │                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
### │                                                             │
### │ Ai đang đau (Actor)? Bác sĩ điều trị (quá tải hành chính)    │
### │                      Bệnh nhân (phải chờ đợi thủ tục lâu)    │
### │                                                             │
### │ Workflow thủ công hiện tại (5 bước):                        │
### │   1. Bệnh nhân có chỉ định ra viện                            │
### │   ──> 2. Bác sĩ đọc toàn bộ bệnh án điện tử, kết quả xét nghiệm│
### │   ──> 3. Trích xuất thông tin chính & gõ bản tóm tắt y khoa     │
### │   ──> 4. Trưởng khoa duyệt hồ sơ xuất viện                    │
### │   ──> 5. Nhân viên hành chính in hồ sơ và bàn giao cho khách  │
### │                                                             │
### │ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ 25 phút/lượt)  │
### │ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3                 │
### │ (Tự động đọc EHR -> Trích xuất dữ liệu -> Draft bản tóm tắt)│
### │                                                             │
### │ Đo thành công bằng gì (Metric có số)?                        │
### │ Giảm thời gian soạn thảo tóm tắt xuất viện từ 25 phút/lượt  │
### │ ──> dưới 3 phút/lượt (bác sĩ chỉ cần kiểm tra và bấm duyệt). │
### │                                                             │
### │ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
### └─────────────────────────────────────────────────────────────┘

---

### ┌─────────────────────────────────────────────────────────────┐
### │ QUICK PROBLEM CARD #2                                       │
### │                                                             │
### │ Bài toán: Kỹ thuật viên/CSKH mất thời gian tra cứu mã lỗi    │
### │ kỹ thuật chuẩn hóa từ mô tả ngôn ngữ tự nhiên của khách hàng.│
### │ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
### │                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
### │                                                             │
### │ Ai đang đau (Actor)? Kỹ thuật viên chẩn đoán lỗi tại xưởng    │
### │                      Điều phối viên tiếp nhận yêu cầu CSKH   │
### │                                                             │
### │ Workflow thủ công hiện tại (4 bước):                        │
### │   1. Khách hàng mô tả lỗi xe bằng tiếng Việt qua App/Hotline  │
### │   ──> 2. CSKH ghi nhận thông tin mô tả thô phi cấu trúc       │
### │   ──> 3. Kỹ thuật viên đọc mô tả, tra cứu cẩm nang để tìm DTC │
### │   ──> 4. Xác định mã lỗi chuẩn và tạo phiếu sửa chữa xưởng    │
### │                                                             │
### │ Bước nào tốn thời gian/lỗi nhất? Bước 3 (⏱ 15 phút/lượt)   │
### │ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3                 │
### │ (Đọc mô tả thô -> Mapping với Vector Database -> Gợi ý mã) │
### │                                                             │
### │ Đo thành công bằng gì (Metric có số)?                        │
### │ Giảm thời gian xác định mã lỗi ban đầu từ 15 phút ──> dưới │
### │ 2 phút, với độ chính xác gợi ý TOP 3 mã lỗi đạt trên 92%.   │
### │                                                             │
### │ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
### └─────────────────────────────────────────────────────────────┘

---

### ┌─────────────────────────────────────────────────────────────┐
### │ QUICK PROBLEM CARD #3                                       │
### │                                                             │
### │ Bài toán: Nhân viên đặt phòng phải đọc và nhập thủ công dữ   │
### │ liệu booking đoàn phức tạp từ email vào hệ thống PMS Opera.  │
### │ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
### │                     [ ] Vinmec   [x] Khác (Vinpearl)_______ │
### │                                                             │
### │ Ai đang đau (Actor)? Nhân viên phòng đặt phòng (Reservation)│
### │                                                             │
### │ Workflow thủ công hiện tại (5 bước):                        │
### │   1. Nhận email đặt phòng đoàn kèm danh sách khách (Excel)   │
### │   ──> 2. Đọc email, mở PMS đối chiếu kiểm tra quỹ phòng trống│
### │   ──> 3. Nhập tay từng tên khách, ngày ở, loại phòng vào PMS │
### │   ──> 4. Kiểm tra chéo tránh sai lệch thông tin nhập liệu     │
### │   ──> 5. Gửi email xác nhận đặt phòng và hóa đơn tạm tính     │
### │                                                             │
### │ Bước nào tốn thời gian/lỗi nhất? Bước 3-4 (⏱ 35 phút/đoàn)  │
### │ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4               │
### │ (Trích xuất email/Excel thành JSON -> Gọi API tự động PMS)  │
### │                                                             │
### │ Đo thành công bằng gì (Metric có số)?                        │
### │ Giảm thời gian xử lý một đoàn từ 35 phút ──> dưới 3 phút.    │
### │                                                             │
### │ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
### └─────────────────────────────────────────────────────────────┘
