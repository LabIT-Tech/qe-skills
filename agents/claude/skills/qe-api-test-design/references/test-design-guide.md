# Test design guide

Rules, techniques and process for `qe-api-test-design`. Basis: ISTQB CTFL 4.0 and CTAL-TA black-box techniques, plus access control from CTAL-TTA.

## 1. Techniques applied to an API

### EP: equivalence partitioning

Apply to every request field. One representative per valid partition, one scenario per invalid partition.

| Field type | Valid partitions | Invalid partitions |
| :-- | :-- | :-- |
| String, required | Non-empty valid value | Empty `""`, blank `"  "`, null, missing, special characters, over max length |
| Enum | One random valid value | Value outside the list, empty, null |
| Number with range | Inside the range | Below min, above max, non-numeric, null |
| Boolean | `true`, `false` | null, non-boolean (`"yes"`, `1`) |
| Array, mandatory | One item, several items | null, `[]`, missing |
| Date | Valid format and range | Invalid format, out of range, null |
| Object | All required inner fields present | null, `{}`, missing inner field |

Positive scenarios use one representative per valid partition. Do not cover every enum value, unless a value has its own business behaviour with a different expected result. Then that value gets one scenario.

### BVA: boundary value analysis

Use when a source states a min or max. Three-value form: min-1, min, min+1, max-1, max, max+1.

- String length: max and max+1.
- Number range: three-value.
- Date or age: use the boundary dates in the data tokens.
- Array size: min and max items, plus or minus one.
- If the source states no limit, write `OPEN`. Do not assume one.
- Do not apply BVA to enums.

### DT: decision table

Use when the result depends on two or more conditions that combine, such as a conditional required field, a role and action matrix, or a multi-condition rule. State whether the table is full or single-fault. Each rule column maps to one scenario.

| Conditions and rules | R1 | R2 | R3 | R4 |
| :-- | :-: | :-: | :-: | :-: |
| Condition 1 | Y | Y | N | N |
| Condition 2 | Y | N | Y | N |
| Expected | 200 plus A | 200 plus B | 200 plus B | 400 |

Self-check: is there a rule whose output depends on two or more conditions? If yes, a decision table exists.

### ST: state transition

Use when the entity has a lifecycle. Label a scenario ST only when a transition (state, event, state) is verified. Checking one state is not ST.

| Aspect | Coverage |
| :-- | :-- |
| Valid transition | One positive scenario each |
| Invalid transition | At least one, expecting an error, or state why it is omitted |
| Terminal state | An action on it expects an error |
| Initial state | Create, then verify the default state |

### EG: error guessing

A supplement drawn from experience. If the spec is silent on the expected result, write `OPEN`. Do not invent it.

| Pattern | What to test |
| :-- | :-- |
| Duplicate submit | Same request twice |
| Concurrent update | Two requests on the same entity |
| Stale reference | Reference to a deleted or inactive entity |
| Overflow | String longer than the column |
| Encoding | Emoji, accented characters, CJK |
| Null and missing | `"field": null` against an absent field |
| Whitespace only | Spaces or tabs |
| Type coercion | `"123"` instead of `123` |

### AC: access control (authentication and authorization)

Authentication asks who the caller is. Authorization asks what the caller may do. Cover both for every protected endpoint. Basis: ISTQB CTAL-TA (error guessing, decision tables for role and action) and the access-control checks of CTAL-TTA security testing.

Authentication (Authen):

| Case | Typical scenario | Expected result |
| :-- | :-- | :-- |
| Token missing | No credential header | From the spec, else `OPEN` |
| Token empty | Header present, value empty | From the spec, else `OPEN` |
| Token malformed | Not a valid token format | From the spec, else `OPEN` |
| Token expired | Valid format, past expiry | From the spec, else `OPEN` |
| Token with a bad signature | Altered payload or wrong key | From the spec, else `OPEN` |
| Wrong scheme | `Basic` where `Bearer` is required | From the spec, else `OPEN` |
| Revoked or logged-out token | Token used after logout | Only when the spec states revocation |

Authorization (Author):

| Case | Typical scenario | Expected result |
| :-- | :-- | :-- |
| Role without the permission | Authenticated caller whose role lacks the required permission | From the spec, else `OPEN` |
| Role matrix | One scenario per role and action pair named in the spec (use DT) | From the spec |
| Permitted role | One positive per role the spec allows | From the spec |
| Missing scope | Token valid, scope or permission code absent | From the spec, else `OPEN` |
| Ownership | Caller reads or changes another user's resource (IDOR) | From the spec, else `OPEN` |
| Cross-tenant | Caller from tenant A uses an id from tenant B | Only when the spec states tenants |
| Privilege escalation | Caller sets a role or owner field in the body | Only when the spec states the field is protected |

Rules for AC:

- The expected status for 401 and 403 comes from the source. Do not assume 401 for authentication and 403 for authorization when the source is silent.
- A role, permission code or tenant that the sources do not name is not tested. Ask in the QnA.
- Use accounts or tokens from the test data. Never write a real token or password.
- Direct service-level attacks (token forgery tools, brute force, rate limits) go to the security suite under TQ-L1.

### PW: pairwise

Use when three or more independent parameters make the full combination too large. Each pair of values appears at least once.

### UC: use case

Use when the API is one step in a flow. Main flow, alternative flow, and one exception flow that fails midway.

## 2. Technique selection

| API type | Primary | Secondary |
| :-- | :-- | :-- |
| Create | EP, BVA, EG | DT for conditional fields |
| Read or list | EP, BVA (paging) | ST for status filters |
| Update | EP, ST | EG for concurrent and stale |
| Delete | ST, EG | EP for soft against hard delete |
| Validation API | EP, BVA, DT | PW |
| Lookup or config | EP | EG for cache and stale |
| Step in a flow | UC, EG | EP for auth |
| Any protected endpoint | AC | DT for the role and action matrix |

## 3. Scope

In scope: field validation, positive and negative scenarios, every error code, authentication, response structure for the positive case, business rules, and data verification for write operations when the TestAnalysis names a data store.

Out of scope unless the user asks: performance and load, security testing beyond access control, UI, cross-system integration, any scenario inferred without evidence.

## 4. Rules

| ID | Rule |
| :-- | :-- |
| TD-G1 | Each item says what to test, not how |
| TD-G2 | Each item traces to the TestAnalysis through a Source id |
| TD-G3 | No inference beyond the documents |
| TD-G4 | No duplicates |
| TD-G5 | One objective per item |
| TD-API1 | Focus on the API contract |
| TD-API2 | At least one positive with all valid parameters |
| TD-API3 | Required string: EMPTY, MISSING, INVALID, SPECIAL_CHARS, and MAX_LENGTH only when a limit is stated |
| TD-API4 | Enum: INVALID, EMPTY, NULL |
| TD-API5 | Each error code has at least one scenario |
| TD-API6 | Authentication: token missing and token empty. Expired, malformed, bad-signature and wrong-scheme cases when the sources describe the scheme, or from shared auth data when the project has it |
| TD-API7 | Field with min or max: boundary scenarios |
| TD-API8 | Optional string: SPECIAL_CHARS, MAX_LENGTH (if a limit is stated), INVALID |
| TD-API9 | Number: INVALID_TYPE and NEGATIVE. With a range: boundary |
| TD-API10 | Enum positive uses one random valid value |
| TD-API11 | Mandatory array positive has two or more elements |
| TD-API12 | Mandatory array: NULL, EMPTY_ARRAY, MISSING |
| TD-API13 | Each access-control rule (BR-A) has at least one PERMISSION scenario |
| TD-AC1 | Each role named in the sources has a scenario for each action it may and may not do (DT) |
| TD-AC2 | An endpoint that takes a resource id has an ownership scenario when the sources state who owns the resource |
| TD-AC3 | 401 and 403 expectations come from the sources, never assumed |
| TD-AC4 | A role, scope or tenant the sources do not name becomes an `OPEN` question, not a scenario |
| TD-DYN1 | Every dynamic field has an entry in the dynamic data summary |
| TD-DYN2 | A new keyword that the TestAnalysis did not confirm: ask the user |
| TD-DYN3 | A dynamic field with status `TBD` is flagged as blocking automation |

## 5. Completion checklist (TD-STOP)

| # | Check |
| :-: | :-- |
| 1 | Every field covered (a required string has all its negative types) |
| 2 | Every error code has a scenario |
| 3 | Every business rule has a scenario |
| 4 | No scenario rests on inference |
| 5 | Every enum has one random valid positive |
| 6 | Every array has a multi-item positive and null, empty, missing negatives |
| 7 | Every stated boundary has min, max and mid |
| 8 | Positives are minimal, no cartesian product |
| 9 | Every scenario has a technique label |
| 10 | A decision table exists where two or more conditions combine |
| 11 | At least two error-guessing scenarios |
| 12 | State transition applied if the entity has a lifecycle |
| 13 | Every scenario has a Source. None means `OPEN` and an empty Expected |
| 14 | Counts re-derived from the TestAnalysis tables |

Auto-correct structure problems silently: remove test steps, split items with two objectives, shorten a full response body to a high-level expectation. Flag content problems to the user.

## 6. Process

### Step 3: generate scenarios

| Group | Items |
| :-- | :-- |
| Positive | Happy path (TD-API2), enum variation, array variation, boundary, boolean true and false, optional field present and absent |
| Negative, field validation | EMPTY, BLANK, MISSING, INVALID, SPECIAL_CHARS, MAX_LENGTH, NULL, EMPTY_ARRAY, INVALID_TYPE, NEGATIVE |
| Negative, authentication | TOKEN_MISSING, TOKEN_EMPTY, and TOKEN_MALFORMED, TOKEN_EXPIRED, TOKEN_BAD_SIGNATURE, WRONG_SCHEME when the sources allow |
| Negative, authorization | ROLE_FORBIDDEN, MISSING_SCOPE, NOT_OWNER, CROSS_TENANT when the sources allow |
| Rules | One per business rule, DT for combined conditions, ST for lifecycle |
| Error guessing | At least two edge cases |

### Step 4: name each scenario

`dataId` pattern: `{Module}_{Action}_{Condition}`.

| Kind | Pattern | Example |
| :-- | :-- | :-- |
| Positive | `{Module}_{Action}_Success_{Variant}` | `Product_Create_Success_ValidParams` |
| Field negative | `{Module}_{Action}_{Field}_{Type}` | `Product_Create_Name_Empty` |
| Auth negative | `{Module}_{Action}_Token_{Type}` | `Product_Create_Token_Missing` |
| Permission negative | `{Module}_{Action}_Perm_{Type}` | `Product_Create_Perm_RoleForbidden` |

The `dataId` is final once the TestDesign is approved (identifier chain, `conventions.md` section 1). It must be unique in the file.

Type and Suite columns follow the metadata standard in `qe-api-testcase/references/metadata-standard.md`. If that skill is not installed, use these values. Type, first match wins: `E2E`, `SECURITY`, `PERMISSION`, `BOUNDARY`, `VALIDATION`, `BUSINESS_RULE`, `NEGATIVE`, `POSITIVE`. Missing, invalid or expired token is `VALIDATION`. A valid caller without the right role, scope or ownership is `PERMISSION`. Suite is exactly one of `DAILY`, `SMOKE`, `REGRESSION`, `ALL`. The critical happy path is `DAILY`, one happy path per API is `SMOKE`, everything else is `REGRESSION`.

### Step 5: map dynamic data

Take the baseline from the TestAnalysis dynamic data table.

- Positive: every dynamic field gets its token.
- Field-validation negative: the field under test holds a fixed invalid value (`-`), the other fields keep their dynamic tokens.
- Precondition negative: the reference field uses the keyword variant (INACTIVE, EXPIRED).

See `dynamic-data.md` for token naming.

### Step 6: check coverage

Run the TD-STOP checklist. Fix structure problems. Show content problems at the gate.

## 7. Technique labels

`EP`, `BVA`, `DT`, `ST`, `EG`, `PW`, `UC`.
