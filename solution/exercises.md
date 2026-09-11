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

Khi temperature thấp (0.0–0.5), các phản hồi có xu hướng ổn định, ít đa dạng và tập trung vào những chủ đề phổ biến như cà phê Việt Nam (Mô hình ưu tiên chọn các token có xác suất xuất hiện cao nhất). Khi temperature tăng lên 1.0–1.5, nội dung và cách diễn đạt đa dạng hơn, chẳng hạn chuyển sang hang Sơn Đoòng hoặc đưa ra góc nhìn ít quen thuộc hơn. Đây là xu hướng quan sát được, không có nghĩa temperature thấp luôn cho cùng một câu trả lời.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**

Mình sẽ bắt đầu với temperature khoảng 0.1–0.3 cho chatbot hỗ trợ khách hàng vì tác vụ này cần câu trả lời nhất quán, rõ ràng và ít biến động giữa các lần gọi. Tuy nhiên, temperature thấp không bảo đảm câu trả lời đúng về mặt sự thật, nên chatbot vẫn cần kết hợp với nguồn tri thức đáng tin cậy, RAG hoặc bước kiểm tra đầu ra.


### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
- Ước tính chi phí: GPT-4o đắt hơn GPT-4o-mini khoảng ~16.7 lần (Output: $0.010 vs $0.0006 / 1K tokens). Với 10.5 triệu output token/ngày, GPT-4o-mini tốn khoảng $6.3/ngày trong khi GPT-4o tốn $105/ngày (chênh lệch gần $3,000/tháng).
- Trường hợp GPT-4o xứng đáng: Các bài toán suy luận phức tạp, độ rủi ro cao như trợ lý pháp lý/tài chính, code agent, giải quyết logic đa bước nơi chi phí sửa sai vượt xa chi phí API.
- Trường hợp nên dùng mini: Tác vụ tần suất lớn, độ phức tạp thấp như phân loại ý định (router), trích xuất JSON, tóm tắt đoạn văn ngắn hoặc chatbot FAQ/CSKH kết hợp RAG.
---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)

Phản hồi dành cho trẻ 8 tuổi ngắn hơn, dùng từ đơn giản và giải thích blockchain bằng hình ảnh một cuốn sổ gồm các trang được nối tiếp nhau. Phản hồi dành cho chuyên gia tài chính dài và chuyên sâu hơn, sử dụng các thuật ngữ như hàm băm, chữ ký mật mã, Proof of Work và Proof of Stake. System prompt đã làm thay đổi đối tượng người đọc, mức độ chi tiết, từ vựng và cách trình bày của mô hình, dù câu hỏi của người dùng không thay đổi.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> **1. Đoạn văn tiếng Việt thực nghiệm (~100 từ):**
> *"Trí tuệ nhân tạo đang phát triển với tốc độ nhanh chóng và tạo ra những thay đổi sâu sắc trong mọi lĩnh vực của đời sống xã hội. Từ y tế, giáo dục cho đến tài chính và sản xuất, các mô hình ngôn ngữ lớn đang hỗ trợ con người xử lý khối lượng thông tin khổng lồ. Việc ứng dụng công nghệ này giúp tiết kiệm thời gian, tối ưu hóa năng suất và giảm thiểu các sai sót trong công việc hàng ngày. Tuy nhiên, sự phát triển này cũng đặt ra nhiều thách thức lớn về an toàn thông tin, quyền riêng tư và đạo đức. Do đó, việc xây dựng quy chuẩn là vô cùng cấp thiết."*
>
> **2. So sánh kết quả:**
> - **Số từ thực tế:** 104 từ (đo bằng `len(text.split())`).
> - **Ước lượng Part 1 (`số từ / 0.75`):** $104 / 0.75 \approx 139$ token.
> - **Số token thực tế theo `count_tokens` (tiktoken):**
>   - Với model `gpt-4o` (bộ mã hóa `o200k_base`): **158 token** (~1.52 token/từ).
>   - *(Nếu dùng model `gpt-4` với bộ mã hóa cũ `cl100k_base`: **188 token**, ~1.81 token/từ).*
> - **Tỷ lệ chênh lệch:**
>   $$\frac{|158 - 139|}{139} \times 100\% \approx 13.7\%$$
>   *(Nếu so với `cl100k_base` thì chênh lệch lên đến $\frac{|188 - 139|}{139} \times 100\% \approx 35.3\%$).*
>   $\rightarrow$ Số token thực tế luôn **lớn hơn đáng kể** so với công thức ước lượng thô $số\ từ / 0.75$.
>
> **3. Vì sao tiếng Việt tốn nhiều token hơn tiếng Anh cùng độ dài?**
> - **Tập dữ liệu huấn luyện (Training Corpus) thiên lệch:** Các thuật toán tokenizer (như BPE — Byte-Pair Encoding) xây dựng bộ từ điển (vocabulary) dựa trên tần suất xuất hiện trong tập ngữ liệu tiền huấn luyện khổng lồ, vốn chiếm hơn 80–90% là tiếng Anh. Do đó, hầu hết các từ nguyên vẹn của tiếng Anh đều có sẵn trong từ điển thành 1 token, trong khi tiếng Việt chiếm tỷ trọng nhỏ nên ít từ vựng nguyên vẹn hơn.
> - **Ký tự có dấu thanh và mã hóa UTF-8:** Tiếng Việt sử dụng hệ thống chữ cái có dấu thanh và dấu phụ (ă, â, đ, ê, ô, ơ, ư và các dấu sắc, huyền, hỏi, ngã, nặng). Trong mã hóa UTF-8, ký tự ASCII tiếng Anh chỉ tốn 1 byte, còn ký tự tiếng Việt có dấu tốn từ 2 đến 3 bytes. Khi một từ tiếng Việt không nằm trọn vẹn trong từ điển của tokenizer, BPE buộc phải chia cắt từ đó thành nhiều subwords hoặc từng byte riêng lẻ (ví dụ từ *"nghiệm"*, *"triển"* có thể tốn 2–3 token), khiến số token đội lên nhanh chóng.
> - **Đặc trưng ngôn ngữ đơn lập:** Tiếng Việt là ngôn ngữ đơn lập, các âm tiết tách rời nhau bằng khoảng trắng (ví dụ *"trí tuệ nhân tạo"* gồm 4 âm tiết/từ đơn, trong khi tiếng Anh *"artificial intelligence"* chỉ gồm 2 từ đơn). Khi tính bằng số từ hay số ký tự, tiếng Việt cần nhiều đơn vị từ vựng hơn để diễn đạt cùng một lượng thông tin ngữ nghĩa.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
Streaming quan trọng nhất khi mô hình tạo câu trả lời dài hoặc có độ trễ cao, vì người dùng có thể đọc nội dung ngay khi từng phần được sinh ra thay vì chờ toàn bộ kết quả hoàn tất; điều này giúp hệ thống có cảm giác phản hồi nhanh và phù hợp với chatbot, trợ lý viết nội dung. 
Non-streaming phù hợp hơn khi kết quả ngắn, cần xử lý toàn bộ trước khi hiển thị, cần kiểm tra tính hợp lệ, hoặc khi ứng dụng chỉ tiếp tục sau khi nhận được một phản hồi hoàn chỉnh. Non-streaming có thể sử dụng trong trường hợp ta không cần stream text, mà ta chỉ cần câu trả lời cuối cùng để làm input cho agent trong Agentic AI
### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**

Exponential backoff tăng dần thời gian chờ sau mỗi lần thất bại, nhờ đó giảm nhanh số request gửi đến API đang quá tải và tạo thời gian để hệ thống phục hồi, trong khi delay cố định vẫn duy trì áp lực retry ở một tần suất đều đặn. Nếu hàng nghìn client cùng retry sau một khoảng cố định giống nhau, chúng có thể đồng loạt gửi lại request tại cùng thời điểm, tạo ra các đợt tải tăng vọt gọi là “thundering herd” và khiến API tiếp tục quá tải. Vì vậy, exponential backoff thường được kết hợp với jitter(thêm một khoảng chờ ngẫu nhiên) để phân tán các lần retry.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
Tôi chọn persona **trợ lý học tập thân thiện dành cho sinh viên mới học lập trình**.

**System prompt:**  
“Bạn là một trợ lý học tập thân thiện, kiên nhẫn và chính xác. Hãy trả lời bằng tiếng Việt, ngắn gọn, dễ hiểu và ưu tiên ví dụ thực tế. Khi giải thích thuật ngữ kỹ thuật, không giả định người dùng đã có kiến thức nền. Nếu không chắc chắn, hãy nói rõ thay vì tự suy đoán.”

Tôi yêu cầu **“trả lời bằng tiếng Việt”** để nội dung nhất quán và dễ tiếp cận với người học Việt Nam. Cụm từ **“ngắn gọn, dễ hiểu”** giúp hạn chế câu trả lời lan man, trong khi yêu cầu **“không giả định kiến thức nền”** khiến trợ lý giải thích phù hợp hơn với người mới.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
Hạn chế lớn nhất hiện tại là trợ lý chỉ lưu ba lượt hội thoại gần nhất, nên có thể quên mục tiêu hoặc thông tin quan trọng được đề cập trước đó. Một cải thiện cụ thể là bổ sung bộ nhớ hội thoại dạng tóm tắt: khi lịch sử vượt quá ba lượt, hệ thống dùng mô hình tạo một bản tóm tắt ngắn chứa các thông tin quan trọng như mục tiêu, sở thích và vấn đề đang xử lý. Bản tóm tắt này được lưu riêng và gửi kèm system prompt cùng ba lượt gần nhất trong mỗi request. Cách này giúp duy trì ngữ cảnh dài hạn mà không làm số lượng token tăng quá nhiều.
---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
