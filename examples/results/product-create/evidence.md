---
artifact: TestAnalysis   # evidence ledger
feature: ProductCreate
story: prod-product-api
status: Draft
approvedBy:
approvedAt:
derivedFrom: 
generatedBy: qe-api-analysis@0.1.0
---

# Evidence ledger: ProductCreate API

Sources: S1 = Swagger `/v2/api-docs` of prod-man; S2 = Confluence requirement page v1.1; S3 = second requirement document (read only through the earlier note).

| id | claim | source | ref | status |
| :-- | :-- | :-- | :-- | :-- |
| EV-1 | POST /product takes the body `CreateProductRequest` (application/json); `required: true` applies to the whole body only | spec | S1 swagger, POST /product, parameters | confirmed |
| EV-2 | Request has 8 fields: category (uuid) code description name price (number double) quantity (integer int64) supplier (uuid) unit | spec | S1 definitions.CreateProductRequest; S2 section 14.2 | confirmed |
| EV-3 | Required: code name category supplier price quantity. Optional: description unit. S1 has no field-level required flag (gap in S1) | spec | S2 section 14.2; S3 via earlier note | confirmed |
| EV-4 | Success returns HTTP 201 | spec | S1 responses.201; S2 section 14.2 | confirmed |
| EV-5 | Response envelope fields are `code` (int32) `data` `message` | spec | S1 definitions.ServiceResponse<ProductModel> | conflict |
| EV-6 | Response envelope fields are `status` `data` plus error code and message (names to confirm) | spec | S2 section 14 intro and OQ13 | conflict |
| EV-7 | Body status on success is 200 while HTTP is 201 (decision pending) | spec | S2 section 10 row 4 and OQ10 | open |
| EV-8 | code and name are 3 to 100 characters and not blank; description 3 to 100 when sent; unit 1 to 100 when sent | spec | S2 BR2 BR8 and section 14.2 | conflict |
| EV-9 | String fields (code name description unit) allow up to 255 characters | spec | S3 via earlier note (raw document not re-read) | conflict |
| EV-10 | Code is stored in upper case; response `productCode` is upper case; new product `status = ACTIVE` | spec | S2 BR2 BR6 AC1.1 | confirmed |
| EV-11 | Code is unique and case-insensitive; a duplicate returns 400 PRODUCT_EXISTED (4001102) with the code in the message | spec | S2 BR3 AC3.1 AC3.2 section 8 | confirmed |
| EV-12 | The code of a soft-deleted product stays taken: 400 PRODUCT_EXISTED (rule pending) | spec | S2 AC3.3 and OQ5 | open |
| EV-13 | Category must exist (404 CATEGORY_NOT_FOUND 4044001); supplier must exist (404 SUPPLIER_NOT_FOUND 4045001); category is checked first | spec | S2 BR4 BR5 AC4.1 to AC4.3 section 8 | confirmed |
| EV-14 | Category must be ACTIVE | spec | S3 via earlier note and S2 section 10 row 2 | conflict |
| EV-15 | Service checks only that the category exists; an inactive category is accepted today | spec | S2 BR4 AC4.4 and OQ3 | conflict |
| EV-16 | Price and quantity stored and returned are exactly those sent | spec | S2 BR7 AC1.2 | confirmed |
| EV-17 | Negative price or quantity is rejected (proposed): quantity uses PRODUCT_QUANTITY_INVALID (4001104); price code not defined; today the product is stored | spec | S2 BR13 AC2.9 and OQ4 | open |
| EV-18 | Validation codes: code missing or blank PRODUCT_CODE_REQUIRED; code length PRODUCT_CODE; name missing or blank PRODUCT_NAME_REQUIRED; name length PRODUCT_NAME; description length PRODUCT_DESC; unit length PRODUCT_UNIT. All HTTP 400 | spec | S2 AC2.1 to AC2.6 section 8 | confirmed |
| EV-19 | Message texts of the validation codes (wording to confirm against the i18n files) | spec | S2 section 8 note and OQ9 | open |
| EV-20 | Category supplier price or quantity missing returns HTTP 400 validation error; the error code and message are not stated | spec | S2 AC2.7 | open |
| EV-21 | No token or invalid token returns 401; valid token without `prod/product.create` returns 403; wrong HTTP method returns 405 | spec | S2 BR12 AC8.1 to AC8.3 section 14.2; S3 status list | confirmed |
| EV-22 | Auth is the `Authorization` header (Swagger type apiKey) carrying a Bearer token (S2). Token value is never recorded | spec | S1 securityDefinitions; S2 section 14 | confirmed |
| EV-23 | Description and unit omitted: HTTP 201 and both are absent from the response | spec | S2 AC1.3 | confirmed |
| EV-24 | A created product is returned by GET /product/{id}/detail and GET /product/search?keyword= | spec | S2 AC1.4 | confirmed |
| EV-25 | Check order: input validation, then code unique, then category exists, then supplier exists | spec | S2 section 4 flowchart | confirmed |
| EV-26 | Empty string for code or name: HTTP 400 is expected (blank) but the error code is not stated (REQUIRED or length) | spec | S2 AC2.1 (blank) and AC2.2 (length) | open |
| EV-27 | Explicit `null` for any field: behaviour not stated | spec | none; S2 14.2 says price and quantity are 'not null' | open |
| EV-28 | Special characters, accented or emoji characters, and leading or trailing spaces in code name description unit: behaviour not stated | spec | none | open |
| EV-29 | Wrong JSON type (string for a number or number for a string) and a non-UUID category or supplier: behaviour not stated | spec | none | open |
| EV-30 | Malformed JSON, unknown extra field: behaviour not stated | spec | none | open |
| EV-31 | Description sent as an empty string: accepted or rejected is not stated | spec | S2 14.2 says 3 to 100 when sent | open |
| EV-32 | Test environments have `isTesting()` defects: price not stored on create (a known defect) | spec | S2 section 10 row 7 and OQ11 | open |
