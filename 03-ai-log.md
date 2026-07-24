# 03 - AI Log (Personal Reflection)

Name: Nguyễn Quang Huy

## AI helped with
- Hỗ trợ brainstorm Quick Cards và cấu trúc báo cáo Phase 1–3.
- Hỗ trợ soạn SYSTEM_PROMPT và viết harness kiểm thử prompt (starter-code/prompt_prototype.py).
- Giúp soạn thảo bản nháp thông điệp cho operator và các kịch bản kiểm thử.

## AI mistakes (hallucinations / unsafe suggestions)
- Ví dụ: LLM ban đầu có thể gợi ý điều hướng tới trạm ở xa ngay cả khi thông tin pin cho thấy mức rất thấp — đây là hành vi không an toàn.
- Một số lần LLM trả lời thiếu thông tin bối cảnh (không hỏi thêm battery %, traffic), dẫn đến khuyến nghị không đủ an toàn.

## How I adjusted prompts (Sửa đổi prompt để đảm bảo an toàn)
- Thêm quy tắc bắt buộc ở cấp system prompt: mọi output phải bắt đầu bằng [DRAFT_ONLY].
- Thêm rule cứng: nếu battery < 5% thì KHÔNG được gợi ý trạm > 5km và phải trigger action dispatch mobile charger.
- Viết các test cases đối kháng (adversarial tests) để tấn công prompt và kiểm tra hệ thống vẫn an toàn.

## Short reflection
Qua bài lab, nhận thấy việc kết hợp rule-based safety + LLM để tạo nháp là mô hình phù hợp cho hệ thống có rủi ro an toàn. Con người vẫn cần đóng vai trò phê duyệt trước khi gửi/tác động thực tế.

## Actions tôi đã làm trong repo
- Hoàn chỉnh starter-code/prompt_prototype.py để hỗ trợ chế độ offline (rule-based) và fallback khi không có API key.
- Tạo/hoàn thiện các file nộp bài (01/02/03/04) cho mục đích autograder.
