# Day 17 Submission — Cá nhân (Phiên bản B — BTVN)

**Student:** [Tên học viên]
**Date:** [Ngày]
**Product idea:** Chatbot AI đọc và giải thích hợp đồng lao động từ công ty nước ngoài cho fresher và mid-level tech worker tại Việt Nam — trước khi họ ký.

---

## Reflection: Từ A → B, tôi đã thay đổi gì và tại sao

Sau khi chạy PRD Stress-Test Prompt với AI, tôi nhận ra 3 điểm cần sửa thực chất:

**1. In-Scope vẫn còn thừa.** AI chỉ ra tính năng "Gợi ý câu hỏi gửi HR" là nice-to-have, không phải bắt buộc để test hypothesis cốt lõi (willingness to pay). Người dùng có thể tự soạn câu hỏi nếu họ đã hiểu điều khoản — tính năng này là polish, không phải core. Phiên bản B chuyển nó sang Out-of-Scope và giữ In-Scope còn 2 tính năng thực sự thiết yếu. Điều này cũng làm Hypothesis Table gọn hơn và focus hơn.

**2. Fallback UX trigger còn mơ hồ.** "Confidence score thấp" không phải trigger — đó là input. Trigger phải là hành động cụ thể hệ thống thực hiện. Phiên bản B viết lại trigger theo dạng: "Khi [điều kiện đo được cụ thể] xảy ra, hệ thống [làm gì cụ thể]."

**3. Aha Moment metric chưa đủ leading.** "Copy-to-action rate" đo hành động nhưng không đo được outcome thực (người dùng có gửi câu hỏi cho HR không, và có nhận được kết quả tốt hơn không). Phiên bản B thêm một proxy metric gần hơn với outcome thực: tỷ lệ người dùng quay lại review HĐ thứ hai trong vòng 30 ngày — vì nếu họ thấy giá trị thực, họ sẽ giới thiệu cho bạn bè hoặc dùng lại khi nhận offer khác.

---

## 1. MVP Boundary Sheet

**Riskiest Assumption:**
> Fresher và mid-level tech worker sẵn sàng trả tiền (99k–149k/lần) để có một công cụ review hợp đồng chuyên biệt — thay vì tự dùng ChatGPT miễn phí hoặc hỏi bạn bè.

**In-Scope** (2 tính năng — sau khi cắt từ 3):

- [x] **Upload và phân tích hợp đồng** — người dùng upload file PDF/Word, AI đọc và trả về danh sách điều khoản rủi ro được highlight kèm giải thích bằng tiếng Việt thường ngày — test giả định: *người dùng cần công cụ đọc HĐ cụ thể của mình, không phải tra cứu điều luật chung chung*
- [x] **Flag điều khoản bất thường theo luật lao động Việt Nam** — AI đánh dấu các điều khoản có dấu hiệu vi phạm BLLĐ 2019 hoặc bất lợi đáng kể (IP quá rộng, non-compete không giới hạn địa lý, thử việc vượt 60 ngày), kèm giải thích ngắn "điều này có nghĩa là gì với bạn trong thực tế" — test giả định: *người dùng cần biết điều khoản nào cụ thể là vấn đề và tại sao, không chỉ cảm giác "có gì đó lạ"*

**Out-of-Scope:**

- **Gợi ý câu hỏi / yêu cầu sửa đổi để gửi HR** — *[chuyển từ In-Scope sang đây sau AI critique]* người dùng có thể tự soạn câu hỏi khi đã hiểu điều khoản; đây là polish không phải core MVP
- **Chat hỏi đáp tự do sau khi review** — tốn context window, làm phức tạp UX; người dùng cần kết quả nhanh hơn là chat dài
- **So sánh hợp đồng với template chuẩn ngành** — cần data template lớn, không cần thiết để test willingness to pay
- **Lưu lịch sử review để so sánh nhiều offer** — tính năng retention tốt nhưng không cần cho lần dùng đầu tiên
- **Hỗ trợ UI tiếng Anh** — MVP output tiếng Việt là đủ; UI tiếng Anh để sau

**Non-Goals:**

- **Đưa ra kết luận pháp lý có trách nhiệm pháp lý** — sản phẩm không thay thế luật sư; không bao giờ viết "điều này vi phạm pháp luật" mà luôn là "điều này có dấu hiệu bất thường, bạn nên xác nhận thêm"
- **Soạn thảo hoặc chỉnh sửa hợp đồng cho bên tuyển dụng** — không build công cụ cho B2B (công ty soạn HĐ) trong bất kỳ giai đoạn nào của MVP
- **Xử lý tranh chấp lao động đã xảy ra** — sản phẩm chỉ phục vụ giai đoạn trước khi ký, không phải sau khi đã có vấn đề

---

## 2. PRD Skeleton

### Problem Statement

> Fresher và mid-level tech worker tại Việt Nam nhận offer từ công ty nước ngoài thường phải ký hợp đồng chứa các điều khoản IP assignment, non-compete, và ESOP phức tạp mà họ không đủ nền tảng pháp lý để đánh giá — trong 2–5 ngày ra quyết định — dẫn đến ký trong trạng thái không biết mình đang từ bỏ quyền gì, hoặc bỏ lỡ cơ hội negotiate điều khoản tốt hơn trước khi deadline qua đi.

### Target User

> Developer, designer, hoặc data analyst tại Việt Nam, 22–30 tuổi, vừa nhận offer từ công ty nước ngoài hoặc startup có vốn ngoại lần đầu hoặc lần thứ hai. Đủ kỹ năng chuyên môn để làm việc trong môi trường quốc tế, nhưng chưa từng xử lý hợp đồng lao động có điều khoản pháp lý phức tạp và không có luật sư trong mạng lưới cá nhân. Deadline ký thường là 2–5 ngày kể từ khi nhận offer.

*(Nối trực tiếp từ Customer Segment Card — Day 16 Version B)*

### User Stories

**Story 1:**
> As a fresher developer vừa nhận offer từ một startup Singapore có văn phòng tại TP.HCM, I want upload file hợp đồng và nhận về danh sách điều khoản rủi ro được giải thích bằng tiếng Việt trong vòng 5 phút, so that tôi biết chính xác điều khoản nào cần hỏi lại HR trước khi ký — thay vì ký trong trạng thái không chắc chắn và hối hận sau này.

**Story 2:**
> As a mid-level designer đang cân nhắc offer từ một agency Nhật Bản, I want xem AI giải thích điều khoản IP assignment trong hợp đồng của tôi — cụ thể là nó ảnh hưởng thế nào đến side project cá nhân tôi đang làm — so that tôi quyết định được liệu cần yêu cầu carve-out trước khi ký hay không, trong 48 giờ trước khi deadline offer hết hạn.

### AI-Specific

**Model Selection:**
- **Model:** Claude claude-sonnet-4-6 (primary); fallback sang GPT-4o nếu benchmark cho thấy cost/accuracy tốt hơn cho use-case cụ thể này
- **Lý do chọn:** Hợp đồng lao động từ công ty nước ngoài thường là PDF 10–30 trang, song ngữ Anh-Việt, với cấu trúc pháp lý phức tạp. Cần model có context window lớn (≥100k tokens) và khả năng hiểu văn bản pháp lý tốt. Claude Sonnet đáp ứng cả hai ở mức latency và cost hợp lý hơn Opus cho tác vụ này.
- **Trade-offs chấp nhận:** Latency 10–20 giây cho file dài — chấp nhận được vì đây là tác vụ async (người dùng không cần real-time); có thể hiển thị progress indicator và stream kết quả theo từng điều khoản
- **Trade-offs không chấp nhận:** Hallucination về điều khoản pháp lý cụ thể (bịa ra điều luật không tồn tại, hoặc giải thích sai nghĩa một điều khoản theo hướng có hại cho người dùng) — đây là rủi ro cao nhất và là lý do chính cần Fallback UX nghiêm túc

**Data Requirements:**
- **Nguồn dữ liệu:**
  - (1) *Knowledge base pháp lý:* Bộ Luật Lao Động 2019 + Nghị định 145/2020/NĐ-CP hướng dẫn + các nghị định liên quan — text tĩnh, được cấu trúc hóa thành vector database để RAG; không cần fine-tune
  - (2) *Pattern library:* ~100 điều khoản mẫu phổ biến trong HĐ công ty nước ngoài tại Việt Nam (IP assignment, non-compete, ESOP, probation) — do team curate thủ công từ các mẫu HĐ thu thập được (với sự đồng ý); dùng để guide AI nhận diện pattern
  - (3) *File HĐ người dùng upload:* Xử lý in-session, không lưu sau khi session kết thúc
- **Owner:** Team sản phẩm owns knowledge base và pattern library; người dùng owns file HĐ của họ và có quyền xóa bất kỳ lúc nào
- **Update frequency:** Knowledge base luật: update khi có văn bản pháp lý mới (thường 1–2 lần/năm); Pattern library: review hàng quý dựa trên điều khoản mới xuất hiện trong feedback người dùng
- **Rủi ro data quality:** BLLĐ 2019 là văn bản chính thức, rủi ro thấp. Pattern library: rủi ro bias nếu sample HĐ không đủ đa dạng (thiếu HĐ từ các ngành hoặc quốc gia ít phổ biến hơn) → plan: ghi rõ coverage hiện tại và disclaimer khi điều khoản nằm ngoài pattern đã biết

**Fallback UX:**

*Chiến lược tổng: Human-in-the-loop làm primary + Expectation Management làm baseline constant*

| Trigger | Điều kiện đo được | Hành động hệ thống | User options |
|---|---|---|---|
| **File không đọc được** | PyMuPDF không extract được text (file scan, ảnh chụp, hoặc bị encrypt) | Hiển thị: "File này ở dạng ảnh — tôi không đọc được text tự động. Bạn có thể copy-paste nội dung điều khoản cần hỏi vào đây." + text input field | Copy-paste thủ công hoặc upload file khác |
| **Điều khoản nằm ngoài knowledge base** | Điều khoản không match với bất kỳ pattern nào trong library VÀ không tìm được điều luật liên quan trong knowledge base sau 2 lượt retrieval | Hiển thị kết quả phân tích sơ bộ + banner vàng: "Điều khoản này hiếm gặp trong dữ liệu của chúng tôi. Đây là giải thích sơ bộ — nên tham khảo thêm ý kiến từ người có kinh nghiệm pháp lý trước khi dùng làm căn cứ negotiate." | Chấp nhận kết quả sơ bộ hoặc bấm "Report — điều này sai" |
| **Nội dung liên quan đến tranh chấp đã xảy ra** | Prompt chứa keyword pattern: "đã ký", "đã xảy ra", "đang tranh chấp", "công ty đang vi phạm" | Hiển thị: "Bạn có vẻ đang hỏi về tình huống đã xảy ra sau khi ký — công cụ này chỉ hỗ trợ review trước khi ký. Nếu đang có tranh chấp lao động, bạn cần liên hệ luật sư hoặc Phòng LĐTBXH tại địa phương." | Thoát hoặc hỏi về HĐ mới khác |

*Disclaimer cố định (Expectation Management — hiển thị ở đầu trang kết quả, không thể dismiss):*
> ⚠️ *Đây là phân tích sơ bộ dựa trên BLLĐ 2019 và các pattern phổ biến — không phải tư vấn pháp lý chính thức và không có giá trị pháp lý. Với điều khoản quan trọng, hãy xác nhận thêm với luật sư trước khi ký.*

*User override:* Người dùng có thể bấm "Điều này sai hoặc thiếu" trên bất kỳ điều khoản nào → report ghi vào queue review của team, không tự động thay đổi kết quả

### Success Metrics

- **Primary metric:** Copy-to-action rate — % session hoàn thành review (đọc đến cuối trang kết quả) có ít nhất 1 lần click "Copy" hoặc highlight nội dung
- **Secondary metric:** Return rate trong 30 ngày — % người dùng đã dùng ít nhất 1 lần quay lại dùng lần thứ 2 (proxy cho "giới thiệu cho bạn bè" hoặc "nhận offer mới")
- **Ngưỡng thành công:** Copy-to-action rate ≥40% trong 4 tuần đầu; Return rate ≥15% trong 30 ngày
- **Timeframe đo lường:** 4–6 tuần sau khi có ≥200 lượt review hoàn chỉnh

### Dependencies & Constraints

- **API cần tích hợp:** Anthropic API (Claude claude-sonnet-4-6); thư viện xử lý PDF (PyMuPDF); vector database (Pinecone hoặc Chroma) để RAG knowledge base pháp lý
- **Legal constraint quan trọng nhất:** Định vị là "công cụ tra cứu và giáo dục pháp luật lao động" — không phải "tư vấn pháp lý". Cần tham vấn luật sư để xác nhận ranh giới này trước khi launch công khai. Disclaimer phải rõ ràng và không thể ẩn.
- **Data privacy:** File HĐ của người dùng không được lưu sau session; cần ghi rõ trong privacy policy và hiển thị ngay tại màn hình upload
- **Timeline constraint:** MVP có thể build trong 3–4 tuần nếu giới hạn: text extraction + RAG call + LLM call + static React frontend; không cần user account hay backend phức tạp giai đoạn đầu
- **Budget constraint:** Chi phí API ước tính ~2k–5k VNĐ/lần review (hợp đồng 10–30 trang); với giá 99k–149k/lần → margin đủ để sustainable ngay từ đầu nếu có conversion

---

## 3. Hypothesis Table

### Hypothesis 1 — Core value: Upload và hiểu hợp đồng

> "Chúng tôi tin rằng tính năng upload file hợp đồng và nhận về danh sách điều khoản rủi ro được giải thích bằng tiếng Việt thường ngày sẽ giúp fresher và mid-level tech worker tại Việt Nam hiểu được nội dung hợp đồng lao động từ công ty nước ngoài đủ để tự xác định câu hỏi cần hỏi lại HR trước khi ký. Chúng tôi sẽ biết mình đúng khi thấy Copy-to-action rate ≥40% trong 4 tuần đầu với ít nhất 200 lượt review hoàn chỉnh."

**Riskiest assumption:** Người dùng tin tưởng kết quả của AI đủ để dùng nó làm cơ sở hành động thực tế (hỏi HR, negotiate, hoặc từ chối ký) — chứ không chỉ đọc cho biết rồi ký như cũ.

**Cách test cheapest:** Chạy Wizard of Oz MVP trong 2–3 tuần — nhận file HĐ qua Telegram bot, phân tích thủ công bởi người có hiểu biết pháp lý cơ bản, trả kết quả trong vòng 24h. Charge 49k–99k/lần. Nếu ≥30% người dùng Wizard of Oz version thực hiện hành động sau khi nhận kết quả (hỏi HR, gửi phản hồi về kết quả) → signal đủ để build AI version.

---

### Hypothesis 2 — Willingness to pay

> "Chúng tôi tin rằng fresher và mid-level tech worker sẵn sàng trả 99k–149k cho một lần review hợp đồng chuyên biệt thay vì tự dùng ChatGPT. Chúng tôi sẽ biết mình đúng khi ≥20% người truy cập trang landing page chuyển sang bước thanh toán trong 2 tuần đầu chạy paid test với ít nhất 200 lượt visit."

**Riskiest assumption:** Người dùng cảm thấy sản phẩm này đủ khác biệt với "hỏi ChatGPT miễn phí" để chi tiền — tức là họ nhận ra giá trị của việc có knowledge base pháp lý Việt Nam được cấu trúc hóa, thay vì chỉ thấy "một AI khác".

**Cách test cheapest:** Landing page + bảng giá thật → đo click-to-payment rate trước khi build full product. Nếu <20% → cần pivot pricing hoặc value proposition, không phải build thêm tính năng.

---

## 4. PMF Scorecard

**Aha Moment:**
> Khoảnh khắc người dùng đọc giải thích của AI về một điều khoản trong hợp đồng của họ và lần đầu tiên nói ra — hoặc hành động theo — "À, điều này có nghĩa là XYZ trong thực tế — tôi phải hỏi lại điều này."
>
> **Hành vi đo được phản ánh khoảnh khắc này:** Người dùng copy ít nhất một đoạn từ trang kết quả review (điều khoản được giải thích, hoặc gợi ý hành động) trong cùng session với lần review đầu tiên — trong vòng 10 phút kể từ khi kết quả hiển thị.

**Actionable Metric:**
> **Copy-to-action rate** = (Số session có ít nhất 1 lần click Copy hoặc highlight text) / (Tổng số session đọc đến cuối trang kết quả) × 100%
>
> Cách đo: Track click event trên nút "Copy" từng điều khoản + clipboard copy event trên toàn trang kết quả; chia cho số session "completed review" (định nghĩa: scroll đến ≥80% chiều dài trang kết quả)
>
> **Secondary — Return rate D30** = % người dùng đã dùng ít nhất 1 lần quay lại trong 30 ngày — đây là proxy gần nhất với "người dùng nhận được giá trị thực và muốn dùng lại hoặc giới thiệu cho người khác"

**PMF Method:**
> **Sean Ellis Test** — ngưỡng: >40% "Very disappointed"
>
> Kết hợp với **Aha Moment tracking** — ngưỡng: ≥40% người dùng active (dùng ít nhất 1 lần) đạt Copy-to-action trong session đầu tiên
>
> Thời điểm đo PMF lần đầu: Sau 6–8 tuần kể từ khi có ≥200 người dùng đã hoàn thành ít nhất 1 review

**Vanity Metrics tôi sẽ không dùng:**
- Số lượt download / số tài khoản đăng ký — không cho biết ai thực sự nhận được giá trị
- Số lượt upload file — người dùng có thể upload rồi không đọc kết quả
- Thời gian trung bình trên trang — tăng vì UX tệ cũng được tính là thành công
- Số lượt share trên mạng xã hội — không đo được impact với quyết định ký HĐ thực tế

---

## 5. AI Critique Log

**Prompt đã dùng:** PRD Stress-Test Prompt (Section 4.1 của handbook)

**Điểm AI chỉ ra và quyết định của tôi:**

1. **[Scope Creep] "Gợi ý câu hỏi gửi HR" là Out-of-Scope, không phải In-Scope** — AI chỉ ra rằng để test hypothesis cốt lõi (người dùng có sẵn sàng trả tiền không), chỉ cần họ hiểu điều khoản là đủ; tính năng gợi ý câu hỏi là polish thêm vào sau khi đã có PMF
   - **Action: Accept** — đã chuyển tính năng này sang Out-of-Scope trong Phiên bản B; giữ In-Scope còn 2 tính năng

2. **[AI Fallback Holes] Trigger "confidence score thấp" không đủ cụ thể để implement** — AI chỉ ra rằng "confidence score thấp" không phải trigger mà là input; trigger phải là điều kiện đo được cụ thể mà engineer có thể code
   - **Action: Accept** — đã viết lại toàn bộ Fallback UX table với 3 trigger rõ ràng (file không đọc được, điều khoản ngoài knowledge base, nội dung tranh chấp đã xảy ra) kèm điều kiện đo được cụ thể và hành động cụ thể

3. **[Vanity Metric Trap] Copy-to-action rate có thể bị inflate nếu người dùng copy vì tò mò, không phải vì sẽ dùng** — AI gợi ý thêm Return rate D30 như một metric khó inflate hơn
   - **Action: Partial Accept** — giữ Copy-to-action rate làm primary (dễ đo sớm, đủ để iterate trong 4–6 tuần đầu) và thêm Return rate D30 làm secondary metric để validate thực chất hơn

4. **[Hypothesis Weakness] Hypothesis 1 không tách biệt được "người dùng hiểu hợp đồng" với "người dùng tin AI đủ để hành động theo"** — đây là 2 giả định khác nhau, có thể cần 2 hypothesis riêng
   - **Action: Reject** — giữ 1 hypothesis nhưng làm rõ "Riskiest assumption" là vế thứ hai (người dùng tin và hành động theo), không phải vế đầu (người dùng hiểu). Tách thành 2 hypothesis sẽ làm scope quá rộng cho MVP.

**Thay đổi lớn nhất giữa Version A và Version B:**
> In-Scope từ 3 tính năng xuống còn 2, và Fallback UX từ mô tả chung chung sang table với trigger + điều kiện đo được + hành động cụ thể cho từng scenario. Đây là hai thay đổi có impact nhất đến khả năng engineer đọc và implement được PRD này mà không cần hỏi lại quá 3 câu.

---

## 6. Self-assessment

**Mắt xích nào vẫn yếu nhất sau Phiên bản B:**
> **Data Requirements** — tôi đã xác định được nguồn (BLLĐ 2019 + Pattern library), nhưng chưa trả lời được: làm thế nào để collect ~100 HĐ mẫu từ công ty nước ngoài để build pattern library ban đầu mà không vi phạm privacy và confidentiality? Đây là blocker thực tế trước khi build — và chưa có plan cụ thể.

**Open questions muốn giải đáp tiếp:**

1. **Data collection cho pattern library:** Nguồn hợp pháp nào để thu thập mẫu HĐ công ty nước ngoài tại Việt Nam — cộng đồng chia sẻ tự nguyện, partner với luật sư lao động, hay bắt đầu từ mẫu HĐ công khai?
2. **Wizard of Oz MVP thực tế:** Nếu chạy thủ công trước, ai là người "đóng vai AI" — người có đủ hiểu biết pháp lý cơ bản để review HĐ mà không cần luật sư full-time?
3. **Legal clearance:** Cần tham vấn luật sư để xác nhận ranh giới giữa "giáo dục pháp luật" và "tư vấn pháp lý" — bước này nên làm trước hay sau khi có Wizard of Oz data?
