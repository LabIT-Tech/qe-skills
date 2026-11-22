---
artifact: TestAnalysis
feature: ProductCreate
story: prod-product-api
status: Draft
approvedBy:
approvedAt:
derivedFrom: 
generatedBy: qe-api-analysis@0.1.0
---

# Test Analysis: ProductCreate API

## Open decisions
- 12 OPEN and 6 conflict rows in `evidence.md`.
- Conflicts: EV-5, EV-6, EV-8, EV-9, EV-14, EV-15. They block the design rows that touch string length limits, inactive category and the response envelope.
- OPEN: EV-7, EV-12, EV-17, EV-19, EV-20, EV-26, EV-27, EV-28, EV-29, EV-30, EV-31, EV-32.
- 15 questions are in the QnA section below. The requirement page is not approved yet (Reviewed / approved: Pending).

## Document info
| Item | Value |
| :-- | :-- |
| Document | TestAnalysis-ProductCreate_API.md |
| Version | 1.0 |
| Date | 2026-11-21 |
| Author | LabIT QE (draft by qe-api-analysis) |

## Change history
| Version | Date | Author | Change | Reason |
| :-: | :-- | :-- | :-- | :-- |
| 1.0 | 2026-11-21 | LabIT QE | First analysis | New API under test |

## Source documents
| # | Type | Name or link | Version or date | Read on | Note |
| :-: | :-- | :-- | :-- | :-- | :-- |
| S1 | Swagger | `{host}/lab/api/v0/prod-man/v2/api-docs` (UI: `swagger-ui.html`; host not published) | none | 2026-11-21 | Fetched through a browser. Read only; no API call |
| S2 | Requirement | Confluence page "Product Management: Product API and Create product flow" | v1.1; updated 2026-11-07 | 2026-11-21 | Read through the Atlassian connector. Status: as-built with open decisions; approval pending. Derived from code |
| S3 | Requirement | second requirement document | none | 2026-11-21 | Not re-read. Facts come from an earlier comparison note (second hand) |

## API contract
| Item | Detail |
| :-- | :-- |
| Endpoint | `POST /product` (base path `/lab/api/v0/prod-man`; host `{host}`) |
| Content-Type | `application/json` (S1) |
| Auth type | `Authorization` header; Swagger type `apiKey`; S2 says Bearer token. Value never recorded (EV-22) |
| Roles | Permission `prod/product.create` (EV-21) |
| Success | HTTP 201 (EV-4) |

## Request fields
| # | Field | Required | Type | Min/Max | Format | Valid values | Notes (EV id) |
| :-: | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | `code` | Yes | string | 3 to 100 (S2) or up to 255 (S3): CONFLICT | | stored in upper case; unique, case-insensitive | EV-3, EV-8, EV-9, EV-10, EV-11 |
| 2 | `name` | Yes | string | 3 to 100 (S2) or up to 255 (S3): CONFLICT | | | EV-3, EV-8, EV-9 |
| 3 | `description` | No | string | 3 to 100 when sent (S2) or up to 255 (S3): CONFLICT | | | EV-3, EV-8, EV-9, EV-31 |
| 4 | `category` | Yes | string | | uuid | must exist; ACTIVE is a CONFLICT | EV-3, EV-13, EV-14, EV-15 |
| 5 | `supplier` | Yes | string | | uuid | must exist | EV-3, EV-13 |
| 6 | `price` | Yes | number | | double | not negative is a proposal (OPEN) | EV-3, EV-16, EV-17 |
| 7 | `quantity` | Yes | integer | | int64 | whole number; not negative is a proposal (OPEN) | EV-3, EV-16, EV-17 |
| 8 | `unit` | No | string | 1 to 100 when sent (S2) or up to 255 (S3): CONFLICT | | | EV-3, EV-8, EV-9 |

Header: `Authorization` is required (EV-21, EV-22).

## Response fields
Envelope (`ServiceResponse<ProductModel>`): field names are in CONFLICT (EV-5, EV-6).
| # | Field | Type | Nullable | Description | Source |
| :-: | :-- | :-- | :-- | :-- | :-- |
| 1 | `code` (S1) or `status` (S2) | integer | OPEN | Envelope result code; success value in body OPEN (EV-7) | EV-5, EV-6 |
| 2 | `data` | object | OPEN | Product object | S1 |
| 3 | `message` | string | OPEN | Message text | S1 |
| 4 | `data.id` | string (uuid) | OPEN | New product id | S1 |
| 5 | `data.productCode` | string | OPEN | Upper-case code | S1; EV-10 |
| 6 | `data.productName` | string | OPEN | Name | S1 |
| 7 | `data.productDesc` | string | Yes (absent when null) | Description | S1; S2 14.1 |
| 8 | `data.status` | enum ACTIVE, INACTIVE | OPEN | `ACTIVE` on create | S1; EV-10 |
| 9 | `data.quantity` | integer (int64) | OPEN | Stock quantity | S1 |
| 10 | `data.price` | number (double) | OPEN | Price | S1 |
| 11 | `data.unit` | string | Yes (absent when null) | Unit | S1; S2 14.1 |
| 12 | `data.category` | object | OPEN | CategoryModel: `id` (uuid) `categoryName` `cateDesc` `status` (ACTIVE, INACTIVE) | S1 |
| 13 | `data.supplier` | object | OPEN | SupplierModel: `id` (uuid) `supName` `supContactName` `supAddress` `supCity` `supPostalCode` `supCountry` `supPhone` | S1 |

## Business rules
### Response and data
| # | Rule | Source (EV id) |
| :-- | :-- | :-- |
| BR-1 | Success returns HTTP 201 with the created product | EV-4, EV-10 |
| BR-2 | Code is stored and returned in upper case | EV-10 |
| BR-3 | Price and quantity are stored and returned exactly as sent (known defect in test environments) | EV-16, EV-32 |
| BR-4 | Description and unit may be omitted; then they are absent from the response | EV-23 |
| BR-5 | The created product can be read back by detail and by search | EV-24 |
### Access control
| # | Rule | Source (EV id) |
| :-- | :-- | :-- |
| BR-A1 | A valid token is required; none or invalid returns 401 | EV-21, EV-22 |
| BR-A2 | Permission `prod/product.create` is required; without it 403 and nothing changes | EV-21 |
| BR-A3 | Wrong HTTP method on the path returns 405 | EV-21 |
### State and lifecycle
| # | Rule | Source (EV id) |
| :-- | :-- | :-- |
| BR-S1 | A new product starts as ACTIVE. Create has no transition, so state transition testing does not apply beyond the initial state | EV-10 |
### Data integrity
| # | Rule | Source (EV id) |
| :-- | :-- | :-- |
| BR-D1 | Code is unique, case-insensitive (`abc01` equals `ABC01`) | EV-11 |
| BR-D2 | The code of a soft-deleted product stays taken (pending decision) | EV-12 |
| BR-D3 | Category must exist | EV-13 |
| BR-D4 | Supplier must exist | EV-13 |
| BR-D5 | Category must be ACTIVE: S3 says yes; S2 says only existence is checked (CONFLICT) | EV-14, EV-15 |
| BR-D6 | Check order: input validation, code unique, category, supplier | EV-25 |
| BR-D7 | Price and quantity are not negative (proposed, OPEN) | EV-17 |

## Error codes and messages
Messages are copied from S2 section 8 and are tentative until OQ9 is answered (EV-19).
| # | Code | Message | HTTP status | Trigger | Source (EV id) |
| :-: | :-- | :-- | :-: | :-- | :-- |
| ERR-1 | `PRODUCT_CODE_REQUIRED` | Product code is required | 400 | Code missing or blank | EV-18, EV-19 |
| ERR-2 | `PRODUCT_CODE` | Product code must be 3-100 characters | 400 | Code length outside the limit | EV-18, EV-8, EV-9 |
| ERR-3 | `PRODUCT_NAME_REQUIRED` | Product name is required | 400 | Name missing or blank | EV-18, EV-19 |
| ERR-4 | `PRODUCT_NAME` | Product name must be 3-100 characters | 400 | Name length outside the limit | EV-18, EV-8, EV-9 |
| ERR-5 | `PRODUCT_DESC` | Description must be 3-100 characters | 400 | Description length outside the limit | EV-18, EV-8, EV-9 |
| ERR-6 | `PRODUCT_UNIT` | Unit must be 1-100 characters | 400 | Unit length outside the limit | EV-18, EV-8, EV-9 |
| ERR-7 | validation error (code OPEN) | OPEN | 400 | Category supplier price or quantity missing | EV-20 |
| ERR-8 | `PRODUCT_EXISTED` 4001102 | Product already exists {code} | 400 | Duplicate code | EV-11 |
| ERR-9 | `PRODUCT_QUANTITY_INVALID` 4001104 | Product's quantity invalid {value} | 400 | Not used today; proposed for negative quantity | EV-17 |
| ERR-10 | `CATEGORY_NOT_FOUND` 4044001 | Category not found: {id} | 404 | Category id not in the system | EV-13 |
| ERR-11 | `SUPPLIER_NOT_FOUND` 4045001 | Supplier not found: {id} | 404 | Supplier id not in the system | EV-13 |
| ERR-12 | unauthorized | Token invalid | 401 | No token or invalid token | EV-21 |
| ERR-13 | forbidden | No permission | 403 | Valid token without `prod/product.create` | EV-21 |
| ERR-14 | (none stated) | OPEN | 405 | Wrong HTTP method | EV-21 |

## Field validations
| # | Field | Type | Condition | Expected code | Expected message | Source (EV id) |
| :-: | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | code | REQUIRED | missing or blank | PRODUCT_CODE_REQUIRED | Product code is required (tentative) | EV-18, EV-19 |
| 2 | code | LENGTH | 3 to 100 (CONFLICT with 255) | PRODUCT_CODE | tentative | EV-8, EV-9 |
| 3 | name | REQUIRED | missing or blank | PRODUCT_NAME_REQUIRED | tentative | EV-18, EV-19 |
| 4 | name | LENGTH | 3 to 100 (CONFLICT with 255) | PRODUCT_NAME | tentative | EV-8, EV-9 |
| 5 | description | LENGTH | 3 to 100 when sent (CONFLICT with 255) | PRODUCT_DESC | tentative | EV-8, EV-9 |
| 6 | unit | LENGTH | 1 to 100 when sent (CONFLICT with 255) | PRODUCT_UNIT | tentative | EV-8, EV-9 |
| 7 | category | REQUIRED | missing | OPEN | OPEN | EV-20 |
| 8 | category | EXISTS | id not in the system | CATEGORY_NOT_FOUND | tentative | EV-13 |
| 9 | supplier | REQUIRED | missing | OPEN | OPEN | EV-20 |
| 10 | supplier | EXISTS | id not in the system | SUPPLIER_NOT_FOUND | tentative | EV-13 |
| 11 | price | REQUIRED | missing or null | OPEN | OPEN | EV-20, EV-27 |
| 12 | price | RANGE | not negative (proposed) | OPEN | OPEN | EV-17 |
| 13 | quantity | REQUIRED | missing or null | OPEN | OPEN | EV-20, EV-27 |
| 14 | quantity | RANGE | not negative (proposed) | PRODUCT_QUANTITY_INVALID | tentative | EV-17 |

## Fields coverage requirements
| # | Field | Type | Valid values and limits | Positive needed | Negative needed |
| :-: | :-- | :-- | :-- | :-- | :-- |
| 1 | code | string | 3 to 100 (OPEN: 255); unique | valid; lower case; min; max | missing; blank; empty; null; special chars; min-1; max+1; duplicate |
| 2 | name | string | 3 to 100 (OPEN: 255) | valid; min; max | missing; blank; empty; null; special chars; min-1; max+1 |
| 3 | description | string | 3 to 100 when sent (OPEN: 255) | present; omitted; min; max | empty; special chars; min-1; max+1 |
| 4 | category | uuid | must exist | valid | missing; null; not a UUID; not found; inactive |
| 5 | supplier | uuid | must exist | valid | missing; null; not a UUID; not found |
| 6 | price | number | not negative (proposed) | valid | missing; null; non-numeric; negative |
| 7 | quantity | integer | whole; not negative (proposed) | valid | missing; null; non-numeric; negative; decimal |
| 8 | unit | string | 1 to 100 when sent (OPEN: 255) | present; omitted; min; max | empty; special chars; max+1 |

## Dynamic data
| # | Field | Category | Description of the value | Resolution (query, API, create) | Status |
| :-: | :-- | :-- | :-- | :-- | :-- |
| 1 | code | RANDOM | Unique string of 3 to 100 characters per run | generated | resolved |
| 2 | name | RANDOM | Unique string of 3 to 100 characters per run | generated | resolved |
| 3 | description | STATIC | `Blue ballpoint pen` | fixed | resolved |
| 4 | category | PRECONDITION | `EXISTING_CATEGORY`: an ACTIVE category id | Category API list or create first | TBD |
| 5 | supplier | PRECONDITION | `EXISTING_SUPPLIER`: a supplier id | Supplier API list or create first | TBD |
| 6 | price | STATIC | `12.5` | fixed | resolved |
| 7 | quantity | STATIC | `100` | fixed | resolved |
| 8 | unit | STATIC | `cái` | fixed | resolved |
| 9 | id, status, productCode | COMPUTED | Server generated; not in the request | n/a | n/a |
| 10 | token | PRECONDITION | A valid token of a user with `prod/product.create` | login API of API Authentication | TBD |

## Test data dependencies
| # | Dependency | How to prepare | Needed for |
| :-: | :-- | :-- | :-- |
| 1 | ACTIVE category and a supplier | Category API / Supplier API (list or create) | all positive rows |
| 2 | Existing product code (`EXISTING_PRODUCT_CODE`) | Create one product first with POST /product | duplicate rows |
| 3 | Soft-deleted product code | Create a product then `DELETE /product/{id}?isSoft=true` (needs `prod/product.delete`) | Code_Duplicate_SoftDeleted |
| 4 | INACTIVE category | Create a category with status INACTIVE (Category API) | Category_Inactive |
| 5 | User without `prod/product.create` | An existing role or user; TBD | Permission_NoCreate |
| 6 | Database check | Not decided: see QnA-A14 (type; schema; table `product`; columns; sample query; read-only account name; environment) | data-check steps (none yet) |
| 7 | Cleanup | Hard delete created products (`isSoft=false`); risk: OQ12 (cart or order that uses a product) | all create rows |

## Risks and gaps
| # | Id | Description | Severity | Impact | Mitigation |
| :-: | :-- | :-- | :-- | :-- | :-- |
| 1 | RG-1 | S1 has no required flags, limits or error codes; everything beyond the 8 fields and 201 rests on S2 and S3 | High | Expected values depend on a requirement page that is not approved | Ask the BA to approve S2 or confirm the OQ items |
| 2 | RG-2 | Length limits conflict (100 against 255) | High | Boundary max+1 rows cannot state an expected result | QnA-A4 |
| 3 | RG-3 | `isTesting()` defects in test environments (price not stored on create) | Medium | The price-stored case can fail by design | QnA-A8 |
| 4 | RG-4 | S3 was not re-read; its facts are second hand | Medium | A fact may be stale | Attach the raw document |
| 5 | RG-5 | Dashboard screen was not readable (OQ1) | Low | UI behaviour is out of scope here | Separate Web analysis |
| 6 | RG-6 | Envelope names conflict (`code` against `status`) | Medium | Response body assertions are tentative | QnA-A9 |
| 7 | RG-7 | Hard delete cleanup can break a cart or order (OQ12) | Medium | Test data cleanup | Use a dedicated test data prefix |

## Coverage check
- Fields in the source: 8 (S1 definitions.CreateProductRequest). Rows in the table: 8.
- Response fields in the source: envelope 3 plus ProductModel 10 (with nested category 4 and supplier 8). Rows in the table: 13 top-level rows (3 + 10).
- S2 rules in scope for Create: 10 (BR1 to BR8, BR12, BR13). Mapped to analysis rows: BR1 to Field validations 1,3,7,9,11,13; BR2 to BR-2, BR-D1 and validations 1,2; BR3 to BR-D1; BR4 to BR-D3 and BR-D5; BR5 to BR-D4; BR6 to BR-S1; BR7 to BR-3; BR8 to validations 3 to 6; BR12 to BR-A1 to BR-A3; BR13 to BR-D7.
- Error codes in the source for Create: 14 (S2 section 8 and 14.2, excluding PRODUCT_NOT_FOUND). Rows in the table: 14.

## QnA
Grouped by who answers. Ids stay fixed once sent.

| QnA | Question | Asked of | Blocks | Status |
| :-- | :-- | :-- | :-- | :-- |
| QnA-A1 | Are special characters and leading or trailing spaces allowed in code and name? Is the code trimmed? | BA | 5 design rows | drafted |
| QnA-A2 | Is an empty string accepted for the optional description? | BA | 1 design rows: Description_Empty | drafted |
| QnA-A3 | What is the final wording of the validation messages (OQ9)? | BA | 4 design rows: Code_Min2, Name_Min2, Description_Min2, Unit_Empty | drafted |
| QnA-A4 | Which length limits apply to code name description and unit: 3 to 100 (unit 1 to 100) as in S2, or up to 255 as in S3? | Product owner | 16 design rows | drafted |
| QnA-A5 | Must the category be ACTIVE to create a product? If yes, which error code and HTTP status are returned for an INACTIVE category? | Product owner | 1 design rows: Category_Inactive | drafted |
| QnA-A6 | Is a negative price or quantity rejected? Which error code does each return (S2 names PRODUCT_QUANTITY_INVALID only for quantity)? | Product owner | 2 design rows: Price_Negative, Quantity_Negative | drafted |
| QnA-A7 | May the code of a soft-deleted product be reused? (S2 AC3.3 shows PRODUCT_EXISTED but marks it pending.) | Product owner | 1 design rows: Code_Duplicate_SoftDeleted | drafted |
| QnA-A8 | Is the target environment an environment with the known `isTesting()` defects? If yes, which cases should be expected to fail? | Product owner | Product_Create_Success_PriceQuantityStored | drafted |
| QnA-A9 | Which envelope field names are real: `code` (Swagger) or `status` (S2)? On success is the body value 200 or 201? | Dev | response-body assertions of every case | drafted |
| QnA-A10 | What error code and message come back when category supplier price or quantity is missing or null? (S2 says only 'validation error'.) | Dev | 11 design rows | drafted |
| QnA-A11 | For an empty string in code or name, is the error PRODUCT_CODE_REQUIRED / PRODUCT_NAME_REQUIRED or PRODUCT_CODE / PRODUCT_NAME (length)? | Dev | 2 design rows: Code_Empty, Name_Empty | drafted |
| QnA-A12 | Is a wrong JSON type, a non-UUID category or supplier, malformed JSON or an unknown extra field rejected with 400? Which code? | Dev | 8 design rows | drafted |
| QnA-A13 | Where are the column lengths and nullability of table `product` (S2 section 15 says no DDL was found, OQ13)? Please confirm them. | DBA | length boundary rows and the data model | drafted |
| QnA-A14 | Does this write API need verification in a database? If yes: database type, schema, table and columns to compare, a sample query, the name of a read-only account (no password here), the environment, and the schema owner. | DBA | data-check steps (none are written yet) | drafted |
| QnA-A15 | Please attach the raw `second requirement document` (S3). Its facts here come from a earlier note, not from the file. | QE lead | confirmation of EV-3 and EV-9 | drafted |
