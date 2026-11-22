# Dynamic data

How to describe generated and pre-existing data in a TestAnalysis and a TestDesign. The skills do not require a specific framework.

## 1. Categories

| Category | Meaning | Resolution |
| :-- | :-- | :-- |
| RANDOM | A unique value per run | Generated at run time |
| PRECONDITION | An entity that must exist in the system | Looked up or created before the run |
| STATIC | A fixed value | Written in the template |
| COMPUTED | The server generates it | Not in the request |

## 2. Token names

Use the token names of the user's framework when the user gives them. If the user does not, describe the value in words and use the neutral names below. They are labels for the design, not code.

| Label | Meaning |
| :-- | :-- |
| `RANDOM_STRING` | Unique string, for example a timestamp plus a worker index |
| `RANDOM_UUID` | UUID v4 |
| `RANDOM_PHONE` | Phone number in the local format |
| `RANDOM_EMAIL` | Unique email address on a test domain |
| `DATE_TODAY`, `DATE_TODAY+{N}d`, `DATE_TODAY-{N}d` | Date relative to today |
| `DOB_AGE_{N}` | Date of birth for an age of N years |
| `DOB_EXACT_{N}` | Date of birth for exactly N years, used at an age boundary |
| `DOB_UNDER_{N}`, `DOB_OVER_{N}` | Under or over an age limit, for negative cases |

If the project marks a token as retired, do not write it.

## 3. Precondition keywords

Name them `{STATUS}_{ENTITY}`, for example `ACTIVE_SUPPLIER`, `INACTIVE_SUPPLIER`, `EXPIRED_COUPON`. A keyword stays the same from the TestAnalysis to the TestDesign. A new keyword needs the user's confirmation (TD-DYN2).

Each keyword has a resolution: a query (table or collection, filter), a list API, or create-first. Where the user cannot say yet, write `TBD`. A `TBD` row blocks automation, not design.

## 4. Mapping rules

| Case | Dynamic data column |
| :-- | :-- |
| Positive | Every dynamic field gets its token |
| Field-validation negative | The field under test is fixed and invalid (`-`). Other fields keep their tokens |
| Precondition negative | The reference field uses the keyword variant (INACTIVE, EXPIRED) |
| Test data created by a run | Mark it with the project's test data prefix, so cleanup can find it |
