---
name: project-context
description: Use when the user shares a Jira/Confluence link or project key, a WBS (Excel/Sheets), a Figma link, or project documents (SRS, BRD, PDF, Word, meeting notes) and asks Claude to understand, onboard, or remember a project; and in any later session about that project — Q&A, writing tickets or docs, risk analysis, test cases, AC review, or AC/WBS/Figma mismatch checks.
---

# Project Context

## Tổng quan
Mỗi dự án có **một file context** làm nguồn sự thật duy nhất. Mọi yêu cầu được tách thành dòng có ID và nguồn gốc, mọi chỗ chưa rõ được hỏi người dùng **từng câu một**, và mọi câu trả lời về dự án đều dựa trên file này, không dựa trên trí nhớ trong phiên.

**Nơi lưu (Claude Code, trong thư mục dự án đang mở):**

| Thứ | Vị trí | Chứa gì |
|---|---|---|
| File context | `docs/project-context/<KEY>.md` | Thuật ngữ, danh sách yêu cầu, ma trận truy vết, câu hỏi mở, quyết định, rủi ro |
| File gốc | `docs/project-context/<KEY>/sources/` | PDF, Word, WBS export, ảnh Figma người dùng đưa |
| Con trỏ | Mục `## Project context` trong `CLAUDE.md` của thư mục | Jira key, câu JQL, link WBS/Figma, đường dẫn file context, quyết định lớn một dòng |
| Jira / Sheets / Figma | Qua MCP | Dữ liệu sống. Luôn đọc lại khi cần, không tin bản chụp cũ |

Nếu thư mục chưa có `CLAUDE.md`, tạo mới với đúng mục `## Project context`. Khi `CLAUDE.md` và file context khác nhau, **file context là bản chuẩn**.

Nếu thư mục là repo git, hỏi người dùng một lần có muốn commit thư mục `docs/project-context/` không (file gốc có thể nhạy cảm). Không tự commit.

**Kết nối:** Jira/Confluence qua Atlassian MCP, Figma qua Figma MCP, Google Sheets qua MCP tương ứng (có thể là connector claude.ai khi đăng nhập Claude Code bằng tài khoản claude.ai). Kiểm tra bằng `/mcp` hoặc thử gọi tool. Nếu chưa có: nhờ người dùng dán nội dung, export file vào `sources/`, hoặc chỉ cách thêm MCP. WBS dạng `.xlsx` đọc trực tiếp bằng Python (openpyxl/pandas).

## Chế độ 1 — Nạp dự án (lần đầu, hoặc khi có nguồn mới)

1. **Đọc hết các nguồn đã được đưa.** Jira qua JQL (`project = KEY`: summary, description, AC, status, link, epic). Confluence page nếu có link. WBS từ Sheets hoặc file. Figma qua `get_metadata` + `get_screenshot` cho từng frame chính. File rời trong `sources/`.
2. **Tách ra thành danh sách yêu cầu.** Mỗi dòng:
   `REQ-ID | Chức năng | Nội dung (nguyên văn nếu có) | Nguồn (SHOP-12 AC2 / WBS 1.3 / Figma "Nhập OTP") | Trạng thái`
   Trạng thái là một trong: `Xác nhận` (người dùng đã chốt), `Theo nguồn` (có trong tài liệu, chưa ai hỏi lại), `Giả định` (Claude suy ra), `Mâu thuẫn`, `Thiếu` (có hạng mục/chức năng nhưng không có nội dung yêu cầu, ví dụ chỉ có dòng WBS mà không có AC). Case lỗi chưa được mô tả (sai OTP, vượt giới hạn...) ghi thành câu hỏi mở, không tạo REQ riêng.
3. **Dựng ma trận truy vết** Chức năng × Jira × WBS × Figma, đánh dấu ô trống và ô vênh. Yêu cầu chỉ có ở một nguồn vẫn là `Theo nguồn`; việc thiếu ở nguồn khác thể hiện trong ma trận.
4. **Ghi file context** theo template bên dưới **và** mục `## Project context` trong `CLAUDE.md` (con trỏ) **trước khi** hỏi người dùng, để phiên sau luôn tìm được file dù phỏng vấn dừng giữa chừng. Người dùng gọi skill này nghĩa là đã đồng ý tạo file. File người dùng đưa vào thư mục: **copy** (không move) vào `sources/`; khi có bản mới, giữ bản cũ với hậu tố ngày (`wbs_2026-09-29.xlsx`) và trỏ tới bản mới nhất.
   Nguồn chưa có (ví dụ không có Figma): để trống cột trong ma trận, thêm một dòng Rủi ro và một câu hỏi mở mức Thấp, nhắc một câu trong tóm tắt.
5. **Gửi một bản tóm tắt ngắn**: phạm vi, số yêu cầu, số mâu thuẫn, số chỗ thiếu, đường dẫn file. **Không** liệt kê hết câu hỏi trong tóm tắt.
6. **Phỏng vấn** (xem bên dưới).
7. **Cập nhật `CLAUDE.md`**: thêm các quyết định lớn vừa chốt (mỗi quyết định một dòng).

## Phỏng vấn — MỘT câu mỗi lượt

Xếp câu hỏi theo mức độ ảnh hưởng: mâu thuẫn chặn dev/test → thiếu AC → mơ hồ trong AC → chi tiết nhỏ.

Dùng công cụ AskUserQuestion nếu có (lựa chọn A/B/C, người dùng luôn gõ được câu trả lời khác). Mỗi lượt gồm đúng bốn phần:
1. `Câu n/N` và REQ-ID liên quan
2. Dẫn chứng: nguồn A nói gì, nguồn B nói gì
3. Một câu hỏi, ưu tiên dạng lựa chọn
4. Đề xuất của Claude nếu có, ghi rõ là đề xuất (đặt lên đầu danh sách lựa chọn, gắn "(Đề xuất)")

Sau mỗi câu trả lời: sửa file context (trạng thái → `Xác nhận`, thêm dòng vào Nhật ký quyết định có ngày và người chốt), rồi mới hỏi câu tiếp. Nếu người dùng nói "để sau", "chưa biết", hoặc "hỏi PO", đánh dấu `Chờ <ai>` trong Câu hỏi mở và chuyển câu. Người dùng có thể dừng bất cứ lúc nào; lần sau tiếp tục từ câu còn mở đầu tiên.

N là tổng số câu đang mở ở thời điểm hỏi. Khi câu trả lời làm phát sinh câu mới hoặc làm câu cũ không còn cần, tính lại N và nói ngắn gọn lý do.

**Gom 5 câu vào một tin nhắn là sai quy trình**, kể cả khi thấy "tiết kiệm thời gian". Nếu người dùng muốn "hỏi hết một lượt": giải thích một câu rằng câu sau phụ thuộc câu trước, cho trả lời bằng chữ cái và gõ "hỏi PO" để bỏ qua, rồi tiếp tục từng câu. Nếu người dùng yêu cầu lần nữa, hoặc cần danh sách để chuyển cho người khác (PO, designer): chỉ đến mục **Câu hỏi mở** trong file context, liệt kê mỗi câu một dòng trong chat, rồi đặt cột "Chờ ai" thành người đó. Khi người dùng mang câu trả lời về, ghi vào file và chỉ hỏi lại chỗ chưa rõ.

## Chế độ 2 — Dùng context (các phiên sau)

Trước khi trả lời bất kỳ câu hỏi nào về dự án:
1. Đọc mục `## Project context` trong `CLAUDE.md` (thường đã tự nạp) để lấy đường dẫn, rồi **đọc file context**. Nếu không thấy, tìm `docs/project-context/*.md`; nếu vẫn không có, hỏi người dùng thư mục dự án ở đâu.
2. Nếu câu hỏi liên quan đến ticket, đọc lại ticket trên Jira. Nếu khác với file (AC đổi, có ticket mới), báo người dùng và cập nhật file.
3. Trả lời có trích REQ-ID/nguồn. Nếu câu trả lời phụ thuộc vào yêu cầu `Giả định`, `Mâu thuẫn` hoặc `Thiếu`, nói rõ ra thay vì lặng lẽ tự chọn.

| Việc | Cách làm |
|---|---|
| Hỏi đáp | Trả lời từ file context + nguồn, kèm REQ-ID |
| Viết ticket/tài liệu | Dùng thuật ngữ trong file; AC dạng Given/When/Then; không bịa quy tắc |
| Viết test case | Mỗi TC có ID, REQ-ID, tiền điều kiện, bước, kết quả mong đợi; phủ happy path, biên, lỗi. TC dựa trên yêu cầu chưa xác nhận: viết theo AC trên Jira, ghi phương án còn lại một dòng, gắn cờ `Blocked by Q-x`. Nếu có skill `qa-write-test-cases` / `negative-test-generator`, dùng chúng và đưa REQ-ID vào từng TC |
| Kiểm tra AC | Mỗi AC: test được không, đo được không, mơ hồ không, mâu thuẫn không, thiếu case lỗi không |
| Đối chiếu AC/WBS/Figma | Cập nhật ma trận; mỗi điểm vênh thành một câu hỏi mở mới |
| Rủi ro | Hạng mục có ước lượng mà không có AC hoặc design, phụ thuộc bên ngoài, câu hỏi mở lâu ngày |

Phát hiện mới trong lúc làm việc (mâu thuẫn, quyết định) → ghi vào file context ngay trong lượt đó.

Phỏng vấn dở dang: không tự mở lại khi người dùng đang hỏi việc khác. Trả lời xong, nếu câu hỏi mở liên quan trực tiếp, mời chốt trong một câu. Cột "Chờ ai" mặc định để trống; "Đề xuất" chỉ đưa khi có căn cứ.

## Template file `docs/project-context/<KEY>.md`

1. **Tổng quan**: mục tiêu, người dùng, phạm vi / ngoài phạm vi, các nguồn kèm link, ngày nạp gần nhất
2. **Thuật ngữ**
3. **Danh sách yêu cầu** (bảng như bước 2)
4. **Ma trận truy vết** Chức năng × Jira × WBS × Figma
5. **Câu hỏi mở**: `Q-ID | REQ-ID | Câu hỏi | Mức | Chờ ai | Ngày mở`
6. **Nhật ký quyết định**: `Ngày | Quyết định | Người chốt | REQ-ID bị ảnh hưởng`
7. **Rủi ro**

## Lỗi thường gặp

| Lỗi | Cách sửa |
|---|---|
| Liệt kê 8 câu hỏi trong một tin nhắn | Chỉ gửi tóm tắt kèm số lượng, rồi hỏi từng câu |
| Yêu cầu không có nguồn | Mỗi dòng phải có ticket/WBS/frame cụ thể |
| Coi suy luận là sự thật | Gắn `Giả định` và hỏi lại |
| Nhét AC và mâu thuẫn vào `CLAUDE.md` | `CLAUDE.md` chỉ giữ con trỏ và quyết định lớn; chi tiết nằm trong file context |
| Trả lời phiên sau mà không mở file context | Luôn đọc `CLAUDE.md` → file context → Jira trước |
| Chép hết Jira vào file rồi coi là bản mới nhất | Jira là dữ liệu sống; file chỉ ghi phần đã tách và đối chiếu |
| Tự commit file gốc nhạy cảm | Hỏi người dùng trước khi commit |

## Khi chạy trong Claude.ai (không có thư mục dự án)

Thay file context bằng một Doc "<KEY> – Project Context" cùng 7 mục; thay `CLAUDE.md` bằng memory `/areas/<key>.md` (chỉ con trỏ); file gốc để trong project knowledge của Claude Project. Mọi quy tắc khác giữ nguyên.
