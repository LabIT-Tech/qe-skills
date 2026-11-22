<p align="center">
  <a href="https://labit.vn">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="docs/assets/labit-logo-dark-mode.png">
      <img src="docs/assets/labit-logo.png" alt="LabIT" width="220">
    </picture>
  </a>
</p>

# qe-skills

<p align="center">
  <a href="#english">English</a> | <a href="#tiếng-việt">Tiếng Việt</a>
</p>

## About

qe-skills is a LabIT toolkit of AI skills for quality engineers. It speeds up testing, from analysis and test design to import-ready test cases, and keeps quality under control: every step needs human approval, every claim traces to a source, and gaps stay visible as open questions. Built for Claude and Kiro, starting with API testing.

qe-skills là bộ skill AI của LabIT cho kỹ sư quản lý chất lượng (QE). Bộ skill giúp tăng tốc công việc kiểm thử, từ phân tích, thiết kế đến test case sẵn sàng import, đồng thời kiểm soát chất lượng: mỗi bước cần người duyệt, mỗi nhận định truy về nguồn, khoảng trống được giữ lại thành câu hỏi mở. Dùng với Claude và Kiro, bắt đầu từ kiểm thử API.

## Contact

| Website | Email | Zalo group |
| :-- | :-- | :-- |
| [labit.vn](https://labit.vn) | [vn.labit@gmail.com](mailto:vn.labit@gmail.com) | [Join the group](https://zalo.me/g/ijzixj103) |

<!-- Add more channels here when they exist, one column each (for example LinkedIn). -->

Questions, feedback and bug reports are welcome in the Zalo group or by email, or open an issue in this repository.

---

## English

Skills for Quality Engineers (QE), for Claude. A LabIT product. The first release covers API testing with three chained skills.

| Skill | Input | Output |
| :-- | :-- | :-- |
| `qe-api-analysis` | Swagger or OpenAPI, requirement document, optional cURL | TestAnalysis, evidence ledger, questions for the BA |
| `qe-api-test-design` | Approved TestAnalysis | TestDesign: scenarios with technique and source |
| `qe-api-testcase` | Approved TestDesign | TestCases: 15-column table (Jira/Xray) |

### Supported agents

| Agent | Status |
| :-- | :-- |
| Claude (Claude Code, Claude app) | Available |
| Kiro | Available |
| Codex | Planned |

Each agent gets its own package, so the skills stay identical and only the install steps differ.

### Get started

> **Read the [user guide](docs/user-guide.md#english) and complete its setup before you use the skills.** If your requirements are on Confluence or Jira, also connect Atlassian first: the Atlassian connector in Claude, or the Atlassian MCP server in Kiro. The steps are in the guide, section "Connect Atlassian to read requirements".

1. Install the plugin in Claude Code:
   ```
   /plugin marketplace add <your-github-user>/qe-skills
   /plugin install qe-skills@labit-qe-skills
   ```
   Other ways to install (Claude app ZIP, copy folders) are in the [user guide](docs/user-guide.md#english).
2. Open a chat and ask for an analysis, for example "Analyze POST /product from my Swagger and requirement document".
3. Answer the setup questions (output folder, language), review each result, and say "ok" to move on.

Full steps, the meaning of each status, and troubleshooting: **[User guide](docs/user-guide.md#english)**.

### How it works

```
Swagger + requirements
        |
        v
 qe-api-analysis  ---- G-REVIEW (human approves) ---->  TestAnalysis (Approved)
                                                              |
                                                              v
                                                    qe-api-test-design
                                                              |
                          G-REVIEW (human approves) <---------+
                                                              v
                                                    TestDesign (Approved)
                                                              |
                                                              v
                                                     qe-api-testcase
                                                              |
                          G-REVIEW (human approves) <---------+
                                                              v
                                                    TestCases (Approved)
```

Four rules hold the chain together:

1. AI drafts, a person approves. Each step reads only an `Approved` input, or asks.
2. No source, no expected value. A claim with no source stays `OPEN`.
3. Two sources that disagree are recorded as `conflict`. The skill never picks one.
4. One design row becomes one test case. The `dataId` never changes after approval.

Many `OPEN` rows mean the sources are thin. That is a correct result.

### Examples

Run the three skills in order in one chat. Say "ok" at each gate. The sample inputs are in `examples/sample-api/` (an invented Swagger and a requirement document with planted gaps).

| Skill | Example prompt | What you get |
| :-- | :-- | :-- |
| `qe-api-analysis` | `Analyze POST /product from examples/sample-api/swagger.json and examples/sample-api/requirements.md` | A TestAnalysis: field table, business rules, error codes, an evidence ledger, and questions for the BA. No field is marked required without a source. A status code that the two sources report differently is a `conflict` |
| `qe-api-test-design` | `Design the tests for the approved TestAnalysis` | A TestDesign: one scenario per row with a technique (EP, BVA, DT, ST, EG, AC for authentication and authorization), a source and a `dataId`. Scenarios without a source stay `OPEN` |
| `qe-api-testcase` | `Generate the test cases from the approved TestDesign, 15 columns for Xray` | A table of test cases, one per design row, with steps and expected results. An unknown expected result is `TBD; needs calibration` |

More prompts:
- `Analyze this endpoint from the Swagger link and the Confluence page <link>` reads the requirement through the Atlassian connector (Claude) or MCP server (Kiro).
- `Design the tests and add the role and permission cases` adds access-control scenarios.

Example output of a full run (analysis, design, test cases) is in [`examples/results/`](examples/results/).

Full steps, the gates and what each status means: **[User guide](docs/user-guide.md#english)**.

### Contributing and roadmap

See [CONTRIBUTING.md](CONTRIBUTING.md) to report a gap or propose a rule, and [ROADMAP.md](ROADMAP.md) for what comes next.

### License

MIT. See [LICENSE](LICENSE).

© 2026 LabIT. This product and its copyright belong to LabIT.

[Back to top](#qe-skills)

---

## Tiếng Việt

Bộ skill cho kỹ sư quản lý chất lượng (Quality Engineer, QE), dùng với Claude. Sản phẩm của LabIT. Bản đầu tiên làm kiểm thử API, gồm ba skill nối nhau.

| Skill | Đầu vào | Đầu ra |
| :-- | :-- | :-- |
| `qe-api-analysis` | Swagger hoặc OpenAPI, tài liệu yêu cầu, cURL nếu có | TestAnalysis, sổ bằng chứng, câu hỏi gửi BA |
| `qe-api-test-design` | TestAnalysis đã duyệt | TestDesign: danh sách kịch bản, mỗi dòng có kỹ thuật và nguồn |
| `qe-api-testcase` | TestDesign đã duyệt | TestCases: bảng 15 cột (Jira/Xray) |

### Agent được hỗ trợ

| Agent | Trạng thái |
| :-- | :-- |
| Claude (Claude Code, ứng dụng Claude) | Đã có |
| Kiro | Đã có |
| Codex | Đang lên kế hoạch |

Mỗi agent có gói riêng, nên các skill giữ nguyên và chỉ khác cách cài đặt.

### Bắt đầu nhanh

> **Đọc [hướng dẫn sử dụng](docs/user-guide.md#tiếng-việt) và làm xong phần thiết lập trước khi dùng skill.** Nếu yêu cầu nằm trên Confluence hoặc Jira, hãy kết nối Atlassian trước: connector Atlassian trên Claude, hoặc MCP server Atlassian trên Kiro. Các bước nằm trong hướng dẫn, mục "Kết nối Atlassian để đọc yêu cầu".

1. Cài plugin trong Claude Code:
   ```
   /plugin marketplace add <github-của-bạn>/qe-skills
   /plugin install qe-skills@labit-qe-skills
   ```
   Các cách cài khác (ZIP trên ứng dụng Claude, chép thư mục) có trong [hướng dẫn sử dụng](docs/user-guide.md#tiếng-việt).
2. Mở một cuộc trò chuyện và yêu cầu phân tích, ví dụ "Phân tích POST /product từ Swagger và tài liệu yêu cầu của tôi".
3. Trả lời các câu hỏi thiết lập (thư mục kết quả, ngôn ngữ), xem từng kết quả, rồi gõ "ok" để đi tiếp.

Các bước đầy đủ, ý nghĩa từng trạng thái và cách xử lý lỗi: **[Hướng dẫn sử dụng](docs/user-guide.md#tiếng-việt)**.

### Cách hoạt động

Ba skill nối nhau theo thứ tự: phân tích, thiết kế test, test case. Giữa hai skill có một cổng duyệt. Người duyệt đổi trạng thái từ Draft sang Approved thì skill sau mới chạy tiếp. Nếu đầu vào còn là Draft, skill sau sẽ hỏi lại.

Bốn nguyên tắc giữ cho chuỗi này đáng tin:

1. AI soạn, người duyệt chịu trách nhiệm.
2. Không có nguồn thì không có kết quả mong đợi. Dòng thiếu nguồn ghi OPEN.
3. Hai nguồn nói khác nhau thì ghi conflict và hỏi người dùng. Skill không tự chọn.
4. Mỗi dòng thiết kế thành đúng một test case. Mã dataId giữ nguyên từ lúc duyệt thiết kế.

Nhiều dòng OPEN cho thấy tài liệu đầu vào còn thiếu. Đó là kết quả đúng.

### Ví dụ

Chạy ba skill theo thứ tự trong một cuộc trò chuyện. Gõ "ok" ở mỗi cổng duyệt. Đầu vào mẫu nằm trong `examples/sample-api/` (một Swagger bịa và tài liệu yêu cầu có chỗ cố ý thiếu).

| Skill | Câu lệnh mẫu | Kết quả |
| :-- | :-- | :-- |
| `qe-api-analysis` | `Phân tích POST /product từ examples/sample-api/swagger.json và examples/sample-api/requirements.md` | TestAnalysis: bảng field, quy tắc nghiệp vụ, mã lỗi, sổ bằng chứng và câu hỏi gửi BA. Không field nào bị gán bắt buộc khi chưa có nguồn. Mã trạng thái hai nguồn nói khác nhau thì ghi `conflict` |
| `qe-api-test-design` | `Thiết kế test cho TestAnalysis đã duyệt` | TestDesign: mỗi dòng một kịch bản có kỹ thuật (EP, BVA, DT, ST, EG, AC cho xác thực và phân quyền), nguồn và `dataId`. Kịch bản chưa có nguồn giữ `OPEN` |
| `qe-api-testcase` | `Tạo test case từ TestDesign đã duyệt, bảng 15 cột cho Xray` | Bảng test case, mỗi dòng thiết kế một case, có bước và kết quả mong đợi. Kết quả chưa biết ghi `TBD; needs calibration` |

Thêm vài câu lệnh:
- `Phân tích endpoint này từ link Swagger và trang Confluence <link>` đọc yêu cầu qua connector Atlassian (Claude) hoặc MCP server (Kiro).
- `Thiết kế test và thêm các case về role và quyền` thêm kịch bản kiểm soát truy cập.

Kết quả mẫu của một lần chạy đầy đủ (phân tích, thiết kế, test case) nằm ở [`examples/results/`](examples/results/).

Các bước đầy đủ, cổng duyệt và ý nghĩa từng trạng thái: **[Hướng dẫn sử dụng](docs/user-guide.md#tiếng-việt)**.

### Đóng góp và lộ trình

Xem [CONTRIBUTING.md](CONTRIBUTING.md) để báo lỗi hoặc đề xuất quy tắc, và [ROADMAP.md](ROADMAP.md) để biết kế hoạch tiếp theo.

### Giấy phép

MIT. Xem file [LICENSE](LICENSE).

© 2026 LabIT. Sản phẩm này và bản quyền thuộc về LabIT.

[Lên đầu trang](#qe-skills)
