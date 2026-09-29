# claude-skills

Skill cá nhân cho BA/QC, đóng gói dạng **plugin marketplace** để dùng đồng bộ trên Claude Code (mọi máy) và Claude.ai.

| Plugin | Gồm |
|---|---|
| `project-context` | Hiểu context dự án từ Jira/Confluence, WBS, Figma, tài liệu; phỏng vấn từng câu; kho context dùng xuyên suốt |
| `qc-toolkit` | qa-write-test-cases, negative-test-generator, qa-thinking, qa-test-planner, pict-test-designer, ai-bug-triage, improve-test-cases, risk-based-testing, exploratory-testing |

## Cài trên Claude Code (mỗi máy làm 1 lần)

```
/plugin marketplace add thaonguyennguyen-qa/claude-skills
/plugin install project-context@thaonguyen-qa-skills
/plugin install qc-toolkit@thaonguyen-qa-skills
```

Superpowers cài từ marketplace gốc:

```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

## Cập nhật khi repo thay đổi

```
/plugin marketplace update thaonguyen-qa-skills
```

## Giấy phép
Các skill trong `qc-toolkit` giữ giấy phép MIT của tác giả gốc (xem `plugins/qc-toolkit/licenses/` và `plugins/qc-toolkit/README.md`).
