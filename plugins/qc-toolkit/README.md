# qc-toolkit

Bộ 9 skill dành cho QC/Tester, lấy từ các kho mã nguồn mở trên GitHub (giấy phép MIT). Nội dung giữ nguyên bản gốc, chỉ chỉnh sửa nhỏ để chạy tốt trên Claude.ai.

| Skill | Dùng khi | Nguồn |
|---|---|---|
| qa-write-test-cases | Viết checklist rồi test case từ ticket/spec/Figma, duyệt từng bước | sharon-e-mathew/claude-qa-skills |
| negative-test-generator | Viết negative test: input sai, thiếu, biên, quyền, timing | sharon-e-mathew/claude-qa-skills |
| qa-thinking | Soi feature/spec như QA: cần xác nhận gì, cần làm rõ gì, cần kiểm gì | sharon-e-mathew/claude-qa-skills |
| ai-bug-triage | Gom trùng, gán severity/priority, soạn ticket bug | sharon-e-mathew/claude-qa-skills |
| improve-test-cases | Chuẩn hoá test case có sẵn mà không đổi ID/ý nghĩa | sharon-e-mathew/claude-qa-skills |
| qa-test-planner | Test plan, regression/smoke suite, bug report, đối chiếu Figma, báo cáo test run | softaworks/agent-toolkit |
| pict-test-designer | Thiết kế test tổ hợp pairwise (PICT) cho nhiều tham số | omkamal/pypict-claude-skill |
| risk-based-testing | Ma trận rủi ro impact × probability, FMEA, phân bổ độ phủ test | petrkindlmann/qa-skills |
| exploratory-testing | Charter, session SBTM, heuristic HICCUPS, mẫu log & debrief | petrkindlmann/qa-skills |

## Thay đổi so với bản gốc
- qa-test-planner: bỏ 2 script bash tương tác (không chạy được trong chat), viết lại description để tự kích hoạt.
- qa-thinking: trỏ bước tiếp theo sang qa-write-test-cases thay cho skill không có trong bộ này.

## Giấy phép
Mỗi skill giữ giấy phép MIT của tác giả gốc, xem thư mục `licenses/`.
