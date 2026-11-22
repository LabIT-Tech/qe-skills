# Roadmap

| Version | Scope | Status |
| :-- | :-- | :-- |
| 0.1 | API: analysis, test design, test cases | Done |
| 0.2 | A second sample API, `qe-prepare` (connection profile and secrets file for Jira, Xray and Confluence), `qe-xray-upload` (import-ready file, then API upload with your approval) | Planned |
| 0.3 | Web: `qe-web-analysis` (scan a page), `qe-web-test-design`, `qe-web-testcase` | Planned |
| 0.4 | `qe-atlassian-ops`: log a bug from a test result, comment, trace bug to test case and requirement, comment on Confluence. Review: check a TestDesign or TestCases against the quality rules | Planned |
| 0.5 | An orchestrator skill that runs the chain with the gates, and a slash command | Planned |
| 1.0 | Stable names, documented formats, more domain packs | Planned |

## What the planned skills are for

| Skill | Purpose |
| :-- | :-- |
| `qe-prepare` | Record your project key, issue types, custom field ids and statuses in a profile, and keep credentials in a separate file that never leaves your machine |
| `qe-xray-upload` | Move approved test cases into Xray with a preview first, and write the Jira keys back |
| `qe-web-*` | The same chain as API testing for web pages, from a read-only scan of a test environment |
| `qe-atlassian-ops` | Turn failed tests into clear bugs after a duplicate check, comment on Jira and Confluence, and show the trace from requirement to test case to bug |

Every write to Jira, Xray or Confluence needs your approval first.

## AI agents

| Agent | Status |
| :-- | :-- |
| Claude (Claude Code plugin, Claude app ZIP) | Done |
| Kiro | Done |
| Codex | Planned |
| Other agents | Open to proposals |

The skills stay the same on every agent; only the packaging and the install notes change. Agent support does not depend on the skill versions above, so it can ship in any release.

Open an issue to propose or take an item. Items are ordered by how often the earlier skills need them, not by size.
