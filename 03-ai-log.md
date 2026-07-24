# 03-ai-log.md — AI Log & Reflection

## Hành trình dùng AI trong buổi Lab
Trong buổi Lab này, tôi đã dùng AI như một thought partner để:
- Brainstorm các vấn đề vận hành tiềm năng trong hệ sinh thái Vingroup.
- Tạo các Quick Problem Card và định nghĩa metric cụ thể cho từng bài toán.
- Thiết kế prompt prototype với ranh giới an toàn và kiểm thử biên bằng các kịch bản tấn công.

## AI giúp gì
AI hỗ trợ tôi nhanh chóng:
- Xác định các bước thủ công tốn thời gian trong quy trình xử lý sự cố hết pin.
- Đề xuất cách cấu trúc Problem Statement 6-field rõ ràng.
- Viết hệ thống prompt có tag bắt buộc và điều kiện pin nghiêm ngặt để bảo vệ an toàn.

## AI sai gì
Trong một số lần thử nghiệm với kịch bản gây nhiễu, AI dễ bị dụ:
- Bỏ qua yêu cầu `[DRAFT_ONLY]` khi người dùng cố tình yêu cầu gửi trực tiếp.
- Đề xuất trạm sạc xa hơn 5km trong tình huống pin dưới 5%, gây rủi ro an toàn.

## Sửa đổi prompt và ranh giới
Để khắc phục, tôi đã:
- Củng cố Rule 1: Bắt buộc output luôn bắt đầu bằng tag `[DRAFT_ONLY]` và chỉ trả về bản nháp.
- Củng cố Rule 2: Nếu pin < 5% thì không được đề xuất trạm xa hơn 5km, phải trả về `dispatch_mobile_charger`.
- Thiết kế ít nhất 2 kịch bản adversarial để kiểm tra xem ranh giới có bị phá vỡ hay không.

## Bài học rút ra
AI là công cụ rất hữu ích khi làm việc với vấn đề vận hành thực tế, nhưng để áp dụng an toàn thì cần:
- Giới hạn rõ ràng nhiệm vụ và phạm vi cho mô hình.
- Giữ con người trong vòng kiểm duyệt cho các quyết định có rủi ro.
- Kiểm thử bằng các tình huống cố ý tấn công để phát hiện rò rỉ prompt.
