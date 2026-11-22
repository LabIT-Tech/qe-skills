# Contributing

Thanks for helping. These skills are for quality engineers, so a change should make a skill more reliable, not longer.

The skill source is maintained by LabIT. You help by reporting what you find and proposing rules. The maintainers apply the change, test it and credit you in the release notes.

Vietnamese and English are both welcome. Write in the language you are most precise in.

## How to reach us

- Zalo group: https://zalo.me/g/ijzixj103
- Email: vn.labit@gmail.com
- An issue in this repository

## What to send

| Kind | Example |
| :-- | :-- |
| A gap found while running a skill | "The skill assumed `price` was required" |
| A new rule | One line, with the failure it prevents |
| A domain pack | Rules for a domain such as e-commerce or healthcare |
| A new test case format | A column set for another tracker |
| A sample API | Invented input files like those in `examples/` |
| A new skill idea | Web or E2E analysis, test case review. Open an issue first |

## How to report a gap

1. Run the skill on an input and write down what went wrong. Use `examples/sample-api/` if you cannot share yours.
2. State the failure as one rule, one line, and say which skill and step it belongs to.
3. Attach the before result. If you have tried a fix, attach the after result too.
4. Say which agent you used (Claude or Kiro) and its version.

## Style rules for a proposed rule

- A rule says what to do and why. A rule with no failure behind it should go.
- Every rule is testable. "Be thorough" is not a rule.
- Keep skill text short. Long tables belong in reference files.
- No company names, internal system names, paths, tokens or personal data. Use invented data.
- No secrets in examples.

## Checklist for an issue

- [ ] The failure and the proposed rule are described
- [ ] Before (and after, if you have it) results are attached
- [ ] No private data

## Conduct

Be direct and kind. Review the rule, not the person.

By contributing, you agree that your contribution is licensed under the MIT License, with copyright held by LabIT (vn.labit@gmail.com).
