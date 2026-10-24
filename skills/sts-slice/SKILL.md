---
name: sts-slice
description: "Slice, Test, Ship: break an epic or feature into thin vertical slices, each a tracker issue with Given/When/Then examples. Use instead of writing specs, plans or story documents."
---

# Slice an epic (Slice, Test, Ship)

Turn an epic into a short, ordered list of thin vertical slices. Each slice is one tracker issue with a few concrete examples. Do not write spec, plan or story files to the repo. The examples are the spec, and the tests written from them later become the documentation.

Input: $ARGUMENTS (an epic description, a link to an epic, or a short idea).

## 1. Understand before slicing

- Read the code the epic will touch, so you only ask what the code cannot answer.
- Interview the developer with a few sharp questions at most: who uses this, what is the riskiest unknown, what is explicitly out of scope, and are there hard constraints (deadlines, contracts, regulations).
- Skip questions with obvious answers. Dig into edge cases and trade-offs they may not have considered.

## 2. Propose the slices

Propose 3 to 8 slices as a numbered list of one-line titles. Each slice must:

- Be **vertical**: it goes through every layer it needs (UI, API, storage) and delivers something a user or caller can observe. "Database schema" or "API layer" is never a slice.
- Be **shippable alone**: it can merge to main and run in production, behind a flag if needed.
- Fit in **0.5 to 2 days** of work. If it doesn't, split it.

Order them so that:

1. Slice 1 is the thinnest end-to-end path that works (the walking skeleton).
2. Risky unknowns come early. A large unknown can be a time-boxed **spike** slice whose code is thrown away and whose output is examples for later slices.
3. Polish, edge cases and admin features come last.

Show the list and wait for the developer to trim, merge or reorder it before writing issues.

## 3. Write issues for the next two slices only

Write full detail only for the next two slices. Keep later slices as one-line titles; what you learn from the first slices will change them.

Each issue has exactly this shape and nothing more:

```
Title: <verb-first, user-visible outcome>

Why: <one line: what this slice unlocks>
Not in this slice: <one line of explicit non-goals>

Given <context>
When <action>
Then <observable outcome>

(3 to 7 examples, using concrete values such as 100.00, SAVE10, "Code not recognised")
```

Rules for examples:
- Use real values, not placeholders. "A cart of 100.00" not "a cart with some total".
- Describe observable behaviour, never implementation ("the total is 90.00", not "DiscountService is called").
- Include at least one failure or rejection case.
- If an example needs more than five lines, the slice is too big.

## 4. Put the issues in the tracker

- If `gh` works in the repo, create GitHub issues with `gh issue create`, labelled `slice` and linked to the epic if there is one.
- If another tracker's CLI or MCP tool is available (Jira, Linear), use it.
- Otherwise print the issues as ready-to-paste text.

Keep the one-line titles of later slices as a checklist on the epic issue (or in your reply), not as separate files.

## 5. Hand off

End with the next slice's issue and the command to start it: `/sts-tdd <issue>`.

When a slice is merged and the team has learned something, run `/sts-slice` again on the remaining titles to detail the next two.

## Do not

- Write `spec.md`, `plan.md`, `stories.md` or any other Markdown file to the repo.
- Detail every slice up front.
- Describe technical design in issues. Design decisions happen in `/sts-tdd`, guided by the tests. If a decision is expensive to reverse (data model, public API, new dependency), suggest a short decision record in the same PR as the code.
