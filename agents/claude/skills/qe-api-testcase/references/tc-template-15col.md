# Template: 15-column test case table (`xray-15`)

Every case has all 15 columns: 14 test fields and `JiraID`. An optional leading `#` index column is allowed. An importer reads columns by header name.

Do not use `,` inside cell content. Use `;` to separate items.

## Columns

| # | Column | Description | Format or values |
| :-: | :-- | :-- | :-- |
| 1 | DataID | Key that links to the test data | Copied from the TestDesign `dataId` |
| 2 | Summary | Summary with prefix | `[{System}][API]_{Description}` |
| 3 | Pre-Condition | Initial state, no actions | State description |
| 4 | Test steps | Numbered steps | `1. ...\n2. ...` |
| 5 | Data | Test data | `key=value; key=value` |
| 6 | Expected result | Observable outcomes | `HTTP 201. Field id is not null` |
| 7 | Testcase Type | Test layer | `API`, `Web`, `Mobile`, `PT`, `Security` |
| 8 | Priority | Risk based | `Highest`, `High`, `Medium`, `Low` |
| 9 | Reporter | | empty |
| 10 | Test Repository Path | Tracker path, from the user | `{Root}/{Env}/{Domain}/{Module}/{Feature}` |
| 11 | Automation Feasibility | How practical it is to automate the case | `High`, `Medium`, `Low`, `No` |
| 12 | Test type | Execution type | `Manual` |
| 13 | Environment | | `SIT`, `UAT` |
| 14 | Automated | Current status | `Yes`, `No` |
| 15 | JiraID | Filled after upload | empty |

## Markdown table

```markdown
#### API Test Cases: {Feature}

| # | DataID | Summary | Pre-Condition | Test steps | Data | Expected result | Testcase Type | Priority | Reporter | Test Repository Path | Automation Feasibility | Test type | Environment | Automated | JiraID |
| :-: | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
```

## Example: positive case

| Column | Value |
| :-- | :-- |
| DataID | Product_Create_Success_ValidParams |
| Summary | [Catalog][API]_Product_Create_Valid request |
| Pre-Condition | API endpoint is reachable. A valid token is available. |
| Test steps | 1. Send POST request to /product with valid parameters\n2. Verify HTTP response status\n3. Verify response body contains field `id` |
| Data | name=RANDOM_STRING; price=100 |
| Expected result | HTTP 201. Field `id` is not null. |
| Testcase Type | API |
| Priority | Highest |
| Test Repository Path | Demo/SIT/Catalog/Product/Product_Create |
| Automation Feasibility | High |
| Test type | Manual |
| Environment | SIT |
| Automated | No |

All other columns are empty.

## Example: negative case

| Column | Value |
| :-- | :-- |
| DataID | Product_Create_Name_Empty |
| Summary | [Catalog][API][Negative]_Product_Create_Name empty |
| Pre-Condition | API endpoint is reachable. A valid token is available. |
| Test steps | 1. Send POST request to /product with name = ""\n2. Verify HTTP response status\n3. Verify the error body fields |
| Data | name=""; price=100 |
| Expected result | TBD; needs calibration |
| Testcase Type | API |
| Priority | Medium |
| Automation Feasibility | High |

The design row for `Product_Create_Name_Empty` has Source `OPEN` in this example, so the expected result stays `TBD; needs calibration` (RULE-FAB). Whether a case is positive or negative shows in the Summary prefix, not in Testcase Type.

## QnA (end of the file)
Questions raised while writing cases (tracker path, system prefix, data source, database check). Write `none` if there are none.
```markdown
#### QnA
| QnA | Question | Asked of | Blocks | Status |
| :-- | :-- | :-- | :-- | :-- |
```

## Compliance summary (end of the file)

```markdown
#### Compliance summary
| Metric | Value |
| :-- | :-- |
| Total test cases | |
| Positive | |
| Negative | |
| Design items mapped | x of y (must be all) |
| Cases with TBD expected result | |
| Violations found | n (fixed: a, flagged: b) |
```
