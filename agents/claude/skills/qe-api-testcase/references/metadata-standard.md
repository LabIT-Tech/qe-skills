# Test metadata standard

Default classification for test cases. A team can replace it. A deviation the team declares is not a defect. Each automation framework maps these fields to its own mechanism (tags, groups, a data file field).

## 1. Fields

| Field | Level | Values |
| :-- | :-- | :-- |
| Suite | per case | One of `DAILY`, `SMOKE`, `REGRESSION`, `ALL` |
| Type | per case | One of 8 values, first matching rule wins |
| Module | per feature or data file | Coarse business function, from the team |
| Author | per test | From the team's config. Not found, ask. Never invent |

## 2. Suite: one value per case

| Suite | Meaning | Rule |
| :-- | :-- | :-- |
| `DAILY` | Daily health check | One representative per main flow. Hand-picked with the team, not expanded per dimension |
| `SMOKE` | System health | At least one happy path per API (`POSITIVE`, a mid-range value, never a valid boundary). One more per real business dimension: plan, age band, payment method. A rejected-boundary guard rail is allowed, at most one each |
| `REGRESSION` | Default tier, aim for 70 to 80 percent of cases | Everything not in another tier. Cap `VALIDATION` at 25 cases per API (by `dataId`), the rest go to `ALL` |
| `ALL` | Release or nightly | Validation overflow, E2E cases not chosen for DAILY or SMOKE, cross-cutting checks repeated across APIs (keep a small sample in REGRESSION) |

- One value only. No `"SMOKE,REGRESSION"`, no `SANITY`, no `E2E` as a suite.
- A runtime filter on Suite is a plain match with no containment. Combine with commas: `SUITE=DAILY,SMOKE`.
- Every API has at least one happy path in DAILY or SMOKE. If none exists, report a coverage gap. Do not promote a negative case in its place.

## 3. Type: first matching rule wins

| # | Type | Use when |
| :-: | :-- | :-- |
| 1 | `E2E` | A case of a multi-step flow (several APIs chained) |
| 2 | `SECURITY` | Real injection or traversal only: SQL injection, XSS, path traversal |
| 3 | `PERMISSION` | Real access control: forbidden, not the owner, role denied |
| 4 | `BOUNDARY` | Explicit min, max or edge: BVA, ExceedMax, TooLong, TooShort |
| 5 | `VALIDATION` | Format, mandatory, constraint: missing, null, empty, invalid format or enum. Includes a missing, invalid or expired token or API key |
| 6 | `BUSINESS_RULE` | Calculation, workflow, rule engine |
| 7 | `NEGATIVE` | Other expected-error cases |
| 8 | `POSITIVE` | Default: expected success |

Disambiguation:
- Token, API key or session missing, invalid or expired: `VALIDATION`, not `PERMISSION` or `SECURITY`.
- Forbidden, another user's resource, action not allowed for the role: `PERMISSION`.
- Missing required field, empty, wrong format, plain special characters: `VALIDATION`.
- Over max length, explicit min or max: `BOUNDARY`.
- Injection or traversal payload: `SECURITY`.
- Id not found, wrong route, status not allowed for the action: `NEGATIVE`.

## 4. Checklist

- [ ] Every case has exactly one Suite and one Type from the lists above
- [ ] At least one DAILY or SMOKE happy path per API
- [ ] Token cases are `VALIDATION`
