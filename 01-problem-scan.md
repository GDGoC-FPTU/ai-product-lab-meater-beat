# Phase 1 & Phase 2: Problem Scan & Quick Problem Cards

## 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dưới đây là danh sách quét cơ hội áp dụng AI tại các công ty thành viên Vingroup sử dụng **4 Lenses**:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công các phản hồi khẩn cấp từ tài xế về sự cố sạc pin hoặc va chạm thực địa (mất 15-20 min/lượt). |
| 2 | **VinFast** | AI có thể tốt hơn | Trợ lý ảo gợi ý trạm sạc còn trụ trống phù hợp nhất với loại cổng sạc (CCS2/GBT) và dung lượng pin của từng dòng xe điện (VF5, VF8, VF9). |
| 3 | **Vinhomes** | Lặp lại | Phân loại và điều hướng tự động các phản ánh/khiếu nại của cư dân từ app Vinhomes Resident về ban quản lý tòa nhà phù hợp (mất 6-12 tiếng để phân loại thủ công). |
| 4 | **Vinmec** | Tốn thời gian | Bác sĩ mất quá nhiều thời gian viết tóm tắt hồ sơ xuất viện (Discharge Summary) từ hồ sơ bệnh án chi tiết (mất 20-30 phút/bệnh nhân). |
| 5 | **VinFast** | Lặp lại | So khớp dữ liệu sạc điện hằng tuần từ hàng nghìn trụ sạc liên kết ngoài với hóa đơn thực tế để đối soát tài chính. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Dưới đây là 3 thẻ bài toán tiềm năng được lựa chọn và đánh giá chi tiết:

### QUICK PROBLEM CARD #1: Xanh SM Xử lý sự cố sạc pin thực địa
```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Tài xế Xanh SM báo cáo sự cố sạc pin / hết pin    │
│ giữa đường cần điều phối cứu hộ hoặc trạm sạc gần nhất.     │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau? Tài xế (chờ đợi), Điều phối viên (quá tải)     │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Tài xế gọi tổng đài điều vận báo hết pin               │
│   → 2. Điều phối viên tra cứu thủ công vị trí xe trên bản đồ│
│   → 3. Tra cứu thủ công các trạm sạc VinFast còn trụ trống   │
│   → 4. Viết tin nhắn chỉ dẫn/đường đi gửi qua App tài xế    │
│   → 5. Liên hệ đội xe cứu hộ nếu xe đã cạn kiệt pin         │
│                                                             │
│ Bước nào tốn nhất? Bước 3-4 (⏱ 12 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4              │
│ (Tự động hóa lấy vị trí -> Tra cứu trạm trống -> Draft tin) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút.      │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Tự động soạn chỉ dẫn)   │
└─────────────────────────────────────────────────────────────┘
```

### QUICK PROBLEM CARD #2: Vinhomes Phân loại và điều hướng phản ánh cư dân
```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Phân loại và điều phối tự động các phản ánh của    │
│ cư dân trên App Vinhomes Resident.                          │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Cư dân (chậm hỗ trợ), CSKH Vinhomes (quá tải)  │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi khiếu nại (text/hình ảnh) trên app          │
│   → 2. Nhân viên CSKH trung tâm đọc và phân loại thủ công   │
│   → 3. CSKH forward yêu cầu đến BQL tòa nhà/kỹ thuật phù hợp│
│   → 4. BQL tiếp nhận và xử lý sự cố tại chỗ                 │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 6-12 tiếng chờ phân phối)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (Phân loại ý định từ phản ánh cư dân -> Điều hướng tự động) │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian điều hướng từ 6 tiếng ──> dưới 10 phút.      │
│ Tỉ lệ điều phối đúng phòng ban đạt trên 95%.                │
│                                                             │
│ Quick Architecture: [x] Rule/LLM Feature (Routing ý định)   │
└─────────────────────────────────────────────────────────────┘
```

### QUICK PROBLEM CARD #3: Vinmec Soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary)
```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Bác sĩ tốn thời gian viết Discharge Summary cho  │
│ bệnh nhân chuẩn bị xuất viện.                               │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau? Bác sĩ (quá tải hành chính), Bệnh nhân (chờ lâu)│
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Bác sĩ xem lại toàn bộ bệnh án điện tử, xét nghiệm     │
│   → 2. Trích xuất thông tin lâm sàng chính                  │
│   → 3. Soạn thảo văn bản tóm tắt xuất viện bằng tiếng Việt  │
│   → 4. Ký duyệt hồ sơ và bàn giao cho bệnh nhân             │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 20-30 phút/bệnh nhân)        │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (Trích xuất thông tin lâm sàng -> Draft Discharge Summary)  │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian viết hồ sơ từ 25 phút ──> dưới 3 phút/ca.    │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Draft tóm tắt lâm sàng)│
└─────────────────────────────────────────────────────────────┘
```
