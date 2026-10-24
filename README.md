# Slice, Test, Ship

A lightweight, test-driven workflow for AI coding agents, packaged as two Claude skills. It replaces a spec-driven chain (spec, then plan, then stories, then code) with thin vertical slices whose examples become tests, and whose tests become the documentation.

## The loop

1. **`/sts-slice`** turns an epic into 3 to 8 thin vertical slices. It writes full tracker issues, each with 3 to 7 Given / When / Then examples, for only the next two slices. It never writes spec or plan files.
2. **`/sts-tdd <issue>`** proposes a test list and stops until you approve it. Then it works one test at a time: shows the test failing, writes just enough code to pass, and commits. Refactors go in separate commits, approved tests are never weakened or deleted, and it finishes with a small PR described by its test list.

The human checks in at three points: the slice's examples, the test list, and the PR.

## Contents

| Path | What it is |
|---|---|
| [`skills/sts-slice/SKILL.md`](skills/sts-slice/SKILL.md) | Slice an epic into tracker issues with examples |
| [`skills/sts-tdd/SKILL.md`](skills/sts-tdd/SKILL.md) | Implement one slice test-first |
| [`docs/slice-test-ship.md`](docs/slice-test-ship.md) | The research report behind the workflow, with sources |

## Using the skills

Copy a skill folder into your Claude Code skills directory, either for one project (`.claude/skills/`) or for all your projects (`~/.claude/skills/`):

```sh
cp -r skills/sts-slice skills/sts-tdd ~/.claude/skills/
```

Then run `/sts-slice <epic>` in Claude Code, followed by `/sts-tdd <issue>` for each slice. Both skills use the `gh` CLI for GitHub issues when it is available, and fall back to printing issues as text.

## License

Copyright (c) 2026 Esteban Bellegarde. Licensed under [Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International](https://creativecommons.org/licenses/by-nc-nd/4.0/) (CC BY-NC-ND 4.0). See [LICENSE](LICENSE).

In short: you may read and share this work unchanged, with credit, for non-commercial purposes. You may not use it commercially or distribute modified versions without permission.

Note that this repository is public, so anyone can still read it, and GitHub lets anyone fork it. The license controls what people are allowed to do with the content, not whether they can see it.
