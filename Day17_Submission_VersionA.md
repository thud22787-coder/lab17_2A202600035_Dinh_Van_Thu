# Day 17 Submission — Cá nhân (Phiên bản A — End-of-session)

**Student:** Đinh Văn Thư - 2A202600035
**Date:** 24/4/2026
**Product idea:** Chatbot AI đọc và giải thích hợp đồng lao động từ công ty nước ngoài cho fresher và mid-level tech worker tại Việt Nam — trước khi họ ký.

---

## 1. MVP Boundary Sheet

**Riskiest Assumption:**

> Fresher và mid-level tech worker sẵn sàng trả tiền (99k–149k/lần) để có một công cụ review hợp đồng chuyên biệt — thay vì tự dùng ChatGPT miễn phí hoặc hỏi bạn bè.

**In-Scope** (tối đa 3):

- [x] **Upload và phân tích hợp đồng** — người dùng upload file PDF/Word, AI đọc và trả về danh sách điều khoản rủi ro được highlight kèm giải thích bằng tiếng Việt thường ngày — test giả định: _người dùng cần công cụ đọc HĐ cụ thể của mình, không phải tra cứu điều luật chung chung_
- [x] **Flag điều khoản bất thường theo luật lao động Việt Nam** — AI đánh dấu các điều khoản có dấu hiệu vi phạm BLLĐ 2019 hoặc bất lợi đáng kể (IP quá rộng, non-compete không giới hạn, thử việc vượt 60 ngày) — test giả định: _người dùng cần biết điều khoản nào cụ thể là vấn đề, không chỉ cảm giác "có gì đó lạ"_
- [x] **Gợi ý câu hỏi / yêu cầu sửa đổi để gửi HR** — sau khi flag, AI đề xuất 2–3 câu người dùng có thể gửi lại HR để hỏi hoặc negotiate — test giả định: _người dùng cần output có thể dùng ngay, không chỉ là thông tin_

**Out-of-Scope:**

- **So sánh hợp đồng với template chuẩn ngành** — giá trị tốt nhưng cần data template, không cần thiết để test willingness to pay
- **Chat hỏi đáp tự do sau khi review** — tốn context window, làm phức tạp UX; người dùng cần kết quả nhanh hơn là chat dài
- **Lưu lịch sử review để so sánh nhiều offer** — tính năng retention tốt nhưng không cần cho lần dùng đầu tiên
- **Hỗ trợ tiếng Anh hoàn toàn** — MVP đọc HĐ tiếng Anh nhưng output vẫn là tiếng Việt; UI tiếng Anh để sau
- **Review hợp đồng freelance hoặc hợp đồng dịch vụ** — vertical khác, không test được hypothesis hiện tại

**Non-Goals:**

- **Đưa ra kết luận pháp lý có trách nhiệm** — sản phẩm không phải luật sư, không thay thế tư vấn pháp lý chính thức; không viết "điều này vi phạm pháp luật" mà viết "điều này có dấu hiệu bất thường, bạn nên hỏi lại"
- **Soạn thảo hoặc chỉnh sửa hợp đồng** — không build công cụ cho bên soạn thảo (công ty) trong giai đoạn này
- **Tích hợp với hệ thống HR hoặc ATS** — B2B hoàn toàn nằm ngoài phạm vi MVP

---

## 2. PRD Skeleton

### Problem Statement

> Fresher và mid-level tech worker tại Việt Nam nhận offer từ công ty nước ngoài thường phải ký hợp đồng chứa các điều khoản IP, non-compete, và ESOP phức tạp mà họ không đủ nền tảng pháp lý để đánh giá — trong 2–5 ngày ra quyết định — dẫn đến ký mà không biết mình đang từ bỏ quyền gì, hoặc bỏ lỡ cơ hội negotiate điều khoản tốt hơn.

### Target User

> Developer, designer, hoặc data analyst tại Việt Nam, 22–30 tuổi, vừa nhận offer từ công ty nước ngoài hoặc startup có vốn ngoại lần đầu hoặc lần thứ hai. Họ đủ kỹ năng để làm việc trong môi trường quốc tế, nhưng chưa từng có kinh nghiệm xử lý hợp đồng lao động phức tạp và không có luật sư trong mạng lưới cá nhân. Deadline ký thường là 2–5 ngày kể từ khi nhận offer.

### User Stories

**Story 1:**

> As a fresher developer vừa nhận offer từ một startup Singapore có văn phòng tại TP.HCM, I want upload file hợp đồng và nhận về danh sách điều khoản cần chú ý được giải thích bằng tiếng Việt trong vòng 5 phút, so that tôi biết chính xác cần hỏi lại HR điều gì trước khi ký — thay vì ký trong trạng thái không chắc chắn.

**Story 2:**

> As a mid-level designer đang cân nhắc chuyển từ công ty Việt Nam sang một agency Nhật Bản, I want xem AI giải thích điều khoản IP assignment trong hợp đồng mới và so sánh với mức bình thường, so that tôi quyết định được liệu có cần yêu cầu carve-out cho side project cá nhân không trước deadline offer hết hạn.

### AI-Specific

**Model Selection:**

- **Model:** Claude claude-sonnet-4-6 (hoặc GPT-4o tùy cost benchmark)
- **Lý do chọn:** Hợp đồng lao động từ công ty nước ngoài thường là văn bản pháp lý tiếng Anh dài 10–30 trang với cấu trúc phức tạp — cần model có context window lớn (≥100k tokens) và khả năng hiểu văn bản pháp lý tốt. Sonnet 4.6 đáp ứng cả hai ở mức chi phí hợp lý hơn Opus.
- **Trade-offs chấp nhận:** Latency 10–20 giây để xử lý file dài — chấp nhận được vì đây là tác vụ async (người dùng không cần real-time như chat)
- **Trade-offs không chấp nhận:** Hallucination về điều khoản pháp lý cụ thể — nếu AI bịa ra một điều luật không tồn tại và người dùng dùng nó để negotiate thì hậu quả nghiêm trọng; phải có fallback rõ ràng

**Data Requirements:**

- **Nguồn dữ liệu:** (1) Bộ Luật Lao Động 2019 và các nghị định hướng dẫn — văn bản tĩnh, cấu trúc hóa thành knowledge base; (2) File hợp đồng do người dùng upload (PDF/Word) — xử lý session-only, không lưu; (3) Thư viện ~50–100 điều khoản pattern phổ biến trong HĐ công ty nước ngoài tại Việt Nam — do team curate thủ công ban đầu
- **Owner:** Team sản phẩm owns knowledge base luật và pattern library; người dùng owns file HĐ của họ
- **Update frequency:** Knowledge base luật — update khi có nghị định mới (không thường xuyên, ~1–2 lần/năm); pattern library — review hàng quý dựa trên feedback người dùng
- **Rủi ro về data quality:** BLLĐ 2019 là văn bản chính thức, ít thay đổi — rủi ro thấp. Pattern library do team curate — rủi ro bias nếu sample không đủ đa dạng theo loại công ty và ngành

**Fallback UX:**

- **Chiến lược:** Human-in-the-loop kết hợp Expectation Management
- **Trigger:** (1) Khi AI không thể extract được cấu trúc điều khoản từ file (file scan, ảnh chụp, hoặc format không chuẩn); (2) Khi một điều khoản không match với bất kỳ pattern nào trong knowledge base — confidence score thấp; (3) Khi nội dung điều khoản liên quan đến tranh chấp đã xảy ra (không phải review trước khi ký)
- **Hành động khi trigger:**
  - Trigger (1): Hiển thị "File này khó đọc tự động — vui lòng upload file text-based hoặc copy-paste nội dung điều khoản cần hỏi vào đây" kèm text input field
  - Trigger (2): Hiển thị kết quả phân tích nhưng thêm banner vàng: "Điều khoản này không phổ biến trong mẫu dữ liệu của chúng tôi. Đây là giải thích sơ bộ — bạn nên tham khảo thêm ý kiến từ người có kinh nghiệm pháp lý."
  - Trigger (3): "Bạn có vẻ đang hỏi về một tình huống đã xảy ra — công cụ này chỉ hỗ trợ review hợp đồng trước khi ký. Nếu đang có tranh chấp, bạn cần liên hệ luật sư."
- **Disclaimer cố định (Expectation Management):** Hiển thị ngay phía trên kết quả: _"Kết quả này là phân tích sơ bộ dựa trên BLLĐ 2019 và các pattern phổ biến — không phải tư vấn pháp lý chính thức. Với điều khoản quan trọng, hãy xác nhận thêm với luật sư."_
- **User options:** Người dùng có thể (a) chấp nhận kết quả và dùng gợi ý, (b) copy điều khoản cụ thể để hỏi thêm, (c) bấm "Điều này sai" để report — feed vào quality loop

### Success Metrics

- **Primary metric:** Tỷ lệ người dùng sử dụng ít nhất một gợi ý "câu hỏi gửi HR" sau khi nhận kết quả review (hành vi copy hoặc click nút "Copy câu này")
- **Ngưỡng thành công:** ≥40% người dùng hoàn thành review thực hiện hành động copy/sử dụng gợi ý — trong 4 tuần đầu sau launch
- **Timeframe đo lường:** 4–6 tuần sau khi ra MVP với ≥200 lượt review hoàn chỉnh

### Dependencies & Constraints

- **API cần tích hợp:** Anthropic API (Claude) hoặc OpenAI API; thư viện xử lý PDF (PyMuPDF hoặc tương đương) để extract text từ file upload
- **Legal constraint:** Cần xác định rõ cách định vị để không bị coi là "hành nghề tư vấn pháp luật không phép" theo Luật Luật sư 2006 — định vị là "công cụ tra cứu và giáo dục pháp luật"
- **Data privacy:** File hợp đồng của người dùng chứa thông tin cá nhân và thông tin công ty — không lưu sau session; cần ghi rõ trong privacy policy
- **Timeline:** MVP có thể build trong 3–4 tuần nếu giới hạn ở text extraction + LLM call + static UI; không cần backend phức tạp giai đoạn đầu

---

## 3. Hypothesis Table

### Hypothesis 1 — Upload và phân tích hợp đồng

> "Chúng tôi tin rằng tính năng upload file hợp đồng và nhận về danh sách điều khoản rủi ro được giải thích bằng tiếng Việt sẽ giúp fresher và mid-level tech worker tại Việt Nam hiểu được nội dung hợp đồng lao động từ công ty nước ngoài đủ để tự đưa ra câu hỏi cho HR. Chúng tôi sẽ biết mình đúng khi thấy ≥60% người dùng hoàn thành toàn bộ luồng (upload → đọc kết quả → đọc ít nhất 3 điều khoản được giải thích) trong vòng 8 tuần đầu sau launch với ít nhất 300 lượt review."

**Riskiest assumption:** Người dùng tin tưởng kết quả đủ để dùng nó làm cơ sở đặt câu hỏi với HR — chứ không chỉ đọc cho biết rồi bỏ qua.

**Cách test cheapest:** Landing page mô tả sản phẩm + form nhận email → gửi cho 30–50 người trong segment → đo tỷ lệ đăng ký và hỏi 5–10 người lý do họ muốn dùng. Nếu lý do chính là "muốn biết mình đang ký cái gì" → hypothesis đúng hướng; nếu là "tò mò xem AI làm được gì" → cần pivot.

---

### Hypothesis 2 — Gợi ý câu hỏi gửi HR

> "Chúng tôi tin rằng tính năng gợi ý câu hỏi / yêu cầu sửa đổi cụ thể để gửi HR sẽ giúp người dùng chuyển từ 'biết có vấn đề' sang 'hành động được ngay' mà không cần biết cách diễn đạt pháp lý. Chúng tôi sẽ biết mình đúng khi thấy ≥40% người dùng hoàn thành review thực hiện hành động copy ít nhất một gợi ý trong 4 tuần đầu."

**Riskiest assumption:** Người dùng thực sự gửi câu hỏi đó cho HR — không chỉ copy cho có. Nếu không gửi, tính năng này có giá trị thông tin nhưng không có impact thực.

**Cách test cheapest:** Sau 2 tuần live, gửi email follow-up cho 20–30 người đã dùng: "Bạn có gửi câu hỏi nào cho HR sau khi review không?" — đây là signal thực nhất mà không cần build thêm gì.

---

## 4. PMF Scorecard

**Aha Moment:**

> Khoảnh khắc người dùng đọc giải thích của AI về một điều khoản trong hợp đồng và lần đầu tiên nói "À, ra điều này có nghĩa là vậy — tôi cần hỏi lại điều này." Hành vi phản ánh khoảnh khắc này: người dùng copy ít nhất một gợi ý câu hỏi gửi HR trong cùng session với lần review đầu tiên.

**Actionable Metric:**

> **"Copy-to-action rate"** — % người dùng hoàn thành review thực hiện ít nhất một hành động copy (gợi ý câu hỏi, đoạn điều khoản, hoặc giải thích) trong cùng session.
> Cách đo: Track click event trên nút "Copy câu này" và "Copy toàn bộ kết quả"; chia cho tổng số session hoàn thành review (đọc đến cuối trang kết quả).

**PMF Method:**

> **Sean Ellis Test** — ngưỡng: >40% "Very disappointed"
> Thời điểm đo lần đầu: Sau 6–8 tuần kể từ khi có ≥200 người dùng active (đã dùng ít nhất 1 lần)

**Vanity Metrics tôi sẽ không dùng:**

- Số lượt đăng ký tài khoản — không phản ánh người dùng thực sự có nhận được giá trị không
- Số lượt upload file — người dùng có thể upload rồi không đọc kết quả
- Thời gian trên trang — có thể tăng vì UX tệ, không phải vì nội dung có giá trị
- Số lượt share trên mạng xã hội — không đo được impact thực với quyết định ký hợp đồng

---

## 5. AI Critique Log

_(Phần này sẽ được điền sau khi chạy PRD Stress-Test Prompt với AI — trước khi nộp Phiên bản B)_

**Điểm AI chỉ ra:**

1. [Chờ kết quả stress-test]
2. [Chờ kết quả stress-test]
3. [Chờ kết quả stress-test]

**Thay đổi lớn nhất giữa Version A và Version B:**

> [Điền sau khi có AI feedback và đã chỉnh sửa]

---

## 6. Self-assessment

**Mắt xích nào yếu nhất:**

> **Fallback UX** — tôi đã mô tả 3 trigger scenario nhưng confidence threshold cho trigger (2) vẫn còn mờ ("không match với bất kỳ pattern nào"). Cần định nghĩa rõ hơn: confidence score được tính bằng cách nào, ngưỡng cụ thể là bao nhiêu, và ai set ngưỡng đó. Đây cũng là phần engineer sẽ hỏi lại nhiều nhất.

**Open questions muốn giải đáp tiếp:**

1. Confidence score cho legal document analysis nên được tính thế nào trong thực tế — dùng self-reported confidence của LLM hay cần layer validation riêng?
2. Privacy constraint cho file hợp đồng (chứa thông tin cá nhân và bí mật công ty) ảnh hưởng thế nào đến khả năng dùng cloud LLM API — có cần on-premise option không?
3. Nếu ITviec/TopDev không partnership, kênh nào để reach người dùng đúng lúc họ nhận offer — không phải sau khi đã ký?

---

> **Ghi chú:** Phiên bản A này là snapshot tư duy cuối buổi học. Phiên bản B sẽ cập nhật sau khi chạy AI Stress-Test và điền đầy đủ AI Critique Log.
