# Memo Teardown — Cursor (Anysphere)

**Họ tên:** Đỗ Lê Việt Anh · **MSV:** 2A202602491

**Vì sao chọn sản phẩm này:** Cursor là sản phẩm AI-native tăng trưởng nhanh nhất trong mảng developer tools. Chỉ trong ~3 năm, nó đi từ một bản fork VS Code của team 5 người đến thương vụ SpaceX mua lại với giá $60B, nên có đủ mốc công khai (changelog, blog, Product Hunt, báo chí). Câu hỏi đáng phân tích nhất của sản phẩm này là: làm sao một "wrapper" trên model của người khác tự xây được moat?

---

## §1. Timeline các cập nhật lớn

**Đọc nhanh:** 8 mốc này là một chuỗi quyết định liên tục.

1. Giành bề mặt làm việc bằng cách fork VS Code.
2. Tự làm model ở chỗ các lab lớn không làm (Tab).
3. Đi theo năng lực model: chuyển sang agent.
4. Bị bóp margin vì phụ thuộc nhà cung cấp model (đổi pricing).
5. Tự làm model frontier (Composer).
6. Mở rộng sang khâu bị nghẽn tiếp theo (review).
7. Đổi đơn vị làm việc từ file sang agent (Cursor 3).
8. Sở hữu luôn lớp compute (SpaceX).

| Thời điểm | Cập nhật (link nguồn gốc) | Context lúc đó | Nguyên lý |
|---|---|---|---|
| **M1** · 10/2023 | **Định vị "AI-native IDE":** fork nguyên VS Code thay vì làm extension. Gọi seed $8M do OpenAI Startup Fund dẫn; ARR > $1M. [TechCrunch](https://techcrunch.com/2023/10/11/anysphere-raises-8m-from-openai-to-build-an-ai-powered-ide) | GPT-4 vừa ra (03/2023). Copilot thống trị nhưng chỉ là extension. VS Code chiếm ~73% dev; Anysphere coi Microsoft là đối thủ chính. Team 5 người. | **x10 + hạ "nỗi lo chuyển đổi" (4 forces).** Extension bị API của VS Code giới hạn nên không thể cho trải nghiệm x10. Fork thì kiểm soát được toàn bộ editor, mà vẫn giữ nguyên extension và phím tắt. User có cái mới mà không mất thói quen cũ. |
| **M2** · 11/2024 | **Mua Supermaven để làm Tab model riêng:** đoán bước sửa tiếp theo, độ trễ thấp. [Cursor blog](https://cursor.com/blog/supermaven) | 10/2024, Copilot mở cho chọn Claude, Gemini và GPT, nên lớp "gọi API + UI" bị hàng hoá hoá. Supermaven nói extension API chặn những gì họ muốn làm. [GitHub blog](https://github.blog/news-insights/product-news/bringing-developer-choice-to-copilot/) | **Wrapper → moat + vòng lặp học.** Tự làm model ở đúng chỗ lab frontier không làm: autocomplete siêu nhanh, đồng thiết kế với UI. Mỗi lần user nhấn Tab hay bỏ qua gợi ý là một tín hiệu để train lại. |
| **M3** · 24/11/2024 | **Agent trong Composer (v0.43):** agent tự chọn context và tự chạy terminal. [Changelog 0.43](https://cursor.com/changelog/0-43-x) | 11 ngày trước, Codeium ra Windsurf với agent Cascade. Claude 3.5 Sonnet đã đủ giỏi tool use để sửa nhiều file. [Maginative](https://www.maginative.com/article/codeium-launches-windsurf-editor-an-agentic-integrated-development-environment/) | **"Nếu model làm được, model sẽ làm" → định nghĩa lại "tốt".** Model đã làm được cả task thì đứng yên ở mức gợi ý từng dòng là bị bỏ lại. Thước đo đổi từ "% gợi ý được accept" sang "số task giao xong". |
| **M4** · 16/06 → 04/07/2025 | **Đổi pricing:** Pro $20 bỏ hạn mức 500 request, chuyển sang $20 usage tính theo giá API; thêm gói Ultra $200. Sau phản ứng dữ dội, Cursor xin lỗi và hoàn tiền phí phát sinh. [Cursor blog](https://cursor.com/blog/june-2025-pricing) | Agent đốt token, task khó tốn gấp hàng chục lần task dễ. Anthropic bán Claude Code với gói $200 tương tự, tức nhà cung cấp model giờ là đối thủ. Cursor vừa gọi Series C $900M. [Simon Willison](https://simonwillison.net/2025/Jul/5/cursor-clarifying-our-pricing) | **Wrapper bị bóp margin bởi nhà cung cấp model.** Giá trị nằm ở model của người khác thì chi phí biên do họ quyết định, nên giá gói buộc phải bám theo token. Đổi pricing cũng chạm thẳng vào lực "nỗi lo" trong 4 forces. |
| **M5** · 29/10/2025 | **Cursor 2.0 + Composer:** model code đầu tiên tự train, nhanh gấp 4 lần model cùng độ thông minh. Giao diện xoay quanh agent, chạy nhiều agent song song. [Cursor blog](https://cursor.com/blog/2-0) | 06/2025, Anthropic cắt quyền truy cập Claude trực tiếp của Windsurf khi có tin OpenAI mua Windsurf, cho thấy rủi ro phụ thuộc là có thật. [The Decoder](https://the-decoder.com/anthropic-cuts-claude-access-for-windsurf-after-openais-3b-takeover-news/) | **Thoát wrapper bằng moat dữ liệu (vòng lặp học).** Cursor có thứ lab frontier không có: hàng triệu phiên code thật, dữ liệu accept/reject, môi trường chạy tool. Họ dùng nó train model chuyên biệt, giảm cả phụ thuộc lẫn chi phí biên (nối tiếp M4). |
| **M6** · 19/12/2025 | **Mua Graphite** (code review, stacked diffs); Graphite vẫn chạy độc lập. [Cursor blog](https://cursor.com/blog/graphite) | Code viết nhanh hơn nhiều, nhưng theo CEO thì review "vẫn như 3 năm trước". GitHub vẫn sở hữu PR; CodeRabbit đang lên. [Fortune](https://fortune.com/2025/12/19/cursor-ai-coding-startup-graphite-competition-heats-up/) | **x10 phải tính trên cả workflow (nút thắt dịch chuyển).** Tăng tốc khâu viết thì khâu review thành nút cổ chai. Muốn x10 thật ở đầu ra (code lên production) thì phải sở hữu cả khâu tiếp theo. Moat từ workflow mạnh hơn moat từ một tính năng. |
| **M7** · 02/04/2026 | **Cursor 3:** workspace lấy agent làm trung tâm. Mọi agent (local, cloud, Slack, GitHub, Linear) nằm chung một sidebar, chuyển qua lại giữa cloud và local. IDE cũ vẫn giữ. [Cursor blog](https://cursor.com/blog/cursor-3) | Composer 2 vừa ra 2 tuần trước. Codex và Claude Code cạnh tranh trực diện. Cursor tự thừa nhận dev đang "quản lý vi mô" từng agent, nhảy giữa nhiều terminal. [IT Brief](https://itbrief.news/story/cursor-3-retools-coding-workspace-around-ai-agents) | **JTBD đổi thì giao diện phải đổi (định nghĩa lại "tốt").** Việc user thuê Cursor đã chuyển từ "viết code" sang "điều phối agent ra kết quả". Cursor vẫn giữ IDE để không phá thói quen của tệp cũ (4 forces). |
| **M8** · 14/08/2026 | **Thuộc về SpaceX** (thương vụ all-stock $60B, công bố 16/06, hoàn tất 14/08). Lý do nêu ra: dùng đội GPU lớn nhất thế giới để có model mạnh hơn, rẻ hơn; Grok 4.6 là ví dụ đầu tiên. [Cursor blog](https://cursor.com/blog/joining-spacex) | ARR khoảng $2B (02/2026). xAI đã nhập vào SpaceX từ 02/2026. Cursor từng từ chối các đề nghị mua từ lab AI. [Bloomberg](https://www.bloomberg.com/news/articles/2026-03-02/cursor-recurring-revenue-doubles-in-three-months-to-2-billion) | **Tích hợp dọc: moat dời xuống lớp compute → model.** Đây là bước cuối của logic M4 → M5: muốn kiểm soát chi phí biên thì sở hữu luôn GPU. Cái giá là mất tính trung lập "dùng model nào cũng được", vốn là sức hút ban đầu. |

**Vì sao chọn những mốc này:**
- Mỗi mốc mình giữ lại đều đổi ít nhất một trong bốn thứ: bề mặt sản phẩm, nguồn model, cách tính tiền, hoặc khâu trong workflow. Mốc sau cũng phải giải thích được bằng hệ quả của mốc trước.
- **Đã loại:**
  - **Các vòng gọi vốn Series A–D:** là sự kiện tài chính, nên chỉ dùng làm context.
  - **Cursor 1.0 (06/2025):** chỉ là nhãn phiên bản; hướng agent đã được quyết từ M3.
  - **Visual Editor, iOS, JetBrains:** mở rộng kênh phân phối, không đổi JTBD hay pricing.
  - **Projects/Rollouts (09/2026):** quá mới, chưa thấy hệ quả, nên dùng làm dữ liệu cho §3.

---

## §2. Tệp user & JTBD

| | Early adopters (2023–2024) | Tệp hiện tại – lõi (2025–2026) |
|---|---|---|
| **Đặc điểm** | Full-stack dev ở startup 2–10 người hoặc indie hacker. Dùng VS Code, đã trả tiền Copilot và ChatGPT Plus, hay copy code qua lại giữa ChatGPT và editor, theo dõi tin AI trên X/HN. Tự quyết công cụ, tự trả $20 bằng thẻ cá nhân. ([TechCrunch 2023](https://techcrunch.com/2023/10/11/anysphere-raises-8m-from-openai-to-build-an-ai-powered-ide): khi đó Cursor tập trung vào cá nhân và team nhỏ) | Engineering manager hoặc platform lead ở công ty hàng nghìn kỹ sư (Nokia, Grab). Mua cho cả tổ chức, cần SSO, rule chung, chứng nhận bảo mật. Ở Grab, ~98% tech org dùng Cursor hằng tháng. ([Cursor × Grab](https://cursor.com/blog/grab)) |
| **JTBD chính** | "Viết xong feature trong một lần ngồi mà không phải rời editor để hỏi ChatGPT rồi dán code ngược lại." | "Đưa nhiều thay đổi lên production hơn với cùng số người, mà không làm tăng bug và lỗ hổng bảo mật." |
| **Trước đó họ làm bằng cách nào** | Copilot gợi ý từng dòng trong extension, cộng ChatGPT ở tab bên cạnh, copy-paste bằng tay. | Copilot trải đều cho cả team; review, merge và theo dõi deploy làm thủ công. Nút thắt nằm ở khâu review. |

**Dịch chuyển tệp:**
- Người ra quyết định mua đổi từ **cá nhân tự trả tiền** sang **tổ chức mua cho cả nghìn seat**. Ba mốc gây ra dịch chuyển này:
  - **M4:** pricing theo usage, có gói Teams/Ultra cho người dùng nặng.
  - **M6:** Graphite đưa Cursor vào khâu review, nơi người mua là engineering manager chứ không còn là từng dev.
  - **M7:** giao diện quản lý nhiều agent, tức là công cụ cho cả đội.
- Các bản cập nhật 08–09/2026 (Rollouts, Security Review, self-hosted machines) đều chỉ có ở gói Teams/Enterprise.
- **Đang nổi lên một tệp thứ ba:** designer, PM, analyst ngay trong các doanh nghiệp đó. Ở Grab, designer tự ship hàng trăm bản sửa UI thay vì mở ticket rồi chờ vài ngày. JTBD của họ là "tự làm cái mình cần hôm nay, không xếp hàng chờ engineering". Tệp này xuất hiện nhờ M7 (agent-first) và tính năng "Start from scratch, without a repo" (08/2026).

**Switching cost (map 4 forces):**

| Lực | Hướng tác động | Bằng chứng |
|---|---|---|
| **Push** (vấn đề hiện tại) | Đẩy user ra khỏi Cursor | Chi phí khó đoán sau M4. Nhiều người đang trả cả Cursor Pro lẫn Claude Pro nên bỏ Cursor để gộp còn một gói. ([Daniel Miessler, 06/2025](https://danielmiessler.com/blog/dumping-cursor-for-vscode-claude-code)) |
| **Pull** (sức hút cái mới) | Kéo sang đối thủ | Claude Code và Codex cho làm việc ở cấp feature thay vì từng dòng ([Atticus Li, 04/2026](https://www.atticusli.com/blog/posts/switched-cursor-to-claude-code-what-i-miss/)). Codex được gộp sẵn trong ChatGPT Enterprise. |
| **Habit** (thói quen) | Giữ ở lại Cursor | Nhịp Tab-to-accept, diff trực quan, sửa nhỏ nhanh. Đây đúng là những gì người đã rời Cursor nói họ còn nhớ. |
| **Anxiety** (nỗi lo khi đổi) | Giữ ở lại: **yếu với cá nhân, mạnh với doanh nghiệp** | Cá nhân: Cursor là fork VS Code nên rời đi cũng dễ như lúc vào ("không có learning curve", Miessler), và code nằm trong git chứ không nằm trong Cursor. Doanh nghiệp: rule của team, hooks, Bugbot/Graphite trong quy trình PR, security review, hợp đồng mua sắm. |

**Trải nghiệm dùng thật của tôi:**
- Khi mới tải về, Cursor khá khó dùng vì tôi chưa quen cách làm việc AI-first. Đây là lực **Anxiety/Habit cũ** cản người mới.
- Sau 1–2 tháng tôi đã quen với tư duy AI-first. Đó là lúc lực **Habit** chuyển phe, từ cản trở thành giữ chân.
- Càng về sau, Cursor càng có nhiều model mạnh, khiến năng suất của tôi tăng lên. Đây là lực **Pull**, nhưng nó gắn với *model* nhiều hơn là với Cursor. Vì vậy nếu model tốt nhất có ở nơi khác, lực này cũng có thể kéo tôi đi (khớp với Dự đoán 3).

**Lực giữ user mạnh nhất:**
- **Với dev cá nhân là Habit (nhịp Tab).** Khi agent viết gần hết code (chính Cursor 3 thừa nhận xu hướng này), lực Habit sẽ biến mất. Lúc đó dev cá nhân sẽ đi theo agent nào rẻ hơn hoặc mạnh hơn, và Cursor trở lại vị thế wrapper.
- **Với doanh nghiệp là Anxiety (workflow đã cắm sâu).**
- Vì vậy chuỗi M5 → M8 có thể đọc như một cuộc chạy đua: **thay lực Habit đang yếu dần bằng workflow lock-in và model riêng** trước khi Habit biến mất hẳn.

---

## §3. Ba dự đoán hướng đi (6–12 tháng tới, tức 10/2026 → 10/2027)

**Dự đoán 1** *(loại: mở rộng tính năng)*
- **Dự đoán:** Cursor Origin (dịch vụ host code ra mắt 08/2026, hiện sync hai chiều với GitHub) sẽ trở thành nơi lưu code mặc định cho cloud agent ở gói Teams/Enterprise. Review của Graphite và Rollouts sẽ được gộp thành một luồng khép kín ngay trong Cursor: agent tạo PR → review → merge → theo dõi deploy → tự rollback (bật được theo tuỳ chọn).
- **Lập luận:**
  - §2 chỉ ra điểm yếu lớn nhất là Cursor **không có lock-in dữ liệu** vì code nằm ở GitHub. Origin là lời đáp trực tiếp ([SiliconANGLE](https://siliconangle.com/2026/08/17/cursor-launches-origin-code-hosting-service-to-compete-with-github/)).
  - M6 cho thấy cách Cursor xử lý nút thắt là chiếm luôn khâu tiếp theo.
  - Changelog 09/2026 ghi rõ Rollouts "chưa tự merge hay rollback hôm nay" và tích hợp feature flag "sắp có", tức đây là bước tiếp theo đã lộ ra ([Changelog](https://cursor.com/changelog/rollouts-and-security-reviewer)).

**Dự đoán 2** *(loại: mở rộng segment)*
- **Dự đoán:** Cursor sẽ ra một loại seat riêng, rẻ hơn, cho người không phải engineer trong tài khoản doanh nghiệp (designer, PM, analyst, ops). Seat này chỉ gồm cloud agent, preview và publish; mọi thay đổi vào codebase chính phải có engineer duyệt PR. Cursor sẽ không đi cạnh tranh trực diện với Lovable/v0 ở thị trường cá nhân.
- **Lập luận:**
  - Tệp mới nổi ở §2 (Grab: designer, PM, analyst, cả văn phòng CEO đều đang dùng) đã có thật nhưng đang dùng seat của engineer.
  - M7 làm giao diện không còn bắt người dùng đọc từng file.
  - Changelog 08/2026 đã có "Start from scratch" và publish qua Vercel.
  - Bán thêm seat trong tài khoản doanh nghiệp sẵn có (land & expand) khớp với dịch chuyển người mua sang tổ chức ở §2. Thị trường cá nhân lại là nơi lực Habit yếu và pricing từng gây khủng hoảng (M4).

**Dự đoán 3** *(loại: đe doạ Big Tech / mô hình kiếm tiền)*
- **Dự đoán:** Model nhà (Composer/Grok) sẽ thành mặc định trong Auto/Cursor Router và rẻ hơn rõ rệt hoặc nằm sẵn trong gói. Model Claude/GPT thành lựa chọn tính phí cao hơn. Trong vòng 12 tháng, ít nhất một trong Anthropic/OpenAI sẽ siết điều khoản với Cursor (rate limit, giá, hoặc model mới lên Cursor chậm hơn). Cursor phản ứng bằng cách nhấn mạnh "vẫn đa model" với khách enterprise, nhưng kéo dần lưu lượng về model nhà.
- **Lập luận:**
  - M8 đặt Cursor vào cùng phe với xAI, đối thủ trực tiếp của OpenAI và Anthropic, mà context của M5 cho thấy Anthropic từng cắt quyền truy cập của Windsurf khi Windsurf sắp về tay đối thủ.
  - M4 → M5 → M8 là logic kiểm soát chi phí biên: chỉ model nhà mới cho Cursor margin tốt.
  - Nav của Cursor đã đưa Grok lên mục "Models" và có bài viết về Cursor Router ([Cursor blog](https://cursor.com/blog/how-cursor-router-works)).
  - Rủi ro theo §2: dev cá nhân đi theo model tốt nhất, nên nếu model tốt nhất kém đi trên Cursor thì tệp này sẽ rời đi trước.

**Dự đoán mình tự tin nhất là Dự đoán 1**, vì phần lớn các mảnh đã ra mắt (Origin, Rollouts, Graphite); chỉ còn bước ghép lại. **Giả định có thể làm nó gãy:** doanh nghiệp chịu chuyển nơi lưu code gốc (system of record) khỏi GitHub. Nếu họ vẫn giữ GitHub làm nguồn gốc và chỉ dùng Origin làm bản sao cho agent chạy, thì Origin chỉ còn là "bãi nháp của agent", và Cursor vẫn không có lock-in dữ liệu.

---

## §4. AI Log

Công cụ AI đã dùng: **Claude (Anthropic)** với web search và đọc trang web, trong suốt quá trình làm bài.

| Việc | AI làm hay bạn làm? | Bạn kiểm chứng/phán đoán lại thế nào? |
|---|---|---|
| Chọn sản phẩm | **Bạn chọn.** AI đưa 4 gợi ý (Cursor, Perplexity, Notion AI, Duolingo). | Bạn chọn Cursor. AI kiểm tra 3 tiêu chí CP0 (changelog có phân trang, có trang Product Hunt, có báo chí về các lần ra mắt). |
| Thu thập nguồn và lập danh sách 34 mốc ứng viên | **AI** (web search, mở changelog, blog Cursor, Product Hunt) | AI đánh dấu các mốc chỉ có nguồn thứ cấp và phát hiện 4 chỗ số liệu lệch giữa các nguồn (ngày/số tiền Series A–C, ARR lúc SpaceX mua). Vì vậy memo không dùng số liệu gọi vốn làm mốc. |
| Chọn 8 mốc và lý do loại các mốc khác | **AI đề xuất, tôi duyệt** | Tôi đồng ý với 8 mốc vì đây là 8 lần thay đổi rõ rệt nhất của Cursor trên hành trình từ một bản fork của VS Code do 5 người phát triển đến khi được SpaceX mua lại với giá $60B. |
| Kiểm chứng link nguồn của 8 mốc | **AI** mở lần lượt 8 bài gốc trên blog/changelog Cursor và TechCrunch, đối chiếu ngày và nội dung | Link CNBC về thương vụ SpaceX bị lỗi 403 nên đã thay bằng blog Cursor + Bloomberg. _[Bạn tự mở lại 8 link trước khi nộp]_ |
| Viết cột Context và Nguyên lý ở §1 | **AI viết nháp** | Vài chi tiết context là kiến thức nền của AI, chưa gắn nguồn: GPT-4 ra 03/2023, Claude 3.5 Sonnet giỏi tool use. _[Bạn đối chiếu tên nguyên lý với slide buổi học]_ |
| Tệp user, JTBD, 4 forces ở §2 | **AI tổng hợp** từ case study Grab và 2 bài "why I switched"; **tôi bổ sung trải nghiệm dùng thật** | Persona early adopter là **suy luận** từ TechCrunch 2023; AI chưa đọc trực tiếp review 2023. Tôi đối chiếu phần 4 forces với trải nghiệm của chính mình (ghi ở §2): lúc đầu khó dùng vì chưa quen cách làm AI-first, sau 1–2 tháng thì quen. Điều này khớp với nhận định lực Habit hình thành dần rồi giữ chân user. |
| 3 dự đoán và giả định ở §3 | **AI viết nháp** từ §1–§2 và changelog Origin/Rollouts | AI mở bài SiliconANGLE về Origin và changelog Rollouts để chắc các mảnh đã ra mắt. _[Bạn ghi: dự đoán nào bạn đồng ý / sửa lại]_ |
| Ghép memo theo template | **AI** | — |
