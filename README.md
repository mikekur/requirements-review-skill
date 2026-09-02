# Requirements Review — Claude Skill

A [Claude Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) that performs a structured review of a requirement (requirement, user story, or spec item) **before** it goes into test analysis. It flags problems in the text of the requirement — it does not rewrite or "improve" the requirement itself.

The output is written for a Business Analyst / team to read: a verdict, a findings table, open questions for the BA, and what's already well-written — not a wall of criticism.

> **Note:** the skill's instructions and its output are in **Ukrainian** (the review report itself is generated in Ukrainian). This README is in English for publishing purposes.

## What it checks

The skill reviews a requirement against **7 quality criteria**:

1. **Atomicity** — does the requirement describe exactly one behavior/function (no "and", no bundled features)?
2. **Unambiguity** — no undefined qualitative words ("fast", "convenient", "correctly") without a measurable definition nearby.
3. **Completeness** — negative scenarios and edge cases are covered, not just the happy path.
4. **Consistency** — no internal contradictions, and no conflict with other provided requirements.
5. **Non-duplication** — the requirement doesn't duplicate another requirement in the provided set (only checked when more than one requirement is given).
6. **Testability** — every statement in the requirement can be turned into a pass/fail test case.
7. **Feasibility** — the requirement isn't technically self-contradictory or obviously unimplementable based on the text itself.

## Core rules

- Every finding must be backed by an **exact quote** from the requirement text — nothing is invented.
- Gaps in the text (missing negative scenario, undefined term, etc.) are phrased as **questions to the BA**, never as assumptions about what the author "probably meant".
- If multiple requirements are given, each is reviewed **separately**.
- If no requirement text is provided, the skill asks for it instead of fabricating an example.

## Severity levels

| Severity | Meaning |
|---|---|
| **Critical** | Blocks test analysis — no test case can be derived from the requirement as written. |
| **Major** | Risk of missed or conflicting test scenarios (e.g. missing negative scenario, ambiguity with multiple valid interpretations). |
| **Minor** | An imprecision that doesn't block analysis but could cause misreadings. |

## Verdict logic

| Verdict | Condition |
|---|---|
| ✅ Ready for test analysis | No findings, or Minor findings only |
| ⚠ Needs clarification | At least one Major finding, no Critical findings |
| ❌ Not ready | At least one Critical finding |

## Output format

```
## Verdict
<✅ Ready / ⚠ Needs clarification / ❌ Not ready>

<1-sentence justification>

## Findings

| # | Criterion | Quote | Problem | Severity | Suggested fix |
|---|-----------|-------|---------|----------|----------------|
| 1 | ... | "..." | ... | Critical/Major/Minor | ... |

## Questions for the BA
1. ...

## What's good
- ...
```

The "What's good" section is always included, even for a ❌ verdict — the skill looks for genuine strengths in the text rather than skipping this section.

## Installation

1. Copy this skill's folder (containing `SKILL.md`) into your Claude Skills directory:
   - Claude Code (personal skills): `~/.claude/skills/requirements-review/`
   - Project-scoped: `.claude/skills/requirements-review/` inside your repo
2. Make sure the folder contains `SKILL.md` directly (no nested subfolder).
3. Restart/reload Claude if needed so it picks up the new skill.

## Usage

Just ask Claude to review a requirement, e.g.:

> Review this requirement: "The system should quickly and correctly process user requests and save them to the database."

The skill triggers automatically on requests like "review this requirement", "is this user story ready for test analysis", etc. — you don't need to name the skill explicitly.
