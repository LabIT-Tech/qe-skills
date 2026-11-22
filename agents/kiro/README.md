# qe-skills for Kiro

A LabIT product. Support: [Zalo group](https://zalo.me/g/ijzixj103) | [vn.labit@gmail.com](mailto:vn.labit@gmail.com)

Kiro loads Agent Skills (a folder with a `SKILL.md`). This package holds the three skills in `.kiro/skills/`:

- `qe-api-analysis`
- `qe-api-test-design`
- `qe-api-testcase`

## Install in one project

Unzip `qe-skills_kiro_v<version>.zip` (from `downloads/kiro/`) into the project root, so that you get `.kiro/skills/qe-api-analysis/SKILL.md` and the two others.

## Install for all projects

Copy the three folders from `.kiro/skills/` into `~/.kiro/skills/`.

## Install from the Kiro IDE

Open Agent Steering & Skills, choose Import a skill, then pick a local skill folder or its GitHub URL.

## Atlassian (optional)

Kiro has no connectors. To read requirements from Confluence or Jira, add an Atlassian MCP server in `~/.kiro/settings/mcp.json`. The steps are in the [user guide](../../docs/user-guide.md), section "Connect Atlassian to read requirements".

## Use

Ask in the Kiro chat, for example "Analyze POST /product from my Swagger and requirement document". Review each result and say "ok" to move on. The full flow, the gates and the troubleshooting are in the [user guide](../../docs/user-guide.md).

## Status

Kiro loads skills only from a trusted folder.
