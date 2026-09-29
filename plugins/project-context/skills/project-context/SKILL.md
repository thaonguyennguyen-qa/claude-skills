---
name: "project-context"
description: "Use when the user shares Jira/Confluence, a WBS, Figma or project docs and asks Claude to understand a project, or in later chats doing Q&A, tickets, test cases, AC review or AC/WBS/Figma mismatch checks."
---

# Project Context

## Tổng quan
Mỗi dự án có **một Doc "<KEY> – Project Context"** làm nguồn sự thật duy nhất. Mọi yêu cầu được tách thành dòng có ID và nguồn gốc, mọi chỗ chưa rõ được hỏi người dùng **từng câu một**, và mọi câu trả lời về dự án đều dựa trên Doc này, không dựa trên trí nhớ trong chat.

**Chia tầng lưu trữ, mỗi tầng một việc:**

| Tầng | Chứa gì | Không chứa gì |
|---|---|---|
| Claude Project riêng cho từng dự án | File gốc (PDF, Word, WBS export, ảnh Figma) trong project knowledge (phần "RAG") | — |
| Doc Project Context | Thuật ngữ, danh sách yêu cầu, ma trận truy vết, quyết định, câu hỏi mở | Bản sao nguyên văn toàn bộ ticket Jira |
| Memory | Một file `/areas/<key>.md`: Jira key, câu JQL, link WBS/Figma, link Doc, tóm tắt một dòng cho các quyết định lớn đã chốt | Danh sách AC, mâu thuẫn, suy luận của Claude |
| Jira / Sheets / Figma | Dữ liệu sống. Luôn đọc lại khi cần, không tin bản chụp cũ | — |

Khi memory và Doc khác nhau, **Doc là bản chuẩn**. Sửa memory cho khớp với Doc.

Nếu người dùng đang không ở trong Claude Project riêng cho dự án, hãy khuyên họ tạo một Project (một lần duy nhất) rồi tiếp tục làm việc; đừng chặn việc lại vì chuyện này.

## Chế độ 1 — Nạp dự án (lần đầu, hoặc khi có nguồn mới)

1. **Đọc hết các nguồn đã được đưa.** Jira qua JQL (`project = KEY`, lấy summary, description, AC, status, link, epic). Confluence page nếu có link. WBS qua Sheets hoặc file đính kèm. Figma qua `get_metadata` + `get_screenshot` cho từng frame chính. Nếu connector chưa bật, đề nghị bật connector hoặc nhờ người dùng đính kèm file/ảnh.
2. **Tách ra thành danh sách yêu cầu.** Mỗi dòng gồm:
   `REQ-ID | Chức năng | Nội dung (nguyên văn nếu có) | Nguồn (SHOP-12 AC2 / WBS 1.3 / Figma "Nhập OTP") | Trạng thái`
   Trạng thái là một trong: `Xác nhận` (người dùng đã chốt), `Theo nguồn` (có trong tài liệu, chưa ai hỏi lại), `Giả định` (Claude suy ra), `Mâu thuẫn`, `Thiếu`.
3. **Dựng ma trận truy vết** Chức năng × Jira × WBS × Figma, đánh dấu ô trống và ô vênh.
   Một yêu cầu chỉ xuất hiện trong một nguồn (ví dụ chỉ có trên Figma) vẫn là `Theo nguồn`. Việc nó thiếu ở các nguồn khác thể hiện trong ma trận truy vết.
4. **Tạo Doc** theo template bên dưới **trước khi** hỏi người dùng, để mọi câu trả lời có chỗ ghi ngay. Người dùng đã gọi skill này nghĩa là đã đồng ý tạo Doc, không cần hỏi lại.
5. **Gửi một bản tóm tắt ngắn**: phạm vi, số yêu cầu, số mâu thuẫn, số chỗ thiếu, link Doc. **Không** liệt kê hết câu hỏi trong tóm tắt.
6. **Phỏng vấn** (xem bên dưới).
7. **Ghi memory** file `/areas/<key>.md`, chỉ gồm các con trỏ và quyết định đã chốt.

## Phỏng vấn — MỘT câu mỗi lượt

Xếp câu hỏi theo mức độ ảnh hưởng: mâu thuẫn chặn dev/test → thiếu AC → mơ hồ trong AC → chi tiết nhỏ.

Mỗi lượt gồm đúng bốn phần:
1. `Câu n/N` và REQ-ID liên quan
2. Dẫn chứng: nguồn A nói gì, nguồn B nói gì
3. Một câu hỏi, ưu tiên dạng lựa chọn (có kèm lựa chọn "khác")
4. Đề xuất của Claude nếu có, ghi rõ là đề xuất

Sau mỗi câu trả lời: cập nhật Doc (trạng thái → `Xác nhận`, ghi vào Nhật ký quyết định có ngày và người chốt), rồi mới hỏi câu tiếp theo. Nếu người dùng nói "để sau", "chưa biết", hoặc "hỏi PO", đánh dấu câu đó `Chờ <ai>` trong Câu hỏi mở và chuyển câu. Người dùng có thể dừng bất cứ lúc nào; lần sau tiếp tục từ câu còn mở đầu tiên.

N là tổng số câu hỏi đang mở ở thời điểm hỏi. Khi một câu trả lời làm phát sinh câu mới hoặc làm câu cũ không còn cần thiết, tính lại N và nói ngắn gọn lý do.

**Gom 5 câu vào một tin nhắn là sai quy trình**, kể cả khi thấy như vậy "tiết kiệm thời gian". Nếu người dùng muốn "hỏi hết một lượt": giải thích trong một câu rằng câu sau phụ thuộc câu trước, cho phép trả lời bằng chữ cái (A/B/C) và gõ "hỏi PO" để bỏ qua, rồi tiếp tục hỏi từng câu. Nếu người dùng yêu cầu lần nữa, hoặc cần danh sách để chuyển cho người khác (PO, designer): chỉ họ đến mục **Câu hỏi mở** trong Doc, liệt kê mỗi câu một dòng trong chat, rồi đặt cột "Chờ ai" thành người đó. Khi người dùng mang câu trả lời về, ghi các câu trả lời vào Doc và chỉ hỏi lại những chỗ chưa rõ.

## Chế độ 2 — Dùng context (các chat sau)

Trước khi trả lời bất kỳ câu hỏi nào về dự án:
1. Đọc memory `/areas/<key>.md` để lấy link Doc, rồi **đọc Doc**.
2. Nếu câu hỏi liên quan đến ticket, đọc lại ticket trên Jira. Nếu ticket khác với Doc (AC đã đổi, có ticket mới), báo người dùng và cập nhật Doc.
3. Trả lời có trích REQ-ID/nguồn. Nếu câu trả lời phụ thuộc vào yêu cầu có trạng thái `Giả định`, `Mâu thuẫn` hoặc `Thiếu`, nói rõ ra thay vì lặng lẽ tự chọn một phương án.

| Việc | Cách làm |
|---|---|
| Hỏi đáp | Trả lời từ Doc + nguồn, kèm REQ-ID |
| Viết ticket/tài liệu | Dùng thuật ngữ trong Doc; AC viết dạng Given/When/Then; không bịa quy tắc |
| Viết test case | Mỗi TC có ID, REQ-ID, tiền điều kiện, bước, kết quả mong đợi; phủ happy path, biên, lỗi. Nếu TC dựa trên yêu cầu chưa được xác nhận: viết theo AC trên Jira, ghi phương án còn lại trong một dòng, và gắn cờ `Blocked by Q-x` |
| Kiểm tra AC | Mỗi AC: có test được không, có đo được không, có mơ hồ không, có mâu thuẫn không, có thiếu case lỗi không |
| Đối chiếu AC/WBS/Figma | Cập nhật ma trận; mỗi điểm vênh thành một câu hỏi mở mới |
| Rủi ro | Hạng mục có ước lượng nhưng không có AC hoặc design, phụ thuộc bên ngoài, câu hỏi mở đã lâu |

Phát hiện mới trong lúc làm việc (một mâu thuẫn, một quyết định) → ghi vào Doc ngay trong lượt đó.

## Template Doc "<KEY> – Project Context"

1. **Tổng quan**: mục tiêu, người dùng, phạm vi / ngoài phạm vi, các nguồn kèm link, ngày nạp gần nhất
2. **Thuật ngữ**
3. **Danh sách yêu cầu** (bảng như ở bước 2)
4. **Ma trận truy vết** Chức năng × Jira × WBS × Figma
5. **Câu hỏi mở**: `Q-ID | REQ-ID | Câu hỏi | Mức | Chờ ai | Ngày mở`
6. **Nhật ký quyết định**: `Ngày | Quyết định | Người chốt | REQ-ID bị ảnh hưởng`
7. **Rủi ro**

## Lỗi thường gặp

| Lỗi | Cách sửa |
|---|---|
| Liệt kê 8 câu hỏi trong một tin nhắn | Chỉ gửi tóm tắt kèm số lượng câu hỏi, rồi hỏi từng câu |
| Yêu cầu không có nguồn | Mỗi dòng phải có ticket/WBS/frame cụ thể |
| Coi suy luận là sự thật | Gắn trạng thái `Giả định` và hỏi lại |
| Nhét AC và mâu thuẫn vào memory | Memory chỉ giữ con trỏ và quyết định đã chốt; chi tiết nằm trong Doc |
| Trả lời chat sau mà không mở Doc | Luôn đọc memory → Doc → Jira trước |
| Chép hết Jira vào Doc rồi coi đó là bản mới nhất | Jira là dữ liệu sống; Doc chỉ ghi phần đã tách và đối chiếu |

## Khi chạy trong Claude Code (không có Claude Docs / memory của Claude.ai)

| Trong Claude.ai | Thay bằng trong Claude Code |
|---|---|
| Doc "<KEY> – Project Context" | File `docs/project-context/<KEY>.md` trong thư mục dự án, cùng 7 mục template |
| Memory `/areas/<key>.md` | Một mục ngắn trong `CLAUDE.md` của dự án: Jira key, JQL, link WBS/Figma, đường dẫn file context |
| Claude Project knowledge | Thư mục `docs/project-context/sources/` chứa file gốc |

Mọi quy tắc còn lại (ID, nguồn, trạng thái, phỏng vấn từng câu, file context là bản chuẩn) giữ nguyên. Nếu Jira/Figma MCP chưa được cấu hình trong Claude Code, nhờ người dùng dán nội dung hoặc đính kèm file export.
