# Template: TestAnalysis

Fill every section with real results. Remove empty example rows. The file starts with the handoff front-matter from `conventions.md` section 1 (`artifact: TestAnalysis`).

```markdown
---
artifact: TestAnalysis
feature: {Feature}
story: {story id}
status: Draft
approvedBy:
approvedAt:
derivedFrom:
generatedBy: qe-api-analysis@0.1.0
---

# Test Analysis: {Feature} API

## Open decisions
Put the decisions the reviewer must make on these first lines.
- {number of OPEN rows} OPEN, {number of conflict rows} conflict. List: EV-3, EV-7, ...

## Document info
| Item | Value |
| :-- | :-- |
| Document | TestAnalysis-{Feature}_API.md |
| Version | 1.0 |
| Date | {YYYY-MM-DD} |
| Author | {name} |

## Change history
| Version | Date | Author | Change | Reason |
| :-: | :-- | :-- | :-- | :-- |
| 1.0 | {date} | {name} | First analysis | New API |

## Source documents
| # | Type | Name or link | Version or date | Read on | Note |
| :-: | :-- | :-- | :-- | :-- | :-- |
| 1 | Swagger | | | | |
| 2 | Requirement | | | | |

## API contract
| Item | Detail |
| :-- | :-- |
| Endpoint | `{METHOD} {path}` |
| Content-Type | |
| Auth type | Bearer / API key / Basic / none (never the value) |
| Roles | |

## Request fields
| # | Field | Required | Type | Min/Max | Format | Valid values | Notes (EV id) |
| :-: | :-- | :-- | :-- | :-- | :-- | :-- | :-- |

## Response fields
| # | Field | Type | Nullable | Description | Source |
| :-: | :-- | :-- | :-- | :-- | :-- |

## Business rules
### Response and data
| # | Rule | Source (EV id) |
| :-- | :-- | :-- |
### Access control
| # | Rule | Source (EV id) |
| :-- | :-- | :-- |
### State and lifecycle (if any)
| # | Rule | Source (EV id) |
| :-- | :-- | :-- |
### Data integrity (if any)
| # | Rule | Source (EV id) |
| :-- | :-- | :-- |

## Error codes and messages
| # | Code | Message | HTTP status | Trigger | Source (EV id) |
| :-: | :-- | :-- | :-: | :-- | :-- |

## Field validations
| # | Field | Type | Condition | Expected code | Expected message | Source (EV id) |
| :-: | :-- | :-- | :-- | :-- | :-- | :-- |

## Fields coverage requirements
Input for the test design.
| # | Field | Type | Valid values and limits | Positive needed | Negative needed |
| :-: | :-- | :-- | :-- | :-- | :-- |

## Dynamic data
| # | Field | Category | Description of the value | Resolution (query, API, create) | Status |
| :-: | :-- | :-- | :-- | :-- | :-- |

## Test data dependencies
Include the database check answers (DB-1 to DB-3): database, schema, table, columns, sample query, read-only account name, owner. Write `no database check` if the user says so.
| # | Dependency | How to prepare | Needed for |
| :-: | :-- | :-- | :-- |

## Risks and gaps
| # | Id | Description | Severity | Impact | Mitigation |
| :-: | :-- | :-- | :-- | :-- | :-- |

## Coverage check
- Fields in the source: {n}. Rows in the table: {n}.
- Rules in the source: {n}. Rows in the table: {n}.
- Error codes in the source: {n}. Rows in the table: {n}.

## QnA
Questions raised by this analysis, grouped by who answers (QNA-1 to QNA-6). Write `none` if there are none.
| QnA | Question | Asked of | Blocks | Status |
| :-- | :-- | :-- | :-- | :-- |
| QnA-A1 | Is `name` required on POST /product? The Swagger does not mark it. | BA | TestDesign: Name scenarios | drafted |
```

## `evidence.md`

```markdown
| id | claim | source | ref | status |
| :-- | :-- | :-- | :-- | :-- |
| EV-1 | POST /product returns 201 on success | spec | swagger, POST /product, responses | confirmed |
| EV-2 | POST /product returns 200 on success | spec | requirement doc, section 3 | conflict |
```
