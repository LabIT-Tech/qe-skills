# Quality rules

How a TestDesign is judged. Basis: ISTQB CTFL 4.0, CTAL-TA, CTAL-TM, CTAL-TAE. A rule here never overrides the project. A deviation the project declares is not a defect.

## 1. Risk, oracle, traceability

| ID | Rule |
| :-- | :-- |
| TQ-R1 | Priority comes from product risk (likelihood times impact), not habit |
| TQ-O1 | Every scenario has a source (acceptance criterion, business rule, evidence id or an accepted assumption). No source means `OPEN` and an empty expected value |
| TQ-C1 | Counts of acceptance criteria, rules and fields are re-derived from the source tables, never copied from a heading or a summary line |
| TQ-C2 | The coverage matrix is by scenario and verification point. A vague point such as "handled reasonably" does not cover a criterion |

## 2. Techniques: what each label must mean

| ID | Rule |
| :-- | :-- |
| TQ-T1 | EP: list valid and invalid partitions, at least one representative per partition, no cartesian product |
| TQ-T2 | BVA: only on an ordered domain whose boundary the source states. Say two-value or three-value |
| TQ-T3 | DT: only with two or more conditions that combine. State whether full or single-fault |
| TQ-T4 | ST: label only when a transition is verified. Include one invalid transition or give the reason it is omitted |
| TQ-T5 | UC: each acceptance criterion has a main flow and, where the spec has one, an alternative flow |
| TQ-T6 | EG: drawn from a defect taxonomy. If the spec is silent, write `OPEN`, never invent |
| TQ-T7 | PW: when three or more independent parameters need combining |
| TQ-T8 | AC: label only when the caller identity or permission is the thing varied. Split Authen (who) from Author (what may be done). Expected status comes from the source |

## 3. Layering

| ID | Rule |
| :-- | :-- |
| TQ-L1 | What only direct service calls can reach (replay, bypass, race, rate limits per device or IP) goes to the API or security suite, listed with a reason. It is not dropped |

## 4. Optional domain pack: insurance and finance

Turn this on when the user says the API touches identity, authentication, money or contracts. Each touched item is covered, moved to another layer (TQ-L1), or omitted with a reason. Anything the spec is silent on becomes an `OPEN` question.

| ID | Check |
| :-- | :-- |
| TQ-D1 | Identity: uniqueness of phone, national id, email. One phone shared by several people |
| TQ-D2 | Authentication and OTP: expiry, old code invalid after resend, replay, wrong-attempt limit and lockout, request limits per phone and per device or IP |
| TQ-D3 | Consent to personal data: not pre-ticked, explicit, recorded with time and terms version. The legal basis and level need confirmation from Compliance |
| TQ-D4 | Secrets: password and OTP absent from logs, storage and screenshots |
| TQ-D5 | Data integrity: no duplicate record on double submit or lost connection (idempotency) |
| TQ-D6 | Information disclosure: an error that reveals whether an account exists |
| TQ-D7 | Audit trail when an account or contract is created or changed |
| TQ-D8 | Money and contracts: rounding, currency, effective dates, age bands, waiting and cooling-off periods, duplicate payments. Needs confirmation from Compliance or Actuarial |
| TQ-D9 | Infrastructure failure: timeout, 5xx, provider down |

The domain pack is a starting list. Other domains can add their own pack in a pull request.
