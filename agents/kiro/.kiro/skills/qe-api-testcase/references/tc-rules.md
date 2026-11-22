# Test case generation rules

Read this completely before generating. Rules are referenced by id and checked in section 11.

## 1. General

| ID | Rule |
| :-- | :-- |
| RULE-G1 | Generate only within the scope of the TestDesign, the acceptance criteria and the API spec. Assume nothing extra |
| RULE-G2 | One design item gives exactly one test case |
| RULE-G3 | Business-level cases a person can run, not low-level implementation |
| RULE-G4 | Case content is in English by default (OUT-2) |

## 2. Identifiers

| ID | Rule |
| :-- | :-- |
| RULE-S4 | `DataID` is the `dataId` from the TestDesign, unchanged. Only if a row lacks one, build it as `{Module}_{Action}_{Field}_{Valid or Invalid}_{ShortDescription}` and flag it |

## 3. Summary

| Field | Format | Example |
| :-- | :-- | :-- |
| Summary, positive | `[{System}][API]_{Description}` | `[Core][API]_Product_Create_Valid request` |
| Summary, negative | `[{System}][API][Negative]_{Description}` | `[Core][API][Negative]_Product_Create_Name empty` |

`{System}` comes from the user. If the user gives none, leave the system prefix out and write `[API]_{Description}`.

## 4. Testcase Type

The column says which test layer a case belongs to. It does not say positive or negative: that shows in the Summary prefix.

| Value | Use when |
| :-- | :-- |
| `API` | The case calls an API directly. This is the value for nearly every case this skill writes |
| `Web` | The case runs through a web UI |
| `Mobile` | The case runs through a mobile app |
| `PT` | A performance test: load, stress, latency under load |
| `Security` | A real security check: injection, traversal, bypass, replay |

Map from the design Type: SECURITY is `Security`; every other design Type is `API`. `Web`, `Mobile` and `PT` apply when the design comes from a web, mobile or performance skill, or when the user says so. If a case could belong to two layers, ask. The team may replace this list.

## 5. Metadata fields (`xray-15`)

| Field | Rule | Allowed values | Default |
| :-- | :-- | :-- | :-- |
| Testcase Type | RULE-F1 | `API`, `Web`, `Mobile`, `PT`, `Security` | from section 4 |
| Priority | RULE-F3 | `Highest`, `High`, `Medium`, `Low` | none, see section 6 |
| Automation Feasibility | RULE-F4 | `High`, `Medium`, `Low`, `No` | from section 7 |
| Automated | RULE-F5 | `Yes`, `No` | `No`, unless the user says an automation framework already covers API tests |
| Test type | RULE-F6 | `Manual` | `Manual` |
| Environment | RULE-F7 | `SIT`, `UAT` or the team's list | `SIT` |
| Test Repository Path | RULE-F10 | `{Root}/{Env}/{Domain}/{Module}/{Feature}` from the user | never inferred |
| Reporter | RULE-F12 | empty | empty |
| JiraID | | Filled after upload | empty |

## 6. Priority: risk model (RULE-F3)

Priority is the business risk if a defect escapes to production. Assess two axes per case, then derive the priority.

| Impact | Score | Criteria |
| :-- | :-: | :-- |
| Critical | 4 | Financial loss, data corruption, compliance breach, core flow blocked with no workaround |
| High | 3 | Major function broken with a workaround, data inconsistency without money involved |
| Medium | 2 | Function degraded, limited impact, a gap the backend still catches |
| Low | 1 | Cosmetic, rare edge case, no business consequence |

| Likelihood | Score | Criteria |
| :-- | :-: | :-- |
| High | 3 | New code, complex logic (DT or ST), several systems involved |
| Medium | 2 | Modified code, moderate complexity, one dependency |
| Low | 1 | Stable code, simple single-field validation |

| Impact times Likelihood | Priority |
| :-: | :-- |
| 9 or more | `Highest` |
| 6 to 8 | `High` |
| 3 to 5 | `Medium` |
| 1 to 2 | `Low` |

Signals in the TestDesign: Suite `SMOKE` means Impact 3 or more. Technique `DT` or `ST` means Likelihood 2 or more. Money, premium, fee or payout in the description means Impact 4. Status or transition means Impact 3 or more. Token or auth means Impact 3. Special characters, max length or an optional field means Impact 2 or less.

| ID | Rule |
| :-- | :-- |
| PRI-1 | Assess each case. Never map from the Suite alone |
| PRI-2 | Unsure about impact: use `Medium` and flag it for review |
| PRI-3 | A priority the user sets wins |
| PRI-4 | Two cases in the same Suite can differ in priority |
| PRI-5 | If the TestDesign has a Priority column, copy it per DataID and use this model only for empty cells |

## 7. Automation Feasibility (RULE-F4)

The Automation Feasibility column holds one of four values: `High`, `Medium`, `Low`, `No`. It says how practical it is to automate the case. `No` means the case cannot be automated. The column does not say whether a script exists (that is the `Automated` column, Yes or No).

| Testcase Type | Automation Feasibility (default) |
| :-- | :-- |
| `API` | `High` |
| `Web` | `Medium` |
| `Mobile` | `Medium` |
| `PT` | `Medium` |
| `Security` | `Medium` |

A user override wins. A case the user marks as not automatable gets `No`. A case that needs manual judgement, a physical device or a one-off data setup can be lowered to `Low`, with a short reason in the gate message.

## 8. Pre-condition, steps, expected result

| ID | Rule |
| :-- | :-- |
| RULE-P1 | Pre-condition is an initial state only: "API reachable. Valid token available." Never an action step |
| RULE-P2 | Each case runs on its own. No dependency on another case's result |
| RULE-T1 | Steps are clear actions of a person or a request. No description of internals |
| RULE-T2 | Minimal steps. Checks belong in Expected result |
| RULE-T3 | One atomic action per step |
| RULE-T4 | Steps are import-ready: each has an Action and an observable result |
| RULE-E1 | Expected result is observable and comparable |
| RULE-E2 | Forbidden words: "correctly", "properly", "as expected", "works fine" |

Step pattern: actor, action, target, data. Good: "Send POST request to /product with name empty". Bad: "The system processes the request" and "Check that everything works" and "Do steps 1 to 3 of TC-001".

## 9. API rules

| ID | Rule |
| :-- | :-- |
| TC-POS1 | Positive: a valid request, a success response |
| TC-POS2 | Positive: no expected error code or message |
| TC-POS3 | Positive with data verification: if the TestDesign Expected mentions a data check, add a step to query the store and an Expected line with table, field and value. Only for create, update, delete. If the TestDesign does not mention it, do not add it |
| TC-NEG1 | Negative: name the affected field in the steps |
| TC-NEG2 | EMPTY means the field is `""`. BLANK means `" "`. MISSING means the field is removed |
| TC-NEG3 | Special characters use `"!@#$%^&*()"` |
| TC-NEG4 | Max length uses the limit from the spec plus one. Without a limit in the spec, the case is `OPEN` |
| TC-NEG5 | Expected error: the fields this API's error body really returns, taken from the spec. Mark them tentative and calibrate at the first run |
| TC-NEG6 | Each negative changes only one field |
| TC-AUTH1 | Token missing: remove the token. Token empty: send `""` |
| TC-AUTH2 | Auth cases expect 401 or 403 only if the spec says so. Otherwise `TBD` |

Steps, positive with a data check:
```
1. Send {METHOD} request to {endpoint} with valid parameters
2. Verify HTTP response status is {code}
3. Verify response body matches the schema
4. Verify response fields are present with the expected values
5. Query table `{table}` for the {created|updated|deleted} record
6. Verify stored fields match the response: {field1}={value1}; {field2}={value2}
```

Steps, negative field validation:
```
1. Send {METHOD} request to {endpoint} with {field} = {condition}
2. Verify HTTP response status is {code}
3. Verify the error body fields: {fields this API returns}
```

## 10. Order, stop rules, fabrication

Row order: positive (happy path first), negative grouped by field (Empty, Blank, Missing, Invalid, SpecialChars), auth (Token Missing, Token Empty), integration.

| ID | Rule |
| :-- | :-- |
| RULE-STOP1 | Stop when every design item is converted. No extras |
| RULE-STOP2 | No duplicate, exploratory or assumption-based cases |
| RULE-FAB | Never invent a status code, message, UI text or value. Use `TBD; needs calibration` and tell the user. Sources allowed: the API spec or requirement document, an actual response, a data query, the user |

## 11. Validation checklist

Run on every case before showing the output. Fix format issues silently. Flag content issues to the user.

| Check | Rule |
| :-- | :-- |
| In scope | RULE-G1 |
| One-to-one mapping | RULE-G2 |
| DataID from the TestDesign | RULE-S4 |
| Summary prefix | section 3 |
| Pre-condition is a state | RULE-P1 |
| Independent | RULE-P2 |
| Steps executable and minimal | RULE-T1, RULE-T2 |
| Expected verifiable, no forbidden words | RULE-E1, RULE-E2 |
| Negative changes one field | TC-NEG6 |
| `TBD` where the design has no source | RULE-FAB |
| No `,` inside cell content (xray-15) | template note |

## 12. XLSX (optional, only after approval and only if asked)

| ID | Rule |
| :-- | :-- |
| XLSX-1 | Build a real `.xlsx` file, not CSV |
| XLSX-2 | Content as it is in the `.md`, no edits |
| XLSX-3 | Steps and Expected use a line break inside the cell |
| XLSX-4 | Items inside a cell use `;` as separator |
| XLSX-5 | Sheet name `TestCases`. Save next to the `.md` file. Report the path and the row count |

## 13. Import-ready steps

When cases are imported into a tracker, each numbered step becomes one step with an Action, an Expected result and optional Data. Write each step so it can stand alone. Align each expected outcome with its step.
