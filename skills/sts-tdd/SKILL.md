---
name: sts-tdd
description: "Slice, Test, Ship: implement one slice or story test-first. Propose a test list for approval, then red/green/refactor with a commit per green and a small PR."
---

# Test-driven implementation of one slice (Slice, Test, Ship)

Implement one slice using strict red, green, refactor. The approved test list is the plan and the spec. Do not write a plan file.

Input: $ARGUMENTS (an issue number or link, or a short description with examples).

## 1. Read

- Read the issue: its examples, "why" and "not in this slice" lines. If it is a GitHub issue, use `gh issue view`.
- Read the code the slice touches and the existing tests nearby. Note the test runner command, the test style and naming conventions, and how similar features are tested.
- If the issue has no concrete examples, stop and ask for them, or suggest running `/sts-slice` first.

## 2. Propose the test list, then STOP

Write an ordered list of test names in behaviour language, matching the project's test style. For example:

```
describe "Applying a discount code"
  it reduces the total by the code's percentage
  it rounds to the cent, half up
  it rejects an unknown code without changing the total
  it replaces an existing code instead of stacking
```

- Cover every example in the issue, each with at least one test.
- Order simplest first, so each test forces a small step.
- Include at least one test that goes through the public entry point (HTTP handler, CLI, UI component, public function), not only units.
- Add edge cases you think matter, marked as additions so the developer can cut them.
- Mention in one or two lines any design choice the tests imply (a new module, a changed signature).

**Wait for the developer to approve or edit the list before writing any code.** This is the main human checkpoint.

## 3. Loop through the list

For each test, in order:

1. **Red.** Write the test. Run it. Show that it fails, and that it fails for the expected reason (an assertion, not a typo or import error). If it passes straight away, stop and investigate. The behaviour may already exist, or the test may be wrong.
2. **Green.** Write the least production code that makes it pass. Run the full relevant suite, not only the new test.
3. **Commit.** Commit on green with a message that names the behaviour, such as `feat: reject unknown discount codes`.
4. **Refactor** if the code needs it, only while all tests are green. Run the tests after each step. Commit refactors separately with `refactor:` messages.

Then move to the next test. After each test, report one short line: the test name, red to green, and the commit.

## Rules

- **Never edit, weaken, skip or delete an approved test to reach green.** If a test looks wrong, stop and explain why.
- **No code without a failing test first.** If you wrote production code before its test, delete it and start the cycle again.
- **No behaviour nobody asked for.** If something seems needed, propose it as a new test-list item and ask.
- **Structural and behavioural changes never share a commit.** When both are needed, do the structural change first, on green.
- **Prefer real collaborators over mocks.** Mock only at system boundaries (network, clock, third-party services). Never mock the thing under test.
- **Commit only on green**, with no new compiler or linter warnings.
- If you go around the same failure twice, stop and tell the developer what you tried rather than looping.

## 4. Finish

- Run the full test suite, linter and type checker.
- Optionally have a fresh-context subagent review the diff against the issue's examples. It should flag only missing coverage, tests that assert implementation instead of behaviour, and untested code.
- Open a small PR. The description is short:
  - a link to the issue,
  - the final test list (this is the reviewer's spec),
  - anything the reviewer needs to decide,
  - one line on any decision that is expensive to reverse, with a short decision record in `docs/decisions/` only if one is warranted.

If the PR grows past roughly 400 changed lines, the slice was too big. Say so, and suggest splitting the rest into a new slice.
