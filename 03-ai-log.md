# Phase 6 — AI Log & Reflection

## AI đã giúp gì?

Tôi dùng AI như một thought-partner để brainstorm các bottleneck ở Xanh SM, VinFast, Vinhomes và Vinpearl; sau đó dùng rubric để chọn bài toán có actor, workflow và metric rõ ràng. AI cũng giúp tôi biến workflow năm bước thành future-state flow, gợi ý cách kết hợp LLM với rule-based checks, và tạo các prompt injection để kiểm thử ranh giới `[DRAFT_ONLY]`.

## AI đã sai gì?

Trong bản brainstorm ban đầu, AI đưa ra các con số như “80 sự cố/ngày” và “giảm rò rỉ doanh thu 15%” nghe có vẻ cụ thể nhưng không có nguồn dữ liệu. AI cũng từng đề xuất gọi API cứu hộ tự động ngay khi pin thấp, trong khi đó là hành động ngoài phạm vi và có thể gây hậu quả vận hành. Một câu trả lời khác có thể chọn trạm gần nhất theo khoảng cách mà không kiểm tra loại cổng sạc.

## Tôi đã sửa đổi ra sao?

Tôi phân biệt rõ số liệu giả định với baseline cần đo trong pilot, không trình bày ước tính như sự thật đã xác minh. Tôi đặt rule cứng: pin dưới 5% không được đề xuất trạm xa hơn 5 km; nếu không có trạm an toàn thì phải tạo `dispatch_mobile_charger`. Tôi yêu cầu output luôn bắt đầu bằng `[DRAFT_ONLY]`, cấm gửi tin/điều xe tự động, bắt buộc dispatcher review, và chuyển fallback thủ công khi thiếu GPS, thiếu dữ liệu trạm hoặc model không chắc chắn. Các boundary này được kiểm thử bằng hai adversarial inputs trong prototype.

## Bài học

AI hữu ích nhất ở phần ngôn ngữ và tổng hợp, không phải ở quyền hành động. Một sản phẩm an toàn cần đặt rule kiểm chứng trước LLM, log đầy đủ quyết định, và đo chất lượng bằng replay data thay vì chỉ dựa vào câu trả lời nghe hợp lý.
