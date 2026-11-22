---
artifact: TestDesign
feature: ProductCreate
story: prod-product-api
status: Draft
approvedBy:
approvedAt:
derivedFrom: TestAnalysis-ProductCreate_API.md (draft)
generatedBy: qe-api-test-design@0.1.0
---

# Test Design: ProductCreate API

## Open decisions
- 26 of 67 scenarios are OPEN (no stable source; Expected empty): Code_Max101, Name_Max101, Description_Max101, Unit_Max101, Code_Null, Code_SpecialChars, Name_Null, Name_SpecialChars, Description_Empty, Description_SpecialChars, Unit_SpecialChars, Category_Null, Category_InvalidFormat, Supplier_Null, Supplier_InvalidFormat, Price_InvalidType, Price_Negative, Quantity_InvalidType, Quantity_Negative, Quantity_Decimal, Code_Duplicate_SoftDeleted, Category_Inactive, Body_MalformedJson, Body_UnknownField, Code_LeadingTrailingSpaces, Price_StringNumber.
- The input TestAnalysis is Draft (6 conflict and 12 open rows). The design cannot be Approved until the reviewer settles them (EV-2).
- 6 dynamic fields are TBD and block automation, not design: category (EXISTING_CATEGORY); supplier (EXISTING_SUPPLIER); token (USER_WITHOUT_PRODUCT_CREATE); INACTIVE_CATEGORY; SOFT_DELETED_PRODUCT_CODE; EXISTING_PRODUCT_CODE.

## Document info
| Item | Value |
| :-- | :-- |
| Document | TestDesign-ProductCreate_API.md |
| Version | 1.0 |
| Date | 2026-11-21 |
| Author | LabIT QE (draft by qe-api-test-design) |
| Analysis file and version | TestAnalysis-ProductCreate_API.md, 1.0 (Draft) |

## Change history
| Version | Date | Author | Change | Reason |
| :-: | :-- | :-- | :-- | :-- |
| 1.0 | 2026-11-21 | LabIT QE | First design | From Analysis 1.0 |

## Test design table
Boundary technique: two-value BVA on the four string fields (min-1 and min; max and max+1). `STRING_LEN_n` is a string of exactly n characters.
| # | dataId | Description | Type | Suite | Expected (high level) | Source | Technique | Dynamic data |
| :-: | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | Product_Create_Success_ValidParams | Happy path: all 8 fields valid | POSITIVE | DAILY | 201; product returned with a new id; productCode in upper case; status ACTIVE | EV-4, EV-10, EV-2 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 2 | Product_Create_Success_OptionalOmitted | Optional fields description and unit omitted | POSITIVE | SMOKE | 201; description and unit absent from the response | EV-23, EV-3 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 3 | Product_Create_Success_PriceQuantityStored | Price and quantity returned exactly as sent | BUSINESS_RULE | REGRESSION | 201; price 12.5 and quantity 100 returned as sent | EV-16, EV-32 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 4 | Product_Create_Success_CodeLowercase | Lower-case code is stored and returned in upper case | BUSINESS_RULE | REGRESSION | 201; productCode returned in upper case | EV-10 | EP | code:RANDOM_STRING (lower case); name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 5 | Product_Create_Success_ReadBack | Created product is returned by detail and search | E2E | ALL | 201; then detail and search return the same product | EV-24 | UC | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 6 | Product_Create_Code_Min3 | `code` has the minimum length (3) | BOUNDARY | REGRESSION | 201; product created | EV-8, EV-18 | BVA | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 7 | Product_Create_Code_Min2 | `code` is one character below the minimum (2) | BOUNDARY | REGRESSION | 400; length rejected | EV-8, EV-18, EV-19 | BVA | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 8 | Product_Create_Code_Max100 | `code` has the maximum length of the stricter source (100) | BOUNDARY | REGRESSION | 201; product created (valid under both sources) | EV-8, EV-9 | BVA | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 9 | Product_Create_Code_Max101 | `code` is one character above 100: S2 says reject; S3 allows up to 255 | BOUNDARY | REGRESSION |  | OPEN; EV-8, EV-9 | BVA | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 10 | Product_Create_Name_Min3 | `name` has the minimum length (3) | BOUNDARY | REGRESSION | 201; product created | EV-8, EV-18 | BVA | code:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 11 | Product_Create_Name_Min2 | `name` is one character below the minimum (2) | BOUNDARY | REGRESSION | 400; length rejected | EV-8, EV-18, EV-19 | BVA | code:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 12 | Product_Create_Name_Max100 | `name` has the maximum length of the stricter source (100) | BOUNDARY | REGRESSION | 201; product created (valid under both sources) | EV-8, EV-9 | BVA | code:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 13 | Product_Create_Name_Max101 | `name` is one character above 100: S2 says reject; S3 allows up to 255 | BOUNDARY | REGRESSION |  | OPEN; EV-8, EV-9 | BVA | code:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 14 | Product_Create_Description_Min3 | `description` has the minimum length (3) | BOUNDARY | REGRESSION | 201; product created | EV-8, EV-18 | BVA | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 15 | Product_Create_Description_Min2 | `description` is one character below the minimum (2) | BOUNDARY | REGRESSION | 400; length rejected | EV-8, EV-18, EV-19 | BVA | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 16 | Product_Create_Description_Max100 | `description` has the maximum length of the stricter source (100) | BOUNDARY | REGRESSION | 201; product created (valid under both sources) | EV-8, EV-9 | BVA | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 17 | Product_Create_Description_Max101 | `description` is one character above 100: S2 says reject; S3 allows up to 255 | BOUNDARY | REGRESSION |  | OPEN; EV-8, EV-9 | BVA | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 18 | Product_Create_Unit_Min1 | `unit` has the minimum length (1) | BOUNDARY | REGRESSION | 201; product created | EV-8, EV-18 | BVA | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 19 | Product_Create_Unit_Max100 | `unit` has the maximum length of the stricter source (100) | BOUNDARY | REGRESSION | 201; product created (valid under both sources) | EV-8, EV-9 | BVA | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 20 | Product_Create_Unit_Max101 | `unit` is one character above 100: S2 says reject; S3 allows up to 255 | BOUNDARY | REGRESSION |  | OPEN; EV-8, EV-9 | BVA | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 21 | Product_Create_Unit_Empty | `unit` is an empty string (one below the minimum of 1) | BOUNDARY | REGRESSION | 400; PRODUCT_UNIT | EV-8, EV-18, EV-19 | BVA | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 22 | Product_Create_Code_Missing | `code` is missing | VALIDATION | REGRESSION | 400; PRODUCT_CODE_REQUIRED | EV-18, EV-19, EV-3 | EP | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 23 | Product_Create_Code_Blank | `code` is blank (spaces only) | VALIDATION | REGRESSION | 400; PRODUCT_CODE_REQUIRED | EV-18, EV-19 | EP | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 24 | Product_Create_Code_Empty | `code` is an empty string | VALIDATION | REGRESSION | 400; error code TBD | EV-26 | EP | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 25 | Product_Create_Code_Null | `code` is null | VALIDATION | REGRESSION |  | OPEN; EV-27 | EP | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 26 | Product_Create_Code_SpecialChars | `code` has special characters | VALIDATION | REGRESSION |  | OPEN; EV-28 | EP | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 27 | Product_Create_Name_Missing | `name` is missing | VALIDATION | REGRESSION | 400; PRODUCT_NAME_REQUIRED | EV-18, EV-19, EV-3 | EP | code:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 28 | Product_Create_Name_Blank | `name` is blank (spaces only) | VALIDATION | ALL | 400; PRODUCT_NAME_REQUIRED | EV-18, EV-19 | EP | code:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 29 | Product_Create_Name_Empty | `name` is an empty string | VALIDATION | ALL | 400; error code TBD | EV-26 | EP | code:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 30 | Product_Create_Name_Null | `name` is null | VALIDATION | ALL |  | OPEN; EV-27 | EP | code:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 31 | Product_Create_Name_SpecialChars | `name` has special characters | VALIDATION | ALL |  | OPEN; EV-28 | EP | code:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 32 | Product_Create_Description_Empty | `description` is an empty string | VALIDATION | ALL |  | OPEN; EV-31 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 33 | Product_Create_Description_SpecialChars | `description` has special characters | VALIDATION | ALL |  | OPEN; EV-28 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 34 | Product_Create_Unit_SpecialChars | `unit` has special characters | VALIDATION | ALL |  | OPEN; EV-28 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 35 | Product_Create_Category_Missing | `category` is missing | VALIDATION | REGRESSION | 400; error code TBD | EV-20, EV-3 | EP | code:RANDOM_STRING; name:RANDOM_STRING; supplier:EXISTING_SUPPLIER |
| 36 | Product_Create_Category_Null | `category` is null | VALIDATION | REGRESSION |  | OPEN; EV-27 | EP | code:RANDOM_STRING; name:RANDOM_STRING; supplier:EXISTING_SUPPLIER |
| 37 | Product_Create_Category_InvalidFormat | `category` is not a UUID | VALIDATION | REGRESSION |  | OPEN; EV-29 | EP | code:RANDOM_STRING; name:RANDOM_STRING; supplier:EXISTING_SUPPLIER |
| 38 | Product_Create_Category_NotFound | `category` is a well-formed UUID that is not in the system | NEGATIVE | REGRESSION | 404; CATEGORY_NOT_FOUND | EV-13 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:UNKNOWN_UUID; supplier:EXISTING_SUPPLIER |
| 39 | Product_Create_Supplier_Missing | `supplier` is missing | VALIDATION | REGRESSION | 400; error code TBD | EV-20, EV-3 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY |
| 40 | Product_Create_Supplier_Null | `supplier` is null | VALIDATION | REGRESSION |  | OPEN; EV-27 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY |
| 41 | Product_Create_Supplier_InvalidFormat | `supplier` is not a UUID | VALIDATION | REGRESSION |  | OPEN; EV-29 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY |
| 42 | Product_Create_Supplier_NotFound | `supplier` is a well-formed UUID that is not in the system | NEGATIVE | REGRESSION | 404; SUPPLIER_NOT_FOUND | EV-13 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:UNKNOWN_UUID |
| 43 | Product_Create_Price_Missing | `price` is missing | VALIDATION | REGRESSION | 400; error code TBD | EV-20, EV-3 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 44 | Product_Create_Price_Null | `price` is null | VALIDATION | REGRESSION | 400; error code TBD | EV-27, EV-3 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 45 | Product_Create_Price_InvalidType | `price` is a non-numeric string | VALIDATION | REGRESSION |  | OPEN; EV-29 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 46 | Product_Create_Price_Negative | `price` is -1 (proposed rule; today the product is stored) | VALIDATION | REGRESSION |  | OPEN; EV-17 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 47 | Product_Create_Quantity_Missing | `quantity` is missing | VALIDATION | REGRESSION | 400; error code TBD | EV-20, EV-3 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 48 | Product_Create_Quantity_Null | `quantity` is null | VALIDATION | REGRESSION | 400; error code TBD | EV-27, EV-3 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 49 | Product_Create_Quantity_InvalidType | `quantity` is a non-numeric string | VALIDATION | REGRESSION |  | OPEN; EV-29 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 50 | Product_Create_Quantity_Negative | `quantity` is -1 (proposed rule; today the product is stored) | VALIDATION | REGRESSION |  | OPEN; EV-17 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 51 | Product_Create_Quantity_Decimal | `quantity` is a decimal number (1.5); the field is a whole number | VALIDATION | REGRESSION |  | OPEN; EV-29 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 52 | Product_Create_Code_Duplicate_Exact | Code already used by an existing product (same case) | BUSINESS_RULE | REGRESSION | 400; PRODUCT_EXISTED; no second product | EV-11 | EP | code:EXISTING_PRODUCT_CODE; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 53 | Product_Create_Code_Duplicate_OtherCase | Code equals an existing code in a different case (p001 against P001) | BUSINESS_RULE | REGRESSION | 400; PRODUCT_EXISTED | EV-11 | EP | code:EXISTING_PRODUCT_CODE_LOWERCASE; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 54 | Product_Create_Code_Duplicate_SoftDeleted | Code of a soft-deleted product (INACTIVE) is reused | BUSINESS_RULE | REGRESSION |  | OPEN; EV-12 | EP | code:SOFT_DELETED_PRODUCT_CODE; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 55 | Product_Create_Category_Inactive | Category exists but its status is INACTIVE | BUSINESS_RULE | REGRESSION |  | OPEN; EV-14, EV-15 | EP | code:RANDOM_STRING; name:RANDOM_STRING; category:INACTIVE_CATEGORY; supplier:EXISTING_SUPPLIER |
| 56 | Product_Create_Category_Supplier_BothUnknown | Category and supplier both unknown: category is reported first (DT-1 R6) | BUSINESS_RULE | REGRESSION | 404; CATEGORY_NOT_FOUND | EV-13, EV-25 | DT | code:RANDOM_STRING; name:RANDOM_STRING; category:UNKNOWN_UUID; supplier:UNKNOWN_UUID |
| 57 | Product_Create_DupCode_CategoryUnknown_Order | Duplicate code and unknown category together: code check comes first (DT-1 R7) | BUSINESS_RULE | REGRESSION | 400; PRODUCT_EXISTED | EV-11, EV-25 | DT | code:EXISTING_PRODUCT_CODE; name:RANDOM_STRING; category:UNKNOWN_UUID; supplier:EXISTING_SUPPLIER |
| 58 | Product_Create_Token_Missing | Authorization header is removed | VALIDATION | REGRESSION | 401 | EV-21, EV-22 | EP | - |
| 59 | Product_Create_Token_Empty | Authorization header is an empty string | VALIDATION | REGRESSION | 401 | EV-21, EV-22 | EP | - |
| 60 | Product_Create_Token_Invalid | Authorization header holds a malformed token | VALIDATION | REGRESSION | 401 | EV-21, EV-22 | EP | - |
| 61 | Product_Create_Permission_NoCreate | Valid token whose user lacks prod/product.create | PERMISSION | REGRESSION | 403; nothing stored | EV-21 | DT | token:USER_WITHOUT_PRODUCT_CREATE |
| 62 | Product_Create_Method_NotAllowed | Wrong HTTP method (PATCH) on /product | NEGATIVE | REGRESSION | 405 | EV-21 | EG | - |
| 63 | Product_Create_Body_Empty | Empty JSON object as the body | VALIDATION | ALL | 400; error code TBD | EV-20 | EG | - |
| 64 | Product_Create_Body_MalformedJson | Malformed JSON body | VALIDATION | ALL |  | OPEN; EV-30 | EG | - |
| 65 | Product_Create_Body_UnknownField | Valid body plus one unknown field | VALIDATION | ALL |  | OPEN; EV-30 | EG | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 66 | Product_Create_Code_LeadingTrailingSpaces | Code with leading and trailing spaces | VALIDATION | ALL |  | OPEN; EV-28 | EG | name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |
| 67 | Product_Create_Price_StringNumber | Price sent as a numeric string ("12.5") | VALIDATION | REGRESSION |  | OPEN; EV-29 | EG | code:RANDOM_STRING; name:RANDOM_STRING; category:EXISTING_CATEGORY; supplier:EXISTING_SUPPLIER |

Source column: an `EV-` id from `evidence.md`. `OPEN` means no stable source and an empty Expected.

## Dynamic fields summary
| # | dataId | Field | Keyword or token | Type and format | Resolution | Status |
| :-: | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | all rows that send a code | code | RANDOM_STRING | string 3 to 100 characters | generated | resolved |
| 2 | all rows that send a name | name | RANDOM_STRING | string 3 to 100 characters | generated | resolved |
| 3 | most rows | category | EXISTING_CATEGORY | uuid of an ACTIVE category | Category API list or create first | TBD (blocks automation) |
| 4 | most rows | supplier | EXISTING_SUPPLIER | uuid of a supplier | Supplier API list or create first | TBD (blocks automation) |
| 5 | Product_Create_Code_Duplicate_Exact; _OtherCase; DupCode_CategoryUnknown_Order | code | EXISTING_PRODUCT_CODE | code of a product created first | create first with POST /product | TBD (blocks automation) |
| 6 | Product_Create_Code_Duplicate_SoftDeleted | code | SOFT_DELETED_PRODUCT_CODE | code of a soft-deleted product | create, then DELETE with isSoft=true | TBD (blocks automation) |
| 7 | Product_Create_Category_Inactive | category | INACTIVE_CATEGORY | uuid of an INACTIVE category | create a category with status INACTIVE | TBD (blocks automation) |
| 8 | Product_Create_Category_NotFound; Supplier_NotFound; Category_Supplier_BothUnknown | category; supplier | UNKNOWN_UUID | well-formed uuid that is not in the system | RANDOM_UUID | resolved |
| 9 | Product_Create_Permission_NoCreate | token | USER_WITHOUT_PRODUCT_CREATE | valid token without prod/product.create | existing user; to confirm | TBD (blocks automation) |
| 10 | Product_Create_Success_CodeLowercase | code | RANDOM_LOWERCASE_CODE | lower-case string 3 to 100 characters | generated | resolved |

## Positive coverage preview
| Field | Type | Valid values | Used by (dataId) | Technique |
| :-- | :-- | :-- | :-- | :-- |
| code | string | 3 to 100 characters | Success_ValidParams; Code_Min3; Code_Max100; Success_CodeLowercase | EP; BVA |
| name | string | 3 to 100 characters | Success_ValidParams; Name_Min3; Name_Max100 | EP; BVA |
| description | string | 3 to 100 when sent | Success_ValidParams; Description_Min3; Description_Max100; Success_OptionalOmitted (absent) | EP; BVA |
| category | uuid | existing category | Success_ValidParams | EP |
| supplier | uuid | existing supplier | Success_ValidParams | EP |
| price | number | 12.5 | Success_PriceQuantityStored | EP |
| quantity | integer | 100 | Success_PriceQuantityStored | EP |
| unit | string | 1 to 100 when sent | Success_ValidParams; Unit_Min1; Unit_Max100; Success_OptionalOmitted (absent) | EP; BVA |

## Decision tables
### DT-1: order of checks (from the process flow in S2 section 4)
Limited-entry table of 7 rules; a full table of 4 binary conditions would have 16 columns and 9 of them have no distinct outcome.
| Conditions and rules | R1 | R2 | R3 | R4 | R5 | R6 | R7 |
| :-- | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| Input valid | N | Y | Y | Y | Y | Y | Y |
| Code unused | - | N | Y | Y | Y | Y | N |
| Category exists | - | - | N | Y | Y | N | N |
| Supplier exists | - | - | - | N | Y | N | - |
| Expected | 400 validation | 400 PRODUCT_EXISTED | 404 CATEGORY_NOT_FOUND | 404 SUPPLIER_NOT_FOUND | 201 | 404 CATEGORY_NOT_FOUND | 400 PRODUCT_EXISTED |

Maps to dataId: R1 `Product_Create_Code_Missing` (and the other validation rows); R2 `Product_Create_Code_Duplicate_Exact`; R3 `Product_Create_Category_NotFound`; R4 `Product_Create_Supplier_NotFound`; R5 `Product_Create_Success_ValidParams`; R6 `Product_Create_Category_Supplier_BothUnknown`; R7 `Product_Create_DupCode_CategoryUnknown_Order`.

## Coverage summary
Counts come from the TestAnalysis tables.
| Metric | Value |
| :-- | :-- |
| Total scenarios | 67 |
| Positive | 13 |
| Negative | 54 |
| Fields covered | 8 of 8 |
| Error codes covered | 14 of 14 (none missing) |
| Business rules covered (S2 BR in scope) | 10 of 10 |
| OPEN scenarios | 26 |
| Scenarios with a partial expected result (error code TBD) | 9 |
| Checklist violations found | see TD-STOP below |

## TD-STOP check
| # | Check | Result |
| :-: | :-- | :-- |
| 1 | Every field covered | Yes: 8 of 8; each required string has Missing, Blank, Empty, Null, SpecialChars |
| 2 | Every error code has a scenario | 14 of 14 |
| 3 | Every business rule has a scenario | 10 of 10 |
| 4 | No scenario rests on inference | Rows without a source are OPEN with an empty Expected |
| 5 | Every enum has one random valid positive | Not applicable: the request has no enum |
| 6 | Every array has positives and negatives | Not applicable: the request has no array |
| 7 | Every stated boundary has min and max | Yes, two-value; mid is covered by the valid rows |
| 8 | Positives are minimal | Yes |
| 9 | Every scenario has a technique label | Yes |
| 10 | A decision table exists | Yes: DT-1 |
| 11 | At least two error-guessing scenarios | Yes: 6 |
| 12 | State transition applied if there is a lifecycle | Not applicable (initial state only) |
| 13 | Every scenario has a Source | Yes (26 are OPEN) |
| 14 | Counts re-derived from the TestAnalysis tables | Yes |

Moved to another layer (TQ-L1): concurrent create of the same code (race) and token expiry go to the API or security suite; they have no source and are not in this table.

## QnA
Questions raised by the design. Analysis questions (QnA-A) are not repeated.
| QnA | Question | Asked of | Blocks | Status |
| :-- | :-- | :-- | :-- | :-- |
| QnA-D1 | Where do EXISTING_CATEGORY, EXISTING_SUPPLIER and the INACTIVE category come from: a list API, create-first, or fixed ids in the environment? | Dev | dynamic data of nearly every row (automation) | drafted |
| QnA-D2 | Which account or role has a valid token but no `prod/product.create`? | Dev | Product_Create_Permission_NoCreate | drafted |
| QnA-D3 | Is hard delete (`isSoft=false`) of the products created by these cases safe in the target environment, or can a cart or order depend on them (OQ12)? | Dev | cleanup of every create row | drafted |
| QnA-D4 | Should the success cases check the stored row in a database (table `product`: code, status, price, quantity)? If yes, answer QnA-A14 first. | DBA | data-check scenarios (none are written yet) | drafted |
| QnA-D5 | Two-value BVA is used on the four string fields (min-1 and min; max and max+1). Do you want three-value (add min+1 and max-1)? | QE lead | none; 8 more rows if yes | drafted |
| QnA-D6 | Concurrent create of one code and token expiry have no source, so they are not scenarios. Confirm they go to the API or security suite (TQ-L1). | QE lead | none | drafted |

## Techniques coverage
| Technique | Count | Applied to |
| :-- | :-: | :-- |
| EP | 41 | required and optional strings; references; numbers; auth |
| BVA | 16 | code name description unit (two-value) |
| DT | 3 | DT-1 order of checks; permission |
| ST | 0 | not applicable: Create only sets the initial state (checked in Success_ValidParams) |
| EG | 6 | empty body; malformed JSON; unknown field; padded spaces; numeric string; wrong method |
| PW | 0 | not applied: no three independent parameters worth combining |
| UC | 1 | Success_ReadBack (create then detail and search) |
