# Ngày 1 — Bài Tập & Phản Ánh
## Nền Tảng LLM API | Phiếu Thực Hành

**Thời lượng:** 1:30 giờ  
**Cấu trúc:** Lập trình cốt lõi (60 phút) → Bài tập mở rộng (30 phút)

---

## Phần 1 — Lập Trình Cốt Lõi (0:00–1:00)

Chạy các ví dụ trong Google Colab tại: https://colab.research.google.com/drive/172zCiXpLr1FEXMRCAbmZoqTrKiSkUERm?usp=sharing

Triển khai tất cả TODO trong `template.py`. Chạy `pytest tests/` để kiểm tra tiến độ.

**Điểm kiểm tra:** Sau khi hoàn thành 4 nhiệm vụ, chạy:
```bash
python template.py
```
Bạn sẽ thấy output so sánh phản hồi của GPT-4o và GPT-4o-mini.

---

## Phần 2 — Bài Tập Mở Rộng (1:00–1:30)

### Bài tập 2.1 — Độ Nhạy Của Temperature
Gọi `call_openai` với các giá trị temperature 0.0, 0.5, 1.0 và 1.5 sử dụng prompt **"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Ở temperature thấp (0.0), phản hồi mang tính tất định tuyệt đối (deterministic), từ ngữ được chọn lọc cực kỳ nhất quán, cô đọng và có cấu trúc giống hệt nhau qua mỗi lần gọi. Khi tăng temperature lên mức trung bình (0.5 – 1.0), câu trả lời trở nên phong phú hơn, sử dụng từ vựng đa dạng, sinh động và có độ trôi chảy tự nhiên cao. Tuy nhiên, ở temperature rất cao (1.5), tính hỗn loạn tăng vọt dẫn đến phản hồi bị mất cấu trúc logic, sinh ra các chuỗi từ lặp đi lặp lại vô nghĩa, sai lệch thông tin nghiêm trọng (hallucination) hoặc bị lỗi định dạng ký tự.

**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature ở mức rất thấp, khoảng **0.0 đến 0.2**. Mức cấu hình này triệt tiêu tính ngẫu nhiên, buộc chatbot luôn đưa ra câu trả lời nhất quán, chính xác tuyệt đối dựa trên tài liệu hỗ trợ sẵn có (knowledge base) và tránh hoàn toàn các trường hợp trả lời bịa đặt thông tin hoặc ngôn từ không chuẩn mực.

---

### Bài tập 2.2 — Đánh Đổi Chi Phí
Xem xét kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người thực hiện 3 lần gọi API, mỗi lần trung bình ~350 token.

**Ước tính xem GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này:**
> Dựa trên bảng giá thực tế được khai báo trong hệ thống dự án (`PRICING_1M_TOKENS`):
> - **GPT-4o:** $5.00 / 1M Input tokens và $20.00 / 1M Output tokens.
> - **GPT-4o-mini:** $0.150 / 1M Input tokens và $0.600 / 1M Output tokens.
> 
> Tỷ lệ chênh lệch giá giữa GPT-4o và GPT-4o-mini đối với cả Input ($5.00 / $0.150 = 33.33) và Output ($20.00 / $0.600 = 33.33) là hoàn toàn giống nhau. Do đó, **cho bất kỳ phân bổ đầu vào/đầu ra nào của workload này, GPT-4o luôn đắt hơn GPT-4o-mini đúng 33.33 lần**.
> 
> *Chi tiết ước tính tài chính cho workload hàng ngày:*
> - Tổng lượt gọi: 10,000 người × 3 = 30,000 requests/ngày.
> - Tổng số token tiêu thụ: 30,000 requests × 350 tokens = 10,500,000 tokens/ngày (10.5M tokens).
> - Giả định tỉ lệ Input/Output là 7:3 (7.35M tokens Input và 3.15M tokens Output):
>   - **GPT-4o-mini:** (7.35M × $0.15 + 3.15M × $0.60) / 1M = $1.1025 + $1.89 = **$2.99 / ngày** (~$90 / tháng).
>   - **GPT-4o:** (7.35M × $5.00 + 3.15M × $20.00) / 1M = $36.75 + $63.00 = **$99.75 / ngày** (~$2,992.5 / tháng).
>   - Chi phí chênh lệch thực tế là khoảng **$2,900 USD mỗi tháng**.

**Mô tả một trường hợp mà chi phí cao hơn của GPT-4o là xứng đáng, và một trường hợp GPT-4o-mini là lựa chọn tốt hơn:**
> - **Trường hợp GPT-4o xứng đáng:** Các tác vụ yêu cầu khả năng suy luận logic đa bước phức tạp, phân tích chuyên sâu các dữ liệu nhạy cảm (như hỗ trợ chẩn đoán y tế, phân tích hợp đồng pháp lý, kiểm thử bảo mật mã nguồn). Ở đây, sai sót của mô hình có thể gây hậu quả cực kỳ nghiêm trọng, vì vậy khả năng xử lý thông tin vượt trội của GPT-4o là hoàn toàn xứng đáng với chi phí.
> - **Trường hợp GPT-4o-mini tối ưu hơn:** Các tác vụ mang tính chất lặp lại, cấu trúc đơn giản và khối lượng truy vấn khổng lồ (như phân loại ý định người dùng (intent routing), tóm tắt nhanh lịch sử chat, trích xuất thực thể (entity extraction), hoặc bộ lọc câu hỏi FAQ cơ bản). Sử dụng GPT-4o cho các tác vụ này sẽ gây lãng phí ngân sách nghiêm trọng mà không mang lại khác biệt đáng kể về mặt hiệu quả.

---

### Bài tập 2.3 — Trải Nghiệm Người Dùng với Streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì non-streaming lại phù hợp hơn?** (1 đoạn văn)
> **Streaming** là giải pháp tối ưu cho các giao diện tương tác trực tiếp thời gian thực với người dùng (chatbot, trợ lý viết bài, dịch thuật song song) nhờ tối ưu chỉ số Time to First Token (TTFT). Việc hiển thị từng từ được sinh ra lập tức giúp giảm độ trễ cảm nhận, tạo cảm giác hệ thống đang phản hồi vô cùng nhanh chóng. Ngược lại, **non-streaming** lại vượt trội trong các tiến trình xử lý ngầm (backend pipelines), hệ thống RAG phức tạp hoặc tác vụ tự động hóa, nơi kết quả đầu ra của LLM cần được xác thực định dạng JSON (parsing), lọc nội dung độc hại (content moderation), hoặc kết hợp với các logic lập trình khác trước khi gửi trả kết quả trọn vẹn và an toàn về phía người dùng.


## Danh Sách Kiểm Tra Nộp Bài
 [x] Tất cả tests pass: `pytest tests/ -v`
 [x] `call_openai` đã triển khai và kiểm thử
 [x] `call_openai_mini` đã triển khai và kiểm thử
 [x] `compare_models` đã triển khai và kiểm thử
 [x] `streaming_chatbot` đã triển khai và kiểm thử
 [x] `retry_with_backoff` đã triển khai và kiểm thử
 [x] `batch_compare` đã triển khai và kiểm thử
 [x] `format_comparison_table` đã triển khai và kiểm thử
 [x] `exercises.md` đã điền đầy đủ
 [x] Sao chép bài làm vào folder `solution` và đặt tên theo quy định
