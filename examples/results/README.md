# Example results

What the three skills produce, so you know what to expect. Real output of one run on `POST /product` of a LabIT lab system (Product Management API), with host names removed.

| File | Produced by | What it shows |
| :-- | :-- | :-- |
| `product-create/TestAnalysis-ProductCreate_API.md` | `qe-api-analysis` | Field table, business rules, error codes, questions for the BA (QnA section) |
| `product-create/evidence.md` | `qe-api-analysis` | Evidence ledger: each fact is `confirmed`, `conflict`, `assumption` or `open` |
| `product-create/TestDesign-ProductCreate_API.md` | `qe-api-test-design` | 67 scenarios with technique, source and `dataId` |
| `product-create/TestCases-ProductCreate_API.md` | `qe-api-testcase` | The test cases, one per design row |

How to read them:
- The files are `Draft`. In a real project a person reviews and approves each one before the next skill runs.
- Many `OPEN` and `TBD` rows are on purpose. Where the sources are silent or disagree, the skills ask instead of guessing.
- Your own results go to the output folder you choose at setup (`{root}/{component}/{api}/`), not here.

These were produced with version 0.1.0, before the access-control technique (AC) was added to `qe-api-test-design`, so the authentication and permission rows are fewer than a new run would give.

Try the same chain on the invented sample in `../sample-api/`.
