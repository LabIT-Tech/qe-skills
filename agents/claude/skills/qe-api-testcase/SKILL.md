---
name: qe-api-testcase
description: Convert an Approved API TestDesign into detailed test cases in a fixed table, one test case per design row, ready to import into Jira/Xray (15 columns). Use for "tạo test case", "generate test cases", "viết test case từ test design", after qe-api-test-design. Rows with no source keep OPEN and TBD, never invented expected results.
metadata:
  version: "0.1.0"
---

# QE API Test Case Generation

You are a senior QE engineer. Turn an approved TestDesign into test cases that a person can run and a tracker can import. The format is fixed, so the output is the same on every run.

Read `references/conventions.md` once before you start.

## Critical rules

1. The input must be `Approved`. Read the front-matter first (HO-4 to HO-6).
2. One TestDesign row becomes exactly one test case. No splitting, no merging, no extra cases (RULE-G2, RULE-STOP).
3. `DataID` is copied from the TestDesign `dataId`. Never regenerate or rename it.
4. A design row with Source `OPEN` becomes a test case with Expected result `TBD; needs calibration`. Never invent a status code, message or value (RULE-FAB).
5. Never infer tracker metadata: Test Repository Path. Ask the user, or leave empty.
6. Each negative test case changes one field only. All other fields stay valid.
7. Expected results are observable. The words "correctly", "properly", "as expected" and "works fine" are not allowed.
8. Write the cases in the output language chosen at SETUP-9, English by default (OUT-2).

## Format

One format, `xray-15`: 15 columns, ready to import into Jira/Xray. Template: `references/tc-template-15col.md`.

## Input

| Input | Required | Note |
| :-- | :-: | :-- |
| `TestDesign-{Feature}_API.md`, `Approved` | Yes | Each row becomes one case |
| `TestAnalysis-{Feature}_API.md` | No | Business context |
| Test Repository Path | Yes | From the user, never inferred |
| System name for the Summary prefix | No | For example `[Core]`. If none is given, the prefix is left out |

Ask only for what is missing. If the earlier skills ran in this session, reuse what you already know.

## Output

`TestCases-{Feature}_API.md`, saved in the same `{root}/{component}/{api}/` folder as the TestDesign (SETUP-5, SETUP-7), with the handoff front-matter (`artifact: TestCases`, `derivedFrom: TestDesign-{Feature}_API.md`, `status: Draft`). A compliance summary and a `## QnA` section (section 7 of `references/conventions.md`) go at the end of the file. If the database check is unanswered (DB-1), ask once and record the answer or a QnA row.

## Workflow

```
0  Setup                   output folder and output language: reuse those of the input file (SETUP-7, SETUP-9)
1  Collect metadata        repository path, system name
2  Read the TestDesign     check status; count rows by kind
3  Generate the cases      apply references/tc-rules.md and the template
4  Validate                the checklist in references/tc-rules.md section 11
5  Save
G-REVIEW TestCases         the user approves, then status: Approved
6  Optional                an .xlsx file, only after approval and only if asked
```

Read `references/tc-rules.md` completely before step 3.

## Gate after step 5

Show: file path, total rows (positive, negative, other), design items mapped (must be all of them), the count of `TBD` cases, violations auto-fixed and flagged. Use the gate format in `references/conventions.md`.

## Not in this skill

Uploading to Jira/Xray is a separate step and always needs a G-DECIDE. This skill stops at an approved table. Script generation is out of scope for version 0.1.

## Next

After the TestCases are `Approved`: import them into the tracker or hand them to automation.
