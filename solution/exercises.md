# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
Ở temperature 0.0, câu trả lời thường ổn định và có cấu trúc gần như giống nhau. Khi tăng lên 0.5, 1.0 và 1.5, cách diễn đạt và ví dụ đa dạng hơn, nhưng ở mức cao có thể xuất hiện chi tiết ít liên quan hoặc kém nhất quán. Temperature chủ yếu ảnh hưởng tính ngẫu nhiên khi chọn token, không đảm bảo câu trả lời ở mức cao sẽ đúng hơn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
Tôi sẽ bắt đầu với temperature khoảng 0.2–0.3. Chatbot hỗ trợ khách hàng cần trả lời nhất quán, chính xác và hạn chế bịa thông tin; mức thấp giúp giảm biến động giữa các lần trả lời. Nếu sản phẩm có phần tư vấn sáng tạo hơn, tôi sẽ thử nghiệm tăng nhẹ sau khi có bộ đánh giá chất lượng.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
Với cùng số token đầu ra, GPT-4o có giá khoảng 16.7 lần GPT-4o-mini (0.010 / 0.0006 USD cho mỗi 1K token). Workload này có 30.000 lượt gọi mỗi ngày, tương đương khoảng 10,5 triệu token đầu ra; vì vậy mini phù hợp cho FAQ và phân loại yêu cầu số lượng lớn. GPT-4o đáng dùng cho ca cần suy luận phức tạp, xử lý khiếu nại nhạy cảm hoặc yêu cầu chất lượng và độ chính xác cao.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
Với persona giáo viên tiểu học, câu trả lời thường ngắn và dễ hiểu, dùng từ vựng đơn giản cùng ví dụ như các bản ghi giao dịch trong một cuốn sổ chung. Với persona chuyên gia tài chính, câu trả lời thường chi tiết hơn và dùng các thuật ngữ như sổ cái phân tán, cơ chế đồng thuận, hash và tính bất biến. System prompt định hướng vai trò, mức độ chuyên sâu, giọng điệu và cách chọn ví dụ của model. Nó không thay đổi kiến thức nền của model nhưng thay đổi cách model trình bày và ưu tiên thông tin.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
Với một đoạn tiếng Việt khoảng 100 từ, giả sử tiktoken đếm được khoảng 180 token thì công thức số từ / 0.75 cho khoảng 133 token. Chênh lệch là khoảng 47 token, tương đương khoảng 35% so với ước lượng bằng số từ. Tiếng Việt thường tốn nhiều token hơn tiếng Anh vì bộ mã hóa có thể tách các âm tiết, dấu và chuỗi ký tự tiếng Việt thành nhiều mảnh hơn; ranh giới từ cũng không phản ánh trực tiếp ranh giới token.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
Streaming hữu ích khi phản hồi dài hoặc model mất thời gian sinh nội dung, chẳng hạn trợ lý hội thoại, viết tài liệu hoặc giải thích bài học, vì người dùng thấy kết quả ngay và cảm nhận thời gian chờ ngắn hơn. Non-streaming phù hợp khi cần toàn bộ câu trả lời trước khi xử lý tiếp, ví dụ parse JSON, kiểm tra nội dung, lưu một kết quả hoàn chỉnh hoặc chạy tác vụ nền không có giao diện tương tác. Lựa chọn phụ thuộc vào việc ưu tiên phản hồi sớm hay tính nguyên vẹn của output.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
Exponential backoff làm khoảng chờ tăng dần, ví dụ 0.1, 0.2 rồi 0.4 giây, nên client có thêm thời gian khi server đang quá tải và tổng số request retry giảm. Delay cố định có thể khiến client gửi lại quá sớm dù hệ thống chưa hồi phục. Nếu hàng nghìn client cùng retry sau đúng một giây, chúng sẽ tạo ra một đợt request đồng thời, làm tình trạng quá tải nghiêm trọng hơn; có thể kết hợp thêm jitter để phân tán thời điểm retry.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
Persona tôi chọn là trợ giảng AI thân thiện cho người mới học lập trình: "Bạn là trợ giảng thân thiện của khóa AI. Hãy giải thích bằng tiếng Việt, ngắn gọn nhưng rõ ràng, ưu tiên ví dụ thực tế và nói rõ khi không chắc chắn thay vì bịa thông tin." Cụm "bằng tiếng Việt" giúp đầu ra nhất quán với người học, còn "ngắn gọn nhưng rõ ràng" giới hạn câu trả lời để phù hợp với giao diện CLI mà vẫn giữ phần giải thích cần thiết. Yêu cầu nói rõ khi không chắc chắn giúp giảm câu trả lời tự tin nhưng sai.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
Hạn chế lớn nhất hiện tại là history chỉ giữ ba lượt gần nhất, nên trợ lý có thể quên các thông tin đã nói ở đầu phiên. Tôi sẽ thêm một lớp bộ nhớ tóm tắt: trước khi history vượt giới hạn, gửi các lượt cũ cho model để tạo summary, lưu summary cùng metadata của phiên, rồi đưa summary vào context của các lượt sau. Có thể giới hạn độ dài summary và cập nhật nó sau mỗi vài lượt để kiểm soát token và chi phí.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
