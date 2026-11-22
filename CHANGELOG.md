# Changelog

## 0.1.0 (2026-11-21)

First public version, API testing.

- `qe-api-analysis`: turns a Swagger or OpenAPI file and a requirement document into a TestAnalysis with an evidence ledger and questions for the BA.
- `qe-api-test-design`: turns an approved TestAnalysis into a TestDesign using ISTQB techniques (EP, BVA, DT, ST, EG, PW, UC) and access control (authentication and authorization), with a completion checklist and an optional insurance and finance pack.
- `qe-api-testcase`: turns an approved TestDesign into a 15-column test case table ready to import into Jira/Xray.
- A person approves each step. A claim without a source stays `OPEN`. Sources that disagree are recorded as `conflict`.
- Setup step: Atlassian connector or MCP server check, output folder, output language (English by default). Results go to `{root}/{component}/{api}/`, one file per result, with a `## QnA` section in each.
- Packages for Claude (plugin and ZIP) and Kiro (`.kiro/skills/` and ZIP).
- User guide in English and Vietnamese, a sample API, example results, and templates for the Jira, Xray and Confluence connection files.
