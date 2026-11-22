# User guide

<p align="center">
  <a href="#english">English</a> | <a href="#tiếng-việt">Tiếng Việt</a>
</p>

Contact: [labit.vn](https://labit.vn) | [vn.labit@gmail.com](mailto:vn.labit@gmail.com) | [Zalo group](https://zalo.me/g/ijzixj103)

---

## English

> Before you start: finish "What you need", "Install" and, if your requirements are on Confluence or Jira, "Connect Atlassian to read requirements". Then run "First run".

### What you need

- Claude Code, or the Claude app with Skills enabled.
- For an analysis: a Swagger or OpenAPI file (or the endpoint spec pasted in chat) and a requirement document, user story or acceptance criteria.
- Optional: Atlassian access if the requirements are on Confluence or Jira: the Atlassian connector in Claude, or an Atlassian MCP server in Kiro (see "Connect Atlassian to read requirements"). If it is not connected, you can paste the content instead.

### Install

Pick one way.

#### Option 1: Claude Code plugin

```
/plugin marketplace add <your-github-user>/qe-skills
/plugin install qe-skills@labit-qe-skills
```

The skills appear as `qe-skills:qe-api-analysis`, `qe-skills:qe-api-test-design` and `qe-skills:qe-api-testcase`.

#### Option 2: Claude app (ZIP)

1. Download the ZIP files from `downloads/claude/` on the `main` branch, named like `qe-api-analysis_v0.1.0.zip`.
2. Open Customize, then Skills, and upload each ZIP.
3. Turn on Code execution and file creation if the setting is off.

Each ZIP works alone, so you can install only the skill you need.

#### Option 3: Copy the folders (Claude)

Copy `skills/<name>` into `~/.claude/skills/` (all projects) or `.claude/skills/` (one project).

#### Kiro

1. Download `qe-skills_kiro_v<version>.zip` from `downloads/kiro/` on the `main` branch.
2. Unzip it in the project root. You get `.kiro/skills/qe-api-analysis`, `.kiro/skills/qe-api-test-design` and `.kiro/skills/qe-api-testcase`. For all projects, copy these folders into `~/.kiro/skills/`.
3. Or, in the Kiro IDE, open Agent Steering & Skills, choose Import a skill and pick a skill folder.
4. Ask in the Kiro chat as in "First run" below. Kiro reads skills only from a trusted folder.

#### Codex

Planned. See the roadmap.

### Connect Atlassian to read requirements

Do this once, before the first analysis, if your requirements are on Confluence or Jira. If they are in a file or you paste them in the chat, skip this section.

The skills only read. They never create or edit a page or an issue.

| Agent | What reads Atlassian | How to set it up |
| :-- | :-- | :-- |
| Claude Code, Claude app | The Atlassian connector | Customize, then Connectors, then Atlassian. Sign in when asked |
| Kiro | An Atlassian MCP server (Kiro has no connectors) | Add the server to the MCP settings, as below |

Kiro, step by step:
1. Open the MCP settings file: `~/.kiro/settings/mcp.json` (all projects) or `.kiro/settings/mcp.json` (one project). In the IDE: the MCP Servers panel, then Open config.
2. Add the Atlassian server under `mcpServers`:

   ```json
   {
     "mcpServers": {
       "atlassian": {
         "command": "npx",
         "args": ["-y", "mcp-remote", "https://mcp.atlassian.com/v2/mcp"]
       }
     }
   }
   ```

3. Save. Kiro opens a browser window to sign in to Atlassian and approve access. No token goes into this file. If the address does not work, take the current one from Atlassian's remote MCP server page.
4. Check that the server shows as connected in the MCP Servers panel. It needs Node.js (`npx`).
5. Ask for an analysis with a Confluence or Jira link. The skill first confirms the site and the page it will read.

If the connection fails, the skill says so and asks you to connect or to paste the content. It never continues from memory.

### First run

1. Start a chat and ask for an analysis. The sample inputs are in `examples/sample-api/`:

   ```
   Analyze POST /product from examples/sample-api/swagger.json and
   examples/sample-api/requirements.md
   ```

2. Answer the setup questions. The skill asks only what it is missing:
   - the output root folder (it proposes `{root}/{component}/{api}/` and waits for your yes before writing),
   - the language of the saved results (English is the default),
   - whether a database check is needed, and the details if yes.
3. Review the result at the gate (see below). Say "ok" to continue, or send corrections.
4. Ask for the next step: "Design the tests" for `qe-api-test-design`, then "Generate the test cases" for `qe-api-testcase`. The next skill finds the previous file in the same folder.

With the sample you should see: no field marked required without a source, one `conflict` on the success status code, and a list of questions for the BA.

### The three steps

| Step | Skill | You give | You get |
| :-- | :-- | :-- | :-- |
| 1 | `qe-api-analysis` | Swagger or OpenAPI, requirement document, optional cURL | `TestAnalysis-{Feature}_API.md`, `evidence.md`, questions inside the `## QnA` section |
| 2 | `qe-api-test-design` | The approved TestAnalysis | `TestDesign-{Feature}_API.md`: one row per scenario, with technique and source |
| 3 | `qe-api-testcase` | The approved TestDesign | `TestCases-{Feature}_API.md`: 15-column table for Jira/Xray (`xray-15`) |

For `xray-15` the skill also asks for the Test Repository Path and the system name for the Summary prefix. It never guesses them.

### Reviewing at a gate

Each step ends at a gate and waits for you.

| Gate | When | What to do |
| :-- | :-- | :-- |
| G-REVIEW | After each of the three documents | Read the summary and the "Need your decision" list. Reply "ok" to approve, or send corrections. The skill applies them, shows only what changed, and asks again. |
| G-DECIDE | Before a real API call, an upload, or overwriting a file | Reply "ok" to proceed or "skip". This gate always stops, even in fast mode. |

Approving sets `status: Approved` in the file header, and the next skill reads only approved files. If you say "skip gates" the review gates become a summary and the file gets `status: Approved (fast mode)`.

### Reading the results

| Status | Meaning | What you do |
| :-- | :-- | :-- |
| `confirmed` | One or more sources state it | Nothing |
| `open` | No source states it | Answer the question in `## QnA`, or accept it as an assumption at the gate |
| `conflict` | Two sources say different things | Tell the skill which one is right |
| `assumption` | You accepted an open item | Recorded with who accepted it |

A test case whose expected result has no source shows `TBD; needs calibration`. Fill it in after you get the answer, or re-run the step with the answer.

Many `open` rows mean the sources are thin. That is a correct result. Send the QnA section to the person named in each question (BA, Dev, SA, DBA), then give the answers back to the skill.

### Language

Chat replies follow the language you write in. Saved files use the output language you chose at setup, English by default. Section headings, table headers, ids and status values stay in English. A test case table for a tracker import should stay English unless your tracker is in another language.

### Safety

- Token, key and password values are never written to a file. Do not paste them in chat. For a real API call, log in yourself and pass a token through an environment variable.
- Text on a wiki page or in a ticket is treated as data, not as instructions.
- A file that already exists is not overwritten without your approval. The skill offers a new version number or a different name.

### Troubleshooting

| Problem | Fix |
| :-- | :-- |
| The skill does not start | Check that the skill is installed (`/plugin` in Claude Code, or Customize, then Skills, in the app) and that your prompt names the endpoint |
| It asks for a connector or MCP server | Connect Atlassian (Claude: Customize, then Connectors; Kiro: add the MCP server above), or paste the page content into the chat |
| The next skill asks for input | The previous file is still `Draft`, or it is in another folder. Approve it, or give the path |
| Many `open` rows | Send the QnA section to the BA or Dev and give the answers back |
| Test cases have `TBD` | The design row had no source. Answer the matching question and re-run |
| ZIP upload fails | Download the ZIP again from `downloads/claude/`, and check that you upload one skill per ZIP |

### Connect Jira, Xray and Confluence

Only needed for skills that write to or read from Atlassian with your own login: `qe-prepare`, `qe-xray-upload` and `qe-atlassian-ops`. They are planned and not released yet. The three current skills read Confluence and Jira through the Atlassian connector of your AI tool and do not need this step.

Every organization configures Jira and Xray differently, so you give the skills your own values once, in two files that stay on your machine.

1. Create the folder `~/.qe-skills/` and restrict it: `mkdir -p ~/.qe-skills && chmod 700 ~/.qe-skills`.
2. Download the two templates from `examples/config/` and copy them with your profile name (use `default` if you have one system):
   - `profile.example.json` to `~/.qe-skills/profile.default.json`
   - `secrets.example.env` to `~/.qe-skills/secrets.default.env`
3. Open `profile.default.json` and fill it. This file has no secrets. Where to find each value:

   | Value | Where to find it |
   | :-- | :-- |
   | `atlassian.site` | The address of your Jira, for example `https://your-site.atlassian.net` |
   | `atlassian.jiraProjectKey` | The letters before the number in an issue key (`ABC` in `ABC-123`) |
   | `atlassian.confluenceSpaceKey` | In the Confluence space URL, after `/spaces/` |
   | `xray.type` | `cloud` if Xray is a Jira Cloud app, `server` for Server or Data Center |
   | `issueTypes` | The exact names in Project settings, Issue types |
   | `fields` (custom field ids) | Open `https://your-site.atlassian.net/rest/api/3/field` while signed in and copy the ids (`customfield_10042`) of the fields you use. Ask your Jira admin if you cannot open it. Leave a field empty if your project does not use it |
   | `statuses` | The exact status names of your Bug workflow |
   | `output.root` | The folder where results are saved |

4. Open `secrets.default.env` and fill it yourself:
   - `ATLASSIAN_EMAIL`: the email of your Atlassian account.
   - `ATLASSIAN_API_TOKEN`: create one at `https://id.atlassian.com/manage-profile/security/api-tokens`.
   - `XRAY_CLIENT_ID` and `XRAY_CLIENT_SECRET`: Xray Cloud only. In Jira open Apps, Xray, Settings, API Keys, Create API Key. Leave empty for Server or Data Center.
5. Restrict the secrets file: `chmod 600 ~/.qe-skills/secrets.default.env`.
6. A skill shows the project key, site and Xray type before its first call and waits for your yes. If a value is missing it lists the key names and stops.

Rules:
- Never paste a token in a chat, a ticket or a shared file. A skill never asks for one.
- Never commit these files. Keep them outside any repository (`~/.qe-skills/` is).
- A token leaked by mistake: revoke it at the same page, then create a new one.
- Environment variables with the same names override the file, for shared machines and CI.

### Contact

Questions, feedback or a bug: the [Zalo group](https://zalo.me/g/ijzixj103), [vn.labit@gmail.com](mailto:vn.labit@gmail.com) or [labit.vn](https://labit.vn).

[Back to top](#user-guide)

---

## Tiếng Việt

> Trước khi bắt đầu: làm xong "Bạn cần chuẩn bị", "Cài đặt" và, nếu yêu cầu nằm trên Confluence hoặc Jira, "Kết nối Atlassian để đọc yêu cầu". Sau đó mới chạy "Chạy lần đầu".

### Bạn cần chuẩn bị

- Claude Code, hoặc ứng dụng Claude có bật Skills.
- Để phân tích: file Swagger hoặc OpenAPI (hoặc dán mô tả endpoint vào chat) và tài liệu yêu cầu, user story hoặc tiêu chí chấp nhận.
- Tùy chọn: quyền truy cập Atlassian nếu yêu cầu nằm trên Confluence hoặc Jira: connector Atlassian trên Claude, hoặc MCP server Atlassian trên Kiro (xem "Kết nối Atlassian để đọc yêu cầu"). Chưa kết nối thì bạn dán nội dung vào chat.

### Cài đặt

Chọn một cách.

#### Cách 1: Plugin cho Claude Code

```
/plugin marketplace add <github-của-bạn>/qe-skills
/plugin install qe-skills@labit-qe-skills
```

Skill xuất hiện với tên `qe-skills:qe-api-analysis`, `qe-skills:qe-api-test-design` và `qe-skills:qe-api-testcase`.

#### Cách 2: Ứng dụng Claude (file ZIP)

1. Tải các file ZIP trong `downloads/claude/` ở nhánh `main`, tên dạng `qe-api-analysis_v0.1.0.zip`.
2. Mở Customize, chọn Skills, tải từng file ZIP lên.
3. Bật Code execution and file creation nếu chưa bật.

Mỗi ZIP chạy độc lập, nên bạn chỉ cần cài skill mình dùng.

#### Cách 3: Chép thư mục (Claude)

Chép `skills/<tên skill>` vào `~/.claude/skills/` để dùng cho mọi dự án, hoặc vào `.claude/skills/` để dùng cho một dự án.

#### Kiro

1. Tải `qe-skills_kiro_v<phiên bản>.zip` trong `downloads/kiro/` ở nhánh `main`.
2. Giải nén ở thư mục gốc dự án. Bạn có `.kiro/skills/qe-api-analysis`, `.kiro/skills/qe-api-test-design` và `.kiro/skills/qe-api-testcase`. Muốn dùng cho mọi dự án, chép các thư mục này vào `~/.kiro/skills/`.
3. Hoặc trong Kiro IDE, mở Agent Steering & Skills, chọn Import a skill rồi chọn thư mục skill.
4. Hỏi trong chat Kiro như mục "Chạy lần đầu" bên dưới. Kiro chỉ đọc skill từ thư mục đã tin cậy.

#### Codex

Đang lên kế hoạch. Xem lộ trình.

### Kết nối Atlassian để đọc yêu cầu

Làm một lần, trước lần phân tích đầu tiên, nếu yêu cầu của bạn nằm trên Confluence hoặc Jira. Yêu cầu nằm trong file hoặc bạn dán vào chat thì bỏ qua mục này.

Skill chỉ đọc. Skill không bao giờ tạo hay sửa trang hoặc issue.

| Agent | Cái đọc Atlassian | Cách thiết lập |
| :-- | :-- | :-- |
| Claude Code, ứng dụng Claude | Connector Atlassian | Customize, rồi Connectors, rồi Atlassian. Đăng nhập khi được hỏi |
| Kiro | MCP server Atlassian (Kiro không có connector) | Thêm server vào cấu hình MCP, như bên dưới |

Kiro, từng bước:
1. Mở file cấu hình MCP: `~/.kiro/settings/mcp.json` (mọi dự án) hoặc `.kiro/settings/mcp.json` (một dự án). Trong IDE: bảng MCP Servers, rồi Open config.
2. Thêm server Atlassian vào `mcpServers`:

   ```json
   {
     "mcpServers": {
       "atlassian": {
         "command": "npx",
         "args": ["-y", "mcp-remote", "https://mcp.atlassian.com/v2/mcp"]
       }
     }
   }
   ```

3. Lưu file. Kiro mở trình duyệt để bạn đăng nhập Atlassian và cấp quyền. Không có token nào nằm trong file này. Nếu địa chỉ không chạy, lấy địa chỉ mới nhất ở trang hướng dẫn remote MCP server của Atlassian.
4. Kiểm tra server hiện trạng thái đã kết nối trong bảng MCP Servers. Cần có Node.js (`npx`).
5. Yêu cầu phân tích kèm link Confluence hoặc Jira. Skill sẽ xác nhận site và trang sẽ đọc trước.

Kết nối lỗi thì skill báo và đề nghị bạn kết nối hoặc dán nội dung. Skill không bao giờ làm tiếp từ trí nhớ.

### Chạy lần đầu

1. Mở chat và yêu cầu phân tích. Đầu vào mẫu nằm trong `examples/sample-api/`:

   ```
   Phân tích POST /product từ examples/sample-api/swagger.json và
   examples/sample-api/requirements.md
   ```

2. Trả lời các câu hỏi thiết lập. Skill chỉ hỏi phần còn thiếu:
   - thư mục gốc cho kết quả (skill đề xuất `{root}/{component}/{api}/` và chờ bạn đồng ý rồi mới ghi file),
   - ngôn ngữ của các file lưu ra (mặc định English),
   - có cần đối chiếu cơ sở dữ liệu không, và chi tiết nếu có.
3. Xem kết quả tại cổng duyệt (xem bên dưới). Gõ "ok" để đi tiếp, hoặc gửi chỉnh sửa.
4. Yêu cầu bước kế: "Thiết kế test" cho `qe-api-test-design`, rồi "Tạo test case" cho `qe-api-testcase`. Skill sau tự tìm file của bước trước trong cùng thư mục.

Với dữ liệu mẫu, kết quả đúng có ba điểm: không field nào được gán bắt buộc khi chưa có nguồn, mã trạng thái thành công ghi `conflict`, và có danh sách câu hỏi cho BA.

### Ba bước

| Bước | Skill | Bạn đưa vào | Bạn nhận được |
| :-- | :-- | :-- | :-- |
| 1 | `qe-api-analysis` | Swagger hoặc OpenAPI, tài liệu yêu cầu, cURL nếu có | `TestAnalysis-{Feature}_API.md`, `evidence.md`, câu hỏi trong mục `## QnA` |
| 2 | `qe-api-test-design` | TestAnalysis đã duyệt | `TestDesign-{Feature}_API.md`: mỗi dòng một kịch bản, có kỹ thuật và nguồn |
| 3 | `qe-api-testcase` | TestDesign đã duyệt | `TestCases-{Feature}_API.md`: bảng 15 cột cho Jira/Xray (`xray-15`) |

Với `xray-15`, skill hỏi thêm Test Repository Path và tên hệ thống để làm tiền tố Summary. Skill không tự đoán hai thông tin này.

### Duyệt tại cổng

Mỗi bước kết thúc ở một cổng và chờ bạn.

| Cổng | Khi nào | Bạn làm gì |
| :-- | :-- | :-- |
| G-REVIEW | Sau mỗi tài liệu trong ba tài liệu | Đọc tóm tắt và danh sách "Need your decision". Trả lời "ok" để duyệt, hoặc gửi chỉnh sửa. Skill áp dụng, chỉ cho xem phần đã đổi và hỏi lại. |
| G-DECIDE | Trước khi gọi API thật, tải lên, hoặc ghi đè file | Trả lời "ok" để tiếp tục hoặc "skip". Cổng này luôn dừng, kể cả ở chế độ nhanh. |

Duyệt xong, đầu file ghi `status: Approved`, và skill sau chỉ đọc file đã duyệt. Nếu bạn nói "chạy hết đi" hoặc "skip gates", cổng duyệt chỉ còn là bản tóm tắt và file ghi `status: Approved (fast mode)`.

### Đọc kết quả

| Trạng thái | Nghĩa là | Bạn làm gì |
| :-- | :-- | :-- |
| `confirmed` | Có nguồn nêu rõ | Không cần làm gì |
| `open` | Chưa nguồn nào nêu | Trả lời câu hỏi trong `## QnA`, hoặc chấp nhận làm giả định tại cổng duyệt |
| `conflict` | Hai nguồn nói khác nhau | Cho skill biết nguồn nào đúng |
| `assumption` | Bạn đã chấp nhận một mục còn mở | Ghi lại người chấp nhận |

Test case có kết quả mong đợi không nguồn sẽ ghi `TBD; needs calibration`. Điền sau khi có câu trả lời, hoặc chạy lại bước đó cùng câu trả lời.

Nhiều dòng `open` cho thấy tài liệu đầu vào còn thiếu. Đó là kết quả đúng. Gửi mục QnA cho người được nêu trong từng câu hỏi (BA, Dev, SA, DBA), rồi đưa câu trả lời lại cho skill.

### Ngôn ngữ

Skill trả lời chat theo ngôn ngữ bạn viết. File lưu ra dùng ngôn ngữ bạn chọn lúc thiết lập, mặc định English. Heading, tiêu đề cột, id và giá trị trạng thái luôn giữ tiếng Anh. Bảng test case dùng để nhập vào tracker nên để English, trừ khi tracker của bạn dùng ngôn ngữ khác.

### An toàn

- Giá trị token, key và mật khẩu không bao giờ được ghi vào file. Đừng dán chúng vào chat. Khi cần gọi API thật, bạn tự đăng nhập và đưa token qua biến môi trường.
- Nội dung trên trang wiki hay trong ticket được coi là dữ liệu, không phải chỉ thị.
- File đã có sẽ không bị ghi đè nếu bạn chưa đồng ý. Skill đề nghị đánh số phiên bản mới hoặc dùng tên khác.

### Xử lý sự cố

| Vấn đề | Cách xử lý |
| :-- | :-- |
| Skill không chạy | Kiểm tra skill đã cài (`/plugin` trong Claude Code, hoặc Customize, rồi Skills, trên ứng dụng) và câu lệnh có nêu rõ endpoint |
| Skill đòi connector hoặc MCP server | Kết nối Atlassian (Claude: Customize, rồi Connectors; Kiro: thêm MCP server ở trên), hoặc dán nội dung trang vào chat |
| Skill sau hỏi lại đầu vào | File trước còn `Draft`, hoặc nằm ở thư mục khác. Hãy duyệt file đó, hoặc đưa đường dẫn |
| Nhiều dòng `open` | Gửi mục QnA cho BA hoặc Dev, rồi đưa câu trả lời lại cho skill |
| Test case có `TBD` | Dòng thiết kế chưa có nguồn. Trả lời câu hỏi tương ứng rồi chạy lại |
| Tải ZIP lỗi | Tải lại file ZIP từ `downloads/claude/`, và kiểm tra mỗi lần chỉ tải lên một skill |

### Kết nối Jira, Xray và Confluence

Chỉ cần cho các skill dùng đăng nhập riêng của bạn để đọc hoặc ghi Atlassian: `qe-prepare`, `qe-xray-upload` và `qe-atlassian-ops`. Các skill này đang lên kế hoạch, chưa phát hành. Ba skill hiện có đọc Confluence và Jira qua connector Atlassian của công cụ AI nên không cần bước này.

Mỗi đơn vị cấu hình Jira và Xray khác nhau, nên bạn đưa giá trị của mình cho skill một lần, trong hai file nằm trên máy bạn.

1. Tạo thư mục `~/.qe-skills/` và giới hạn quyền: `mkdir -p ~/.qe-skills && chmod 700 ~/.qe-skills`.
2. Tải hai file mẫu trong `examples/config/` và chép kèm tên profile (dùng `default` nếu bạn chỉ có một hệ thống):
   - `profile.example.json` thành `~/.qe-skills/profile.default.json`
   - `secrets.example.env` thành `~/.qe-skills/secrets.default.env`
3. Mở `profile.default.json` và điền. File này không chứa bí mật. Tìm từng giá trị ở đâu:

   | Giá trị | Tìm ở đâu |
   | :-- | :-- |
   | `atlassian.site` | Địa chỉ Jira của bạn, ví dụ `https://your-site.atlassian.net` |
   | `atlassian.jiraProjectKey` | Phần chữ trước số trong mã issue (`ABC` trong `ABC-123`) |
   | `atlassian.confluenceSpaceKey` | Trong URL của space Confluence, sau `/spaces/` |
   | `xray.type` | `cloud` nếu Xray là app của Jira Cloud, `server` nếu Server hoặc Data Center |
   | `issueTypes` | Tên chính xác trong Project settings, Issue types |
   | `fields` (id custom field) | Mở `https://your-site.atlassian.net/rest/api/3/field` khi đã đăng nhập, chép id (`customfield_10042`) của các field bạn dùng. Không mở được thì hỏi admin Jira. Field nào dự án không dùng thì để trống |
   | `statuses` | Tên chính xác các trạng thái trong workflow Bug |
   | `output.root` | Thư mục lưu kết quả |

4. Mở `secrets.default.env` và tự điền:
   - `ATLASSIAN_EMAIL`: email tài khoản Atlassian của bạn.
   - `ATLASSIAN_API_TOKEN`: tạo tại `https://id.atlassian.com/manage-profile/security/api-tokens`.
   - `XRAY_CLIENT_ID` và `XRAY_CLIENT_SECRET`: chỉ Xray Cloud. Trong Jira mở Apps, Xray, Settings, API Keys, Create API Key. Để trống nếu dùng Server hoặc Data Center.
5. Giới hạn quyền file bí mật: `chmod 600 ~/.qe-skills/secrets.default.env`.
6. Trước lần gọi đầu tiên, skill hiện project key, site và loại Xray rồi chờ bạn đồng ý. Thiếu giá trị thì skill liệt kê tên khóa còn thiếu và dừng.

Quy tắc:
- Không dán token vào chat, ticket hay file chia sẻ. Skill không bao giờ xin token.
- Không commit các file này. Giữ chúng ngoài mọi repository (`~/.qe-skills/` đã ở ngoài).
- Lỡ lộ token: thu hồi ở trang trên rồi tạo token mới.
- Biến môi trường cùng tên sẽ ghi đè file, dùng cho máy dùng chung và CI.

### Liên hệ

Câu hỏi, góp ý hoặc báo lỗi: [nhóm Zalo](https://zalo.me/g/ijzixj103), [vn.labit@gmail.com](mailto:vn.labit@gmail.com) hoặc [labit.vn](https://labit.vn).

[Lên đầu trang](#user-guide)
