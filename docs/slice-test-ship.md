# Slice, Test, Ship

*AI-TDD workflow · research · October 2026*

A spec-driven chain turns an epic into a spec, a plan and a set of stories before any code exists. Developers then spend their time reading Markdown instead of reading code. This report proposes a lighter loop where the tests are the spec, every change is a thin slice that ships in a day or two, and the only prose left is a short issue and the occasional decision record.

## Recommendation

**Move from "spec first, then code" to "examples first, then tests, then code," one thin vertical slice at a time.**

1. **Cut the epic into slices, not documents.** Each slice is a tracker issue with 3 to 7 concrete examples written as Given / When / Then. No `spec.md`, no `plan.md`.
2. **Review the test list before code exists.** This is the one mandatory human checkpoint, and it is short: a list of test names that reads like a spec.
3. **Let the agent run red, green, refactor** against that list, committing on every green, keeping refactors in separate commits.
4. **Review the PR from the tests down.** A fresh-context reviewer checks the diff against the examples, so the human reads tests first and code second.
5. **Write prose only for decisions that are hard to reverse**, as a short ADR that lives next to the code.

In skill terms, four spec-driven skills become two core ones (`/sts-slice` and `/sts-tdd`) plus two small optional ones (`/sts-review-slice` and `/sts-decide`, not yet written).

## Why a spec-driven chain hurts

The pain is the same one that independent reviewers of spec-driven tools reported in 2025 and 2026. It is a property of the approach, not of how the skills are written.

- **Review burden moves to Markdown.** Birgitta Böckeler, testing Kiro, spec-kit and Tessl for martinfowler.com, found the generated files "very verbose and tedious to review" and concluded: "I'd rather review code than all these markdown files."
- **One size fits nothing.** The same heavyweight flow runs for a small bug and a large epic. Böckeler called it "a sledgehammer to crack a nut" and noted that "small work packages almost seem counter to the idea of SDD."
- **It is waterfall with faster typists.** Spec, then plan, then stories, then code is a cascade. Feedback from running code arrives last, after the most expensive documents are already written and approved.
- **Specs drift, tests cannot.** Most SDD tools are "spec-first" with no maintenance story. The Markdown goes stale the week it is merged. A failing test cannot go stale silently, which is why tests make better long-lived documentation.
- **Control is partly illusory.** Böckeler "frequently saw the agent ultimately not follow all the instructions" in long specs. Thoughtworks put SDD in *Assess* and warned the field may be relearning the bitter lesson that hand-written rules for AI do not scale.
- **Small batches are what AI amplifies.** DORA's 2025 AI Capabilities Model lists working in small batches as one of seven practices that "amplify the positive influence of AI," and recommends teams "enforce the discipline" of small increments.

## What published approaches do

Five approaches cover the range from heavy specs to pure TDD. The pattern across them: the ones that keep quality high without heavy docs all put an executable check at the centre and keep each unit of work small.

| Approach | Unit of work | What humans read | Source of truth | Takeaway |
|---|---|---|---|---|
| Spec-driven tools (spec-kit, Kiro, Tessl) | Epic or feature | Spec, plan, task files | Markdown spec | Keep the up-front clarifying questions, drop the documents. |
| Claude Code best practices (Anthropic) | Task sized to one session | A plan in plan mode, only when needed | Tests and checks the agent can run | "If you could describe the diff in one sentence, skip the plan." Verification is the main lever. Writer / reviewer sessions, and one Claude writing tests for another. |
| Kent Beck, "Augmented Coding" | One test | A list of test names (`plan.md`) | Tests | The plan is just an ordered test list. "go" means implement the next test. Never mix structural and behavioural changes in one commit. |
| Superpowers skill set (obra) | Bite-sized task | Design doc and plan, in short sections | Tests, enforced by the skill | Strict red, green, refactor with a rule that code written before its test is deleted. Fresh subagent per task, separate reviewer. Still writes a design doc and plan. |
| Specification by example / BDD | Scenario | Given / When / Then examples | Executable scenarios | O'Reilly Radar's cost analysis puts the minimum total cost at "well-structured acceptance criteria or BDD scenarios": cheaper than both no spec and heavy specs. |

Evidence that tests help the model itself, not just the humans: in "Test-Driven Development for Code Generation" (arXiv 2402.13521), giving the model tests alongside the prompt raised solve rates by about 9 to 13 points for GPT-4 Turbo and 13 to 30 points for Llama 3 70B on HumanEval and MBPP, with more gained from a fix-and-retry loop.

## The proposed loop

One slice goes through these five steps, usually inside a day. The human touches it at three points: the examples, the test list, and the PR.

1. **Slice** (human + agent). The agent interviews you on the epic and proposes thin vertical slices with examples. You trim.
2. **Test list** (checkpoint). The agent reads the code and lists test names for one slice. You approve in under five minutes.
3. **Red.** The agent writes the next test and shows it fail for the right reason.
4. **Green.** Minimum code, full suite, commit. Repeat until the list is done.
5. **Refactor and ship.** Structural commits, fresh-context review, small PR, merge.

### What a slice looks like

Here is a made-up "discount codes at checkout" epic. Under a spec-driven chain it would produce a spec, a plan and stories before any code. Under this loop the first slice is just this issue:

```
Slice 1 of ~5: Apply a fixed-percentage code to the cart total
Why: first end-to-end path; real users can redeem a code.
Not in this slice: expiry, per-user limits, stacking.

Given a cart of 100.00 and code SAVE10 (10%)
When the code is applied
Then the total is 90.00

Given an unknown code
When it is applied
Then the total is unchanged and the error is "Code not recognised"

Given a cart that already has a code
When a second code is applied
Then the first code is replaced
```

The test list the agent proposes, and that you approve, is the spec a future developer will read:

```
describe "Applying a discount code"
  it reduces the total by the code's percentage
  it rounds to the cent, half up
  it rejects an unknown code without changing the total
  it replaces an existing code instead of stacking
  it is case-insensitive about the code
```

Six months later, `npm test -- --verbose` or `pytest --collect-only -q` prints this list. It is always current, because the build fails when it is wrong.

## Spec-driven chain against Slice, Test, Ship

| | Spec-driven chain | Slice, Test, Ship |
|---|---|---|
| Unit of work | Epic, then stories | Thin vertical slice, 0.5 to 2 days, mergeable on its own |
| Written before code | Spec, plan, story files for the whole epic | One issue with examples, for the next slice only |
| What a developer reads | Hundreds of lines of Markdown, then the code | About 20 lines of examples, a test list, a small diff |
| Source of truth | Spec files that drift | Tests that fail when wrong |
| First running code | After all planning is approved | Within hours of starting the epic |
| When the plan is wrong | Rewrite spec and plan, regenerate | Change the next slice's examples |
| Where planning lives | `plan.md`, committed | Plan mode in the session, thrown away after the slice |
| Long-lived prose | Every spec and plan | Short ADRs, only for decisions that are hard to reverse |

**What to keep from SDD.** The clarifying interview at the start of an epic is valuable and cheap; it moves into `/sts-slice`. Explicit non-goals stay too, as one line per slice. What goes is turning that conversation into documents that someone must then read.

## The skill set

The full skills are in [`skills/`](../skills). The two optional ones are outlined here and not yet written.

### `/sts-slice` (core)

Replaces a "specify" skill and an "epic to stories" skill.

- Interview the developer about the epic: users, the riskiest unknown, non-goals. Ask only what the code cannot answer.
- Propose 3 to 8 vertical slices. Each one goes through every layer it needs, can ship alone, and fits in two days.
- Order slices so the first is the thinnest end-to-end path and risky unknowns come early. A risky unknown can be a time-boxed spike slice.
- For each slice write one tracker issue: a title, one "why" line, one "not in this slice" line, and 3 to 7 Given / When / Then examples. Nothing else.
- Create the issues in the tracker. Do not write files to the repo.
- Detail only the first two slices. Later slices stay as one-line titles until they are next, because what you learn will change them.

### `/sts-tdd <issue>` (core)

Replaces a "plan" skill and an "implement" skill.

- Read the issue and the code it touches. Propose a test list: names only, in behaviour language, ordered simplest first. **Stop and wait for approval.**
- For each test: write it, run it, show it failing for the expected reason. Then write the least code that passes, run the full suite, commit.
- Never edit, skip or delete an approved test to get to green. If a test looks wrong, stop and say so.
- Refactor only on green, in separate commits marked `refactor:`. Structural changes go before behavioural ones.
- Do not add behaviour that no test asks for. If it seems needed, add it to the test list and ask.
- Finish by opening a small PR whose description is the examples and the test list, plus anything the reviewer must decide.

### `/sts-review-slice` (optional)

Runs in a fresh subagent so it is not biased by the session that wrote the code.

- Check that every example in the issue has a test, and that each test asserts the behaviour rather than the implementation.
- Flag tests that mock the thing under test, assertions that can never fail, and code that no test exercises.
- Report only gaps that affect correctness or the examples. Style preferences are out of scope.

### `/sts-decide` (optional)

The only skill that writes Markdown to the repo.

- Use it only for decisions that are expensive to reverse: a data model, a public API, a new dependency, a cross-team contract.
- Write `docs/decisions/NNN-title.md` with context, decision and consequences. Keep it under 30 lines.
- Commit it in the same PR as the code it explains.

### Which path to take, by size of change

| Change | Path |
|---|---|
| Diff fits in one sentence | Ask the agent directly, with "write a failing test that reproduces it, then fix it." No skill. |
| A known story, under two days | `/sts-tdd <issue>` straight away. |
| An epic | `/sts-slice` once, then `/sts-tdd` per slice. Re-run `/sts-slice` on the remainder whenever a slice teaches you something. |
| An epic with a big unknown | `/sts-slice` with a time-boxed spike as slice 1. Throw the spike code away; keep what you learned as examples. |

## Where agent TDD goes wrong, and the guard for each

| Failure | What it looks like | Guard |
|---|---|---|
| Tests that mirror the code | Tests written after the code, asserting what it happens to do | Test list approved before code; red step must show the failure |
| Cheating to green | Weakened assertions, skipped or deleted tests | Rule in `/sts-tdd`; a PreToolUse hook can block edits to approved test files during green |
| Scope creep | "While I was here" features with no test | No behaviour without a test; reviewer flags untested code |
| Mock-heavy tests | Every collaborator mocked; tests pass while the feature is broken | Prefer real collaborators; at least one test per slice through the public entry point |
| Slices that are layers | "Slice 1: database, slice 2: API" | `/sts-slice` rule that every slice is user-visible and shippable alone |
| Long context, lost rules | Agent forgets the loop late in a big session | One slice per session; Stop hook that runs the suite before the turn ends |

Kent Beck names the same three warning signs to watch for when pairing with an agent: loops, functionality nobody asked for, and cheating such as disabling or deleting tests.

## How to try it

1. Pick one upcoming epic of medium size and run it through `/sts-slice` and `/sts-tdd` while another team, or a past epic, serves as the spec-driven baseline.
2. Track four numbers per PR: time from pick-up to merge, PR size in lines, review time, and lines of Markdown a developer had to read before starting.
3. After two weeks, ask the developers one question: "Where did you go to understand what this code should do?" If the answer is the tests, it is working.
4. Keep a spec-driven skill available for work that truly needs a written contract up front, such as a public API or a regulated feature. It becomes the exception, not the default.

## Sources

1. [Böckeler, "Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl"](https://www.martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html), martinfowler.com
2. [Thoughtworks Technology Radar, "Spec-driven development"](https://www.thoughtworks.com/radar/techniques/spec-driven-development)
3. [Anthropic, "Best practices for Claude Code"](https://code.claude.com/docs/en/best-practices.md)
4. [Kent Beck, "Augmented Coding: Beyond the Vibes"](https://newsletter.kentbeck.com/p/augmented-coding-beyond-the-vibes)
5. [DataCamp, "Claude Code Superpowers" (overview of the obra/superpowers skills)](https://www.datacamp.com/tutorial/claude-code-superpowers)
6. [O'Reilly Radar, "Why AI Coding Agents Still Need Clear Specs"](https://oreillyradar.substack.com/p/why-ai-coding-agents-still-need-clear)
7. [Mathews & Nagappan, "Test-Driven Development for Code Generation", arXiv 2402.13521](https://arxiv.org/html/2402.13521v2)
8. [Google Cloud, "Introducing DORA's inaugural AI Capabilities Model"](https://cloud.google.com/blog/products/ai-machine-learning/introducing-doras-inaugural-ai-capabilities-model)
