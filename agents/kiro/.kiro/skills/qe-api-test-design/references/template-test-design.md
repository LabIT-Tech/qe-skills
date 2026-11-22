# Template: TestDesign

The file starts with the handoff front-matter (`artifact: TestDesign`, `derivedFrom: TestAnalysis-{Feature}_API.md`, `status: Draft`).

````markdown
---
artifact: TestDesign
feature: {Feature}
story: {story id}
status: Draft
approvedBy:
approvedAt:
derivedFrom: TestAnalysis-{Feature}_API.md
generatedBy: qe-api-test-design@0.1.0
---

# Test Design: {Feature} API

## Open decisions
- {n} scenarios are OPEN (no source, Expected empty): {dataId list}
- {n} dynamic fields are TBD (block automation): {list}

## Document info
| Item | Value |
| :-- | :-- |
| Document | TestDesign-{Feature}_API.md |
| Version | 1.0 |
| Date | {YYYY-MM-DD} |
| Author | {name} |
| Analysis file and version | TestAnalysis-{Feature}_API.md, 1.0 |

## Change history
| Version | Date | Author | Change | Reason |
| :-: | :-- | :-- | :-- | :-- |
| 1.0 | {date} | {name} | First design | From Analysis 1.0 |

## Test design table
| # | dataId | Description | Type | Suite | Expected (high level) | Source | Technique | Dynamic data |
| :-: | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | Product_Create_Success_ValidParams | Happy path with all valid parameters | POSITIVE | DAILY | 201, record created | EV-1 | EP | RANDOM_STRING |
| 2 | Product_Create_Name_Empty | `name` is an empty string | VALIDATION | REGRESSION | 400, name rejected | EV-5 | EP | - |
| 3 | Product_Create_Price_Max | `price` above the stated max | BOUNDARY | REGRESSION | | OPEN | BVA | - |

Source column: an `EV-` id, `BR-`, `ERR-` id or a spec section. No source means `OPEN` and an empty Expected.

Dynamic data column: `-` for a fixed invalid value, a token for a generated value, a keyword for a precondition.

## Dynamic fields summary
| # | dataId | Field | Keyword or token | Type and format | Resolution | Status |
| :-: | :-- | :-- | :-- | :-- | :-- | :-- |

## Positive coverage preview
| Field | Type | Valid values | Used by (dataId) | Technique |
| :-- | :-- | :-- | :-- | :-- |

## Decision tables (if any)
### DT-1: {condition}
| Conditions and rules | R1 | R2 | R3 | R4 |
| :-- | :-: | :-: | :-: | :-: |

Maps to dataId: `...`

## Coverage summary
Counts come from the TestAnalysis tables.
| Metric | Value |
| :-- | :-- |
| Total scenarios | |
| Positive | |
| Negative | |
| Fields covered | x of y |
| Error codes covered | x of y |
| Business rules covered | x of y |
| OPEN scenarios | |
| Checklist violations found | n (fixed: a, flagged: b) |

## QnA
Questions raised by the design (QNA-1 to QNA-6); questions the analysis already holds are not repeated. Write `none` if there are none.
| QnA | Question | Asked of | Blocks | Status |
| :-- | :-- | :-- | :-- | :-- |

## Techniques coverage
| Technique | Count | Applied to |
| :-- | :-: | :-- |
| EP | | |
| BVA | | |
| DT | | |
| ST | | |
| EG | | |
| PW | | |
| UC | | |
````
