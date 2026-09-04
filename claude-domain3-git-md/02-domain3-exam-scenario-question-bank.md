# Domain 3 — Exam Scenario Question Bank

> Try each question before reading the answer.

## Q1 — Team-wide project instruction

The whole team wants Claude Code to run `./mvnw test` before completing changes.

Best place?

A. Personal `CLAUDE.local.md`  
B. Project `CLAUDE.md`  
C. One-time prompt every session  
D. Path-specific React rule

**Answer: B**

---

## Q2 — Personal project preference

You use a private local test URL different from teammates.

Best place?

A. Shared project CLAUDE.md  
B. Organization policy  
C. `CLAUDE.local.md`  
D. GitHub branch protection

**Answer: C**

---

## Q3 — User-wide preference

You want one personal coding preference across all repositories.

A. `~/.claude/CLAUDE.md`  
B. Every repository's test rule  
C. CI workflow only  
D. One path rule

**Answer: A**

---

## Q4 — API-only rule

“All files under `src/api/**` must use the standard error model.”

Best mechanism?

A. Global project instructions only  
B. Path-specific rule  
C. CI secret  
D. Worktree

**Answer: B**

---

## Q5 — Task-specific procedure

A 15-step release process is needed only during releases.

A. Put all 15 steps in global CLAUDE.md  
B. Create a release skill  
C. Put it in every source file  
D. Plan Mode always

**Answer: B**

---

## Q6 — Security enforcement

CLAUDE.md says, “Never run destructive database commands.” The company requires a guarantee.

Best additional control?

A. More natural-language emphasis  
B. PreToolUse hook / permissions / policy enforcement  
C. Larger context window  
D. More slash commands

**Answer: B**

---

## Q7 — Major refactor

User asks Claude to reorganize 60 files across several modules.

Best initial mode?

A. Direct execution immediately  
B. Plan Mode  
C. Skip code inspection  
D. CI only

**Answer: B**

---

## Q8 — Typo fix

Change `"recieved"` to `"received"` in one known file.

Best approach?

A. Full architecture planning  
B. Direct execution with verification  
C. Create five subagents  
D. New MCP server

**Answer: B**

---

## Q9 — Plan Mode meaning

Which statement is correct?

A. Claude edits first and explains later  
B. Claude can inspect and propose changes before disk edits are approved  
C. Claude cannot read files  
D. Plan Mode is CI only

**Answer: B**

---

## Q10 — Iterative refinement

Claude changes code, runs tests, fixes failures, reruns tests.

Pattern?

A. Static instruction only  
B. Iterative refinement  
C. Resource discovery  
D. One-shot generation

**Answer: B**

---

## Q11 — Huge CLAUDE.md

A project has an 800-line CLAUDE.md containing frontend, backend, release, support, and deployment procedures.

Best improvement?

A. Make it 1,500 lines  
B. Split always-needed instructions, path rules, and skills  
C. Remove all instructions  
D. Put everything in prompts

**Answer: B**

---

## Q12 — Conflicting instructions

Root instructions say “2 spaces.” A nested project file says “4 spaces” for the same files.

Risk?

A. No issue  
B. Conflicting context can reduce reliable adherence  
C. Claude automatically knows which company policy is correct  
D. CI is disabled

**Answer: B**

---

## Q13 — Rules folder

What is `.claude/rules/` useful for?

A. Model training  
B. Modular project instructions and path scoping  
C. API key storage  
D. Git object storage

**Answer: B**

---

## Q14 — Path rule trigger

A rule has:

```yaml
paths:
  - "src/**/*.tsx"
```

When is it relevant?

A. For all tasks regardless of files  
B. When Claude works with matching TSX files  
C. Only when GitHub Actions runs  
D. Never

**Answer: B**

---

## Q15 — Skill vs rule

“Always use constructor injection in Java services.”

Likely best:

A. Project/path-specific coding rule  
B. Release skill  
C. One-time prompt  
D. GitHub secret

**Answer: A**

---

## Q16 — Skill

“Perform our standard PR review checklist.”

Best:

A. Skill  
B. Personal local URL file  
C. Model parameter  
D. Glob only

**Answer: A**

---

## Q17 — CI trigger

Run review every time a PR is opened or updated.

A. GitHub/CI PR trigger  
B. User-level CLAUDE.md only  
C. Plan Mode only  
D. Local terminal only

**Answer: A**

---

## Q18 — Secrets

Where should CI credentials be kept?

A. Plaintext CLAUDE.md  
B. Git commit  
C. Secret store / repository secret mechanism  
D. PR description

**Answer: C**

---

## Q19 — Least privilege CI

Claude only needs to comment on a PR.

Best token permission?

A. Full organization admin  
B. Only permissions needed for the comment/workflow  
C. Root cloud credentials  
D. Production DB admin

**Answer: B**

---

## Q20 — Claude review and testing

A team wants to remove unit tests because Claude reviews every PR.

Correct?

A. Yes  
B. No; LLM review complements deterministic tests  
C. Only for Java  
D. Only in Plan Mode

**Answer: B**

---

## Q21 — Test failure

Claude generates code that compiles locally but one unit test fails.

Best next step?

A. Mark task done  
B. Diagnose failure, refine code, rerun tests  
C. Delete test  
D. Ignore CI

**Answer: B**

---

## Q22 — Requirement not met after green tests

All tests pass, but the implementation misses one acceptance criterion.

What next?

A. Finish because tests pass  
B. Refine implementation and verify requirement  
C. Remove criterion  
D. Plan Mode is impossible now

**Answer: B**

---

## Q23 — Large repository exploration

Researching auth requires reading many files and risks filling the main context.

Best option?

A. Read everything into main context  
B. Focused search/subagent exploration and return findings  
C. Delete CLAUDE.md  
D. Run write tools first

**Answer: B**

---

## Q24 — Worktree use

Two Claude sessions must implement unrelated features in parallel.

Best isolation?

A. Same files and branch  
B. Separate Git worktrees/branches  
C. Same uncommitted working tree  
D. One shared editor buffer

**Answer: B**

---

## Q25 — Non-interactive mode

When is it useful?

A. CI or repeatable scripted workflows  
B. Only for casual conversation  
C. Only for image generation  
D. Never

**Answer: A**

---

## Q26 — Always-loaded release checklist

Why is a long release checklist in CLAUDE.md suboptimal?

A. It consumes context even when not releasing  
B. Skills cannot contain steps  
C. CLAUDE.md cannot contain Markdown  
D. CI cannot read it

**Answer: A**

---

## Q27 — React-only coding convention

Frontend rule should not affect backend Java.

Best:

A. Global instruction  
B. `paths`-scoped rule for React/TSX  
C. GitHub API key  
D. Organization secret

**Answer: B**

---

## Q28 — Enforcement

“Every PR must pass SAST.”

Where should hard enforcement live?

A. Prompt only  
B. CI required check / branch protection  
C. User preference only  
D. Skill description

**Answer: B**

---

## Q29 — Project architecture

“Controllers must not contain business logic.”

Best place if it applies repository-wide?

A. Project instruction / relevant path rule  
B. CI token  
C. One personal prompt  
D. Worktree name

**Answer: A**

---

## Q30 — Plan Mode scenario

Which is most suited to Plan Mode?

A. Fix spelling in README  
B. Redesign authentication flow across services  
C. Change one variable name  
D. Add newline at EOF

**Answer: B**

---

# Harder Questions

## Q31 — Global vs skill

Your team has a 30-step database release playbook used twice a year.

Best architecture?

**Answer:** Keep only evergreen safety principles in persistent instructions; put the release procedure in a skill/workflow invoked for that task.

---

## Q32 — Hard block

The project instruction says not to edit `generated/`, but Claude occasionally does.

What change gives deterministic protection?

**Answer:** Use a permission/hook/tool-level policy to block edits to the path rather than relying only on context instructions.

---

## Q33 — Path-specific validation

Only REST controllers need OpenAPI comments.

Best configuration?

**Answer:** A path-specific rule matching controller files.

---

## Q34 — CI code modification

A GitHub Action lets Claude implement issue fixes and open PRs.

What safety design is strongest?

**Answer:** Minimal repository permissions, protected branches, tests/checks, secret isolation, and human review before merge.

---

## Q35 — Rule conflict

Two applicable rule files contradict one another.

What should the team do?

**Answer:** Resolve the conflict and establish one intentionally scoped source of truth; do not rely on Claude to infer which stale rule is correct.

---

## Q36 — Iterative bug fix

What sequence is strongest?

A. Edit → finish  
B. Reproduce → change → targeted test → broader tests → diff review  
C. Plan forever  
D. Create PR before testing

**Answer: B**

---

## Q37 — Plan approval

Plan Mode produces a plan that misses a critical dependency.

Best next step?

**Answer:** Revise the plan before execution; Plan Mode exists to surface these issues before edits.

---

## Q38 — Shared team convention

Where should a shared project rule live?

**Answer:** In source-controlled project configuration such as project `CLAUDE.md` or project rule files, not only a developer's local file.

---

## Q39 — Local credential

Should an API token be placed in `CLAUDE.local.md`?

**Answer:** No. Local instructions are still model context. Use a secret-management mechanism instead.

---

## Q40 — CI outcome

Claude reports “tests probably pass” without actually running them.

Is the task complete?

**Answer:** Not if test execution is part of the requirement. Verification should be based on actual tool/CI results.

---

# True / False Drill

1. `CLAUDE.md` is appropriate for persistent repository guidance. **True**  
2. `CLAUDE.md` is a deterministic security boundary. **False**  
3. Path-specific rules reduce irrelevant instruction loading. **True**  
4. A reusable task procedure is a good skill candidate. **True**  
5. Plan Mode is always mandatory. **False**  
6. Major ambiguous refactors benefit from planning first. **True**  
7. Iterative refinement should include verification. **True**  
8. CI credentials should be committed in the repository. **False**  
9. LLM code review replaces deterministic tests. **False**  
10. Separate worktrees can isolate parallel development. **True**
