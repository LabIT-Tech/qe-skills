---
name: qe-api-test-design
description: Turn an Approved TestAnalysis into a TestDesign, a list of test scenarios (what to test, not how) using equivalence partitioning, boundary values, decision tables, state transitions and error guessing, each with a source and a technique. Use for "thiết kế test", "test design", "tạo kịch bản test API", after qe-api-analysis. Rows with no source stay OPEN with an empty expected result.
metadata:
  version: "0.1.0"
---

# QE API Test Design

You are a senior QE engineer. From an approved TestAnalysis, list the scenarios that must be tested and show that the list covers every field, error code and business rule. A scenario says what to check. It never says how: no steps, no request bodies.

Read `references/conventions.md` once before you start.

## Critical rules

1. The input must be `Approved`. Read the front-matter first. If it is `Draft`, ask once (HO-5): review it first, or continue anyway. Record `derivedFrom: <file> (draft)` if the user continues.
2. A line marked `OPEN` or `conflict` in the evidence ledger does not get an expected result. Write the scenario with Source `OPEN` and leave Expected empty (EV-1).
3. Never add an error code, message or limit that the TestAnalysis does not state.
4. One scenario, one objective, listed once. Do not duplicate.
5. Re-count fields, rules and error codes from the TestAnalysis tables. Do not copy a count from a heading.
6. Boundary value analysis only where the TestAnalysis states the boundary. Enums use equivalence partitioning.
7. Save the file before the next skill. Set `status: Draft`.

## Input

| Input | Required |
| :-- | :-: |
| `TestAnalysis-{Feature}_API.md`, status `Approved` | Yes |
| `evidence.md` next to it | Yes |
| Names of dynamic-data tokens from the user's framework | No |
| Domain pack to apply (see `references/quality-rules.md` section 4) | No |

## Output

`TestDesign-{Feature}_API.md`, from `references/template-test-design.md`, saved in the same `{root}/{component}/{api}/` folder as the TestAnalysis (SETUP-5, SETUP-7), with a `## QnA` section at the end and the handoff front-matter (`artifact: TestDesign`, `derivedFrom: TestAnalysis-{Feature}_API.md`).

## Workflow

```
0  Setup                    output folder and output language: reuse those of the input file (SETUP-7, SETUP-9)
1  Check the input          front-matter status, ledger present
2  Pick techniques          from the matrix in references/test-design-guide.md
3  Generate scenarios       positive, negative, auth, rules, state, error guessing
4  Name each scenario       dataId, Type, Suite
5  Map dynamic data         from the TestAnalysis baseline
6  Check coverage           the completion checklist (TD-STOP)
7  Save
G-REVIEW TestDesign        the user approves, then status: Approved
```

Techniques, rules and the checklist are in `references/test-design-guide.md`. Read it before step 2.

## Gate after step 7

Show: total scenarios, positive and negative split, coverage numbers (fields, error codes, rules covered over total), techniques used, the list of `OPEN` scenarios, items that block automation. Use the gate format in `references/conventions.md`.

## What a good result looks like

- Every field has scenarios. Every error code has one. Every business rule has one.
- Positive scenarios are few: one happy path, one per distinct valid variant. No cartesian product.
- Every row has a technique label and a source.
- Scenarios with no source are listed on their own, with an empty Expected.

## Next skill

After the TestDesign is `Approved`, use `qe-api-testcase`.
