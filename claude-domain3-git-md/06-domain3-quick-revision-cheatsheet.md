# Domain 3 — Quick Revision Cheat Sheet

## One-line definitions

- **CLAUDE.md:** Persistent instructions shared as Claude Code context.
- **CLAUDE.local.md:** Personal repository-specific instructions.
- **`.claude/rules/`:** Modular instruction files.
- **Path-specific rule:** Rule applied only to matching files/paths.
- **Skill:** Reusable task-specific workflow/instruction package.
- **Plan Mode:** Inspect and propose before editing.
- **Direct execution:** Perform a clear task without a separate plan approval phase.
- **Iterative refinement:** Edit → test → diagnose → refine until requirements pass.
- **CI/CD workflow:** Non-interactive/repository automation using controlled triggers and permissions.
- **Worktree:** Isolated checkout/branch useful for parallel sessions.

---

## Fast configuration choice

| Requirement | Use |
|---|---|
| Always relevant to project | `CLAUDE.md` |
| Personal project preference | `CLAUDE.local.md` |
| Personal across repositories | `~/.claude/CLAUDE.md` |
| Only certain paths | `.claude/rules/` + `paths` |
| Reusable procedure | Skill |
| One-time instruction | Prompt |
| Must be technically enforced | Hook / permission / CI |

---

## Plan vs direct

| Scenario | Best |
|---|---|
| Major refactor | Plan Mode |
| Unknown architecture | Plan Mode |
| Database migration | Plan Mode |
| Security-sensitive multi-file change | Plan Mode |
| One typo | Direct |
| One clear local edit | Direct |
| Straightforward unit test | Direct |

---

## Exam keywords

| Wording | Think |
|---|---|
| “Every session” | CLAUDE.md |
| “Only frontend files” | Path-specific rule |
| “Reusable checklist” | Skill |
| “Review before changing files” | Plan Mode |
| “Small obvious change” | Direct execution |
| “test, fix, rerun” | Iterative refinement |
| “every PR” | CI trigger |
| “must block” | Hook/policy |
| “parallel isolated branches” | Worktrees |
| “CI secret” | Secret store |

---

## Ten traps

1. Put everything in CLAUDE.md — wrong.
2. Treat CLAUDE.md as security enforcement — wrong.
3. Use Plan Mode for every small task — wrong.
4. Direct-edit a huge ambiguous refactor — risky.
5. Store secrets in instructions — wrong.
6. Give CI admin permissions unnecessarily — wrong.
7. Let Claude review replace tests — wrong.
8. Keep conflicting rules — wrong.
9. Load task-specific procedures every session — inefficient.
10. Mark code complete without actual validation — wrong.

---

## Memory formula

**C-S-P-E-T-R-CI**

- **C**onfigure
- **S**cope
- **P**lan
- **E**xecute
- **T**est
- **R**efine
- **CI** automate
