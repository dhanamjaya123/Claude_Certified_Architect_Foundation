# Claude Code Configuration — Foundation Exam Study Guide

> **Basis:** Prepared from the supplied 246-page PDF, *D3 - Claude Code Configuration (Updated)*. The PDF is a slide deck, so this guide consolidates its repeated lesson summaries into one beginner-friendly reference.
>
> **Important:** No study guide can guarantee 100% marks. This document is designed to maximize your preparation from the supplied PDF. Product behavior can change, so for a live certification also compare version-sensitive details with the current official documentation.

## How to Use This Guide If You Have Zero Knowledge

1. Read the **Core Mental Model** and **Must-Memorize Facts** first.
2. Study one topic section at a time.
3. Answer every scenario without looking at the answer.
4. For each wrong answer, write one sentence explaining why the correct mechanism fits.
5. On the final day, revise the **Last-Minute Cram Sheet** and retake the questions.

---

## 1. Core Mental Model

Claude Code is an **agentic command-line interface (CLI)**. It does not merely suggest code like autocomplete. It can inspect files, plan work, call tools, edit files, run commands, observe results, and repeat until a task is complete.

Its basic loop is:

1. **Plan** — decide the next step.
2. **Act** — call a tool.
3. **Observe** — inspect the tool result.
4. **Repeat** — continue or finish.

The three foundational ideas are:

- **Claude Code:** the terminal-based agent.
- **Planning loop:** plan → act/tool call → observe → repeat.
- **Permission model:** the control layer that decides whether a tool action is permitted, denied, or requires approval.

### Claude Code versus other approaches

| Technology | Main behavior | Best description |
|---|---|---|
| IDE autocomplete/plugin | Suggests code while a developer types | Assistance inside the editor |
| Direct Claude API call | Prompt in, response out | A model call; orchestration is your responsibility |
| Claude Code CLI | Performs multi-step work using tools | Human drives an agent from a terminal |
| Claude Agent SDK | Runs Claude Code sessions from an application | Code drives the agentic workflow |

---

## 2. Tools and Tool Selection

Claude selects a tool according to the current task step.

- **Read:** inspect a file without changing it.
- **Write:** create a file or replace its complete contents.
- **Edit:** make a targeted change to an existing file.
- **Bash:** execute shell commands. It is powerful and therefore potentially dangerous.
- **Web search:** obtain current information beyond the model's training knowledge.
- **Agent:** delegate a bounded task to a subagent with a separate context window.

### High-value distinctions

- Use **Read** for safe inspection.
- Use **Edit** for a precise change; use **Write** for new or complete-file content.
- Use **Bash** when the operating system or project tooling must do something.
- Use web search when the answer depends on current external information.
- Use a subagent when isolation or parallel/delegated work provides real value.

---

## 3. Project Setup and `/init`

Claude Code discovers a project by reading its files and structure. Running `/init` generates a starting `CLAUDE.md` containing persistent project guidance.

The generated file is a **draft**, not unquestionable truth. Review it for:

- correct build, test, lint, and formatting commands;
- real repository structure and architecture;
- project-specific conventions;
- security and prohibited-action rules;
- obsolete, invented, or excessively verbose instructions.

Before first use, confirm the working directory, inspect repository status, identify secrets or sensitive paths, define permissions, and review the generated configuration.

---

## 4. Context, Sessions, and Compaction

Everything loaded into a session competes for the finite **context window**: conversation, file contents, instructions, command output, and tool results.

- A new session does not automatically contain the entire previous conversation.
- **Compaction** summarizes older material to reclaim context space.
- Add information that changes how Claude should perform the task.
- Exclude generated output, irrelevant logs, dependencies, large artifacts, and secrets.
- Keep standing instructions concise because `CLAUDE.md` content also consumes context.

`CLAUDE.md` is user-authored standing guidance loaded into sessions. The PDF distinguishes it from an automatic memory system that can retain evolving notes.

---

## 5. Permission Modes

| Mode | Behavior | Best use |
|---|---|---|
| `default` | Reads freely; edits and commands normally prompt | New, sensitive, or closely supervised work |
| `acceptEdits` | Automatically accepts file edits and common file operations within the working directory | Fast coding while reviewing changes afterward |
| `plan` | Read-only exploration and planning; no source edits | Understanding risky or cross-file work before implementation |
| `dontAsk` | Automatically denies anything not explicitly pre-approved | Locked-down, non-interactive automation |
| `bypassPermissions` | Skips permission prompts | Only an isolated container/VM where damage is contained |
| Auto mode | Reduces routine prompts while a safety classifier reviews actions | Long tasks requiring fewer interruptions, but not a substitute for review |

Key details from the PDF:

- Switch everyday modes with **Shift+Tab**, a startup permission-mode flag, or `defaultMode` in settings.
- Auto-approval applies within the working directory and added directories.
- Sensitive paths such as `.git` and `.claude` remain protected outside bypass mode.
- Asking Claude in ordinary chat to switch modes is not the control mechanism.
- Review automatically accepted changes with tools such as `git diff`.

---

## 6. `CLAUDE.md` Scope and Hierarchy

| Scope | Typical location | Purpose |
|---|---|---|
| Managed policy | Organization/system-managed location | Mandatory organization-wide security and policy baseline |
| User | `~/.claude/CLAUDE.md` | Personal preferences across projects |
| Project | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Shared repository rules; commit to version control |
| Local | `CLAUDE.local.md` | Personal, project-specific notes; normally gitignored |

The PDF presents the loading sequence as managed → user → project → local. Applicable instructions are added to context rather than behaving like a simple replacement chain.

### Critical conflict rule

Do **not** depend on a lower file reliably overriding an earlier one. Genuine contradictory natural-language instructions may resolve unpredictably. Design layers to complement one another.

- Put company requirements in managed policy.
- Put personal tone/output preferences at user scope.
- Put team coding and testing rules at project scope.
- Put private machine-specific notes in `CLAUDE.local.md`.

Use `/memory` to inspect loaded memory files and `/context` to inspect active context.

---

## 7. Directory-Scoped Instructions

A `CLAUDE.md` inside a subdirectory is lazy-loaded when Claude reads a file in that directory. No registration is required.

- Subdirectory rules **augment**, not erase, parent rules.
- Root rules remain active.
- Avoid direct contradictions between root and child files.
- Example scopes: `/frontend` for React conventions, `/tests` for test patterns, `/infrastructure` for never-delete rules, and `/generated` for “do not edit generated files.”
- Test the behavior by working in each directory and checking that both parent and scoped rules apply.

---

## 8. File Exclusion and Settings Precedence

The PDF states that the real file-exclusion mechanism is a **Read deny rule** in the `permissions` section of `settings.json`, not a `.claudeignore` file.

Conceptual examples:

```json
{
  "permissions": {
    "deny": [
      "Read(.env)",
      "Read(.env.*)",
      "Read(secrets/**)"
    ]
  }
}
```

Important rules:

- `*` matches one path segment; `**` crosses nested directories.
- Bare or `./` paths resolve from the working directory.
- According to the PDF, a leading `/` anchors to the settings file's directory and `//` denotes an absolute path.
- Ordinary settings use precedence managed → local → project → user.
- Permission rules merge across layers.
- **Deny wins over allow**, even when the rules come from different layers.
- Deny rules govern Claude Code's built-in access, but they are not a substitute for operating-system permissions, secret management, or sandboxing.

Use deny rules for `.env` files, private keys, cloud credentials, secret directories, and other material that should never enter context.

---

## 9. Writing Effective Instructions

Good `CLAUDE.md` directives are:

- **specific** — name the exact behavior;
- **actionable** — Claude can carry it out or test it;
- **unambiguous** — avoid vague words such as “properly” or “cleanly.”

Prefer headings and one concise rule per bullet. Encode repository-specific commands, conventions, architecture, processes, security constraints, and anti-patterns. Do not duplicate generic programming advice or every rule already enforced by a formatter/linter; reference the authoritative command instead.

Weak: “Write good tests.”

Strong: “For every API endpoint change, add or update pytest cases for success, authentication failure, and validation failure; run `pytest tests/api`.”

---

## 10. Custom Slash Commands

Custom slash commands are reusable Markdown instruction templates.

- Project commands live under `.claude/commands/` and are shared through version control.
- User commands are personal and apply across projects.
- The Markdown filename becomes the command name; no separate registration is needed.
- `$ARGUMENTS` inserts dynamic user input.
- Good commands do one focused job and use a clear verb–noun name such as `/review-api`.
- A command file should state the objective, required context/input, procedure or constraints, and expected output.
- Avoid one huge command that tries to perform many unrelated workflows.

---

## 11. Skills

A skill is a reusable instruction package stored in a named directory containing `SKILL.md`.

- The folder name identifies the skill.
- The `SKILL.md` body always loads when the skill is invoked.
- Frontmatter is optional.
- The **description** is particularly important because it helps Claude decide when the skill matches a task.
- Use a skill for a recurring, specialized workflow that needs more structure or supporting resources than a short command.
- Do not create a skill for a one-off action or for an always-on project rule that belongs in `CLAUDE.md`.
- The PDF warns against invented metadata fields such as `trigger`, `version`, or `author-tags`.

Instruction-stack shortcut:

- Always-on repository behavior → `CLAUDE.md`
- User-invoked reusable prompt → slash command/skill
- Isolated delegated worker → subagent

---

## 12. Subagents and Delegation

A subagent is a separate agent instance with its own context window.

- Its context is isolated; the parent's conversation and prior work do not automatically transfer.
- Pass the goal, relevant files/facts, constraints, tools/permissions, and expected output explicitly.
- Subagents inherit the parent's tool and permission context by default, but their access can be restricted further.
- Decide three things before delegating: what task, what context, and what output format.
- Use a subagent for bounded research, independent analysis, or work that benefits from isolation.
- Stay inline for tiny, tightly coupled tasks.
- Too many subagents add coordination overhead and fragmented results.
- Require structured output and explicit error reporting; an empty result must not silently look like success.

---

## 13. Plan Mode

Plan mode is a **permission mode**, not a separate execution tool. It permits read-only investigation and proposal of changes.

A good plan contains:

1. files/components affected;
2. ordered implementation steps;
3. validation, risk, and rollback considerations.

Use it for unfamiliar codebases, risky changes, architecture decisions, or multi-file work. Skip it for trivial, obvious edits where planning overhead adds no value.

The workflow is propose → review → edit/re-plan or approve → execute. Approval is the control gate. Read the plan before approving it, and re-plan if discoveries invalidate assumptions during execution.

---

## 14. Hooks

Hooks are deterministic shell-level scripts registered in settings. They run at lifecycle events outside Claude's reasoning context.

| Hook | Timing | Correct use |
|---|---|---|
| `PreToolUse` | Before a tool call | Inspect or block a dangerous action; log attempted actions |
| `PostToolUse` | After a tool call | Format files, run tests, log results, trigger side effects |
| `Stop` | End of an agent turn | Final validation, cleanup, summary, or completion notification |
| `Notification` | When Claude emits an alert | Route alerts to Slack, email, PagerDuty, or a webhook |

### Exit codes

- Exit `0`: continue.
- Exit `2`: block the tool call and return stderr to Claude.
- Other nonzero codes, including `1`: logged error but **do not block** according to the PDF.

### Implementation rules

- Register hooks in settings, not in `CLAUDE.md` or a prompt.
- Tool-call context arrives as JSON on stdin.
- Use an inline command only for a simple one-liner; use a tested, version-controlled script for real logic.
- Multiple hooks for one event may run in parallel, so do not rely on their order.
- Keep hooks fast; slow hooks stall calls until completion or timeout.
- Test all exit paths with sample inputs and start in dry-run mode when possible.
- A `PostToolUse` hook cannot undo an action. Prevention requires `PreToolUse`.

Common patterns: format after Edit, run focused tests after relevant changes, block destructive Bash, log attempts/results, notify after a long run, and summarize usage/cost.

---

## 15. Claude Agent SDK

The Claude Agent SDK (formerly called the Claude Code SDK in the PDF) provides programmatic control of Claude Code sessions.

- CLI: a human drives the session from a terminal.
- SDK: application code creates/configures sessions, injects prompts/context/permissions, and handles output.
- Direct API: individual model calls unless you build the agent loop and tool orchestration yourself.
- SDK sessions can stream tool events, output chunks, and errors for real-time monitoring.
- Good SDK uses include review bots, documentation generators, CI/CD integrations, and custom internal tooling.

Choose the SDK when code must drive a multi-step agentic workflow. Choose a direct API call for a lightweight prompt/response or when building your own orchestration.

---

## 16. CI/CD and Non-Interactive Use

The PDF's central CI/CD rule is: use `claude -p` (**print mode**) for a non-interactive, single-turn run that writes output and exits.

- Without `-p`, Claude Code enters interactive mode and a headless pipeline can wait indefinitely.
- Request machine-readable output with `--output-format json`; use streaming JSON when live events are needed.
- Restrict tools with `--allowedTools` (spelling/casing may be version-sensitive).
- Use atomic, standalone, idempotent tasks.
- Capture stdout, validate structured output, handle nonzero exit codes, and set a timeout.
- Give automation the minimum permissions and tools required.
- Store API keys in the CI secret store, never in the workflow file.
- For GitHub Actions, the PDF recommends the official `claude-code-action` setup via `/install-github-app`, rather than treating a raw `-p` command as the dedicated GitHub integration.

---

# Scenario-Based Practice Questions

## Questions 1–10: Foundations, Tools, and Context

### 1. A developer expects Claude Code to suggest the next few characters while typing. What misconception do they have?

**Answer:** They are treating Claude Code as IDE autocomplete.

**Explanation:** Claude Code is an agentic CLI. It executes multi-step tasks by planning, calling tools, observing results, and continuing; autocomplete primarily proposes inline completions.

### 2. Claude reads a failing test, edits source, runs the test, sees another error, and edits again. Which concept is demonstrated?

**Answer:** The planning/agentic loop.

**Explanation:** The cycle is plan → act → observe → repeat. The second edit occurs because the tool result becomes a new observation.

### 3. You need to inspect `package.json` without modifying anything. Which tool best fits?

**Answer:** Read.

**Explanation:** Read is the safe inspection tool. Write or Edit would introduce unnecessary mutation risk.

### 4. A new file must be created with its complete contents. Should Claude use Edit or Write?

**Answer:** Write.

**Explanation:** Write fits creation or whole-file replacement; Edit fits a targeted change to existing content.

### 5. The task asks for today's framework release status. Which capability is appropriate?

**Answer:** Web search.

**Explanation:** Current information may be newer than training knowledge, so external search is the correct source.

### 6. Why can a very long `CLAUDE.md` reduce performance on the actual task?

**Answer:** It consumes the finite context window.

**Explanation:** Instructions compete with conversation, files, and tool output. Concise, relevant standing context leaves more room for work.

### 7. A session is near its context limit. What mechanism allows it to continue?

**Answer:** Compaction.

**Explanation:** Compaction summarizes older context to reclaim space, although fine detail may be lost.

### 8. A developer starts a new session and assumes every previous message is automatically present. Is that safe?

**Answer:** No.

**Explanation:** Session continuity is not the same as carrying the full prior conversation. Persist durable instructions in the appropriate configuration and re-supply task context when needed.

### 9. `/init` produces a `CLAUDE.md`. What must happen next?

**Answer:** A human should review and refine it.

**Explanation:** Generated onboarding output is a starting point. Commands, architecture, and constraints must be verified against the repository.

### 10. A task is small but independent and would benefit from isolated context. What mechanism may help?

**Answer:** A subagent.

**Explanation:** A subagent receives a separate context window, but it needs an explicit handoff and expected output.

## Questions 11–20: Permissions and Configuration Scope

### 11. You are examining an unfamiliar production repository and want no edits. Which mode is best?

**Answer:** Plan mode.

**Explanation:** It supports read-only exploration and planning before any implementation is approved.

### 12. You are actively pairing with Claude and want edits to proceed while you review `git diff`. Which mode fits?

**Answer:** `acceptEdits`.

**Explanation:** It removes repetitive edit prompts within the working directory while preserving post-change review.

### 13. A locked-down pipeline must deny every tool not explicitly approved. Which mode fits the PDF?

**Answer:** `dontAsk`.

**Explanation:** It auto-denies unapproved actions and avoids waiting for a human response.

### 14. When is `bypassPermissions` appropriate?

**Answer:** Only in a strongly isolated container or VM with contained damage potential.

**Explanation:** It skips prompts entirely, so using it on a normal workstation or sensitive repository is unsafe.

### 15. A user types “switch to plan mode” as an ordinary prompt. Is that the proper mode control?

**Answer:** No.

**Explanation:** The PDF specifies controls such as Shift+Tab, a startup flag, or the `defaultMode` setting.

### 16. The company requires a non-optional security rule for all repositories. Where should it live?

**Answer:** Managed policy.

**Explanation:** Managed policy is organization-wide, loads broadly, and cannot be excluded locally.

### 17. One developer prefers concise responses in every project. Where should that preference live?

**Answer:** User-scope `~/.claude/CLAUDE.md`.

**Explanation:** It is personal and cross-project, not a team repository standard.

### 18. Every contributor must run a particular test command before completing work. Where should the rule live?

**Answer:** Project-level `CLAUDE.md`, committed to version control.

**Explanation:** Shared team behavior belongs in the repository so all contributors receive it.

### 19. A developer needs private notes for one repository only. Where should they go?

**Answer:** Gitignored `CLAUDE.local.md`.

**Explanation:** Local scope is narrow to one user and one project and should not become a shared standard.

### 20. User configuration says “use tabs,” while project configuration says “use spaces.” Can you depend on project scope winning?

**Answer:** No.

**Explanation:** The files are added to context, and genuine natural-language conflicts can resolve unpredictably. Remove the contradiction and keep coding standards at project scope.

## Questions 21–28: Directory Scope, Exclusion, and Instructions

### 21. React conventions should apply only under `/frontend`. What is the cleanest design?

**Answer:** Put a `CLAUDE.md` inside `/frontend`.

**Explanation:** It lazy-loads when Claude reads within that directory and keeps framework-specific rules out of the root file.

### 22. Does `/frontend/CLAUDE.md` replace the repository root instructions?

**Answer:** No; it augments them.

**Explanation:** Parent rules remain active and the subdirectory rules add narrower guidance.

### 23. Must a directory-scoped `CLAUDE.md` be registered in a manifest?

**Answer:** No.

**Explanation:** Its location defines its scope, and reading inside the directory triggers lazy loading.

### 24. You must prevent `.env` from entering Claude's context. What mechanism does the PDF recommend?

**Answer:** A `Read(.env)` deny rule in the permissions settings.

**Explanation:** The PDF explicitly rejects `.claudeignore` as the real security mechanism.

### 25. A user-level deny blocks `secrets/**`, while project settings allow it. What happens?

**Answer:** Access remains denied.

**Explanation:** Permission rules merge and deny is evaluated before allow across layers.

### 26. Is a Read deny rule a full replacement for OS access control and secret management?

**Answer:** No.

**Explanation:** It restricts Claude Code's built-in access but does not replace filesystem permissions, a vault, sandboxing, or credential hygiene.

### 27. Which instruction is more exam-worthy: “Write clean code” or “Run `npm test` after changing files under `src/`”?

**Answer:** The second.

**Explanation:** It is specific, actionable, testable, and repository-specific.

### 28. Should `CLAUDE.md` copy hundreds of formatter rules already enforced automatically?

**Answer:** No.

**Explanation:** Duplicating tool-enforced rules creates two sources of truth and wastes context. State the authoritative formatter command instead.

## Questions 29–36: Commands, Skills, Subagents, and Plans

### 29. A team wants a shared `/deploy-review` workflow. Where should its Markdown file live?

**Answer:** In the project's `.claude/commands/` directory and in version control.

**Explanation:** Project commands are distributed with the repository; the filename becomes the command.

### 30. A command must accept a pull-request number. What placeholder enables dynamic input?

**Answer:** `$ARGUMENTS`.

**Explanation:** It turns a static command template into one that incorporates invocation-specific input.

### 31. A workflow has supporting references and is repeatedly applicable based on task meaning. Command, skill, or `CLAUDE.md`?

**Answer:** Skill.

**Explanation:** A skill packages specialized reusable instructions and resources, while `CLAUDE.md` is always-on and a short command is a simpler manual shortcut.

### 32. What is the one required file in a skill directory?

**Answer:** `SKILL.md`.

**Explanation:** Its body contains the instructions. Frontmatter is optional according to the PDF.

### 33. Which skill metadata deserves especially careful wording?

**Answer:** `description`.

**Explanation:** It drives semantic matching and therefore whether Claude recognizes the skill as relevant.

### 34. A subagent returns irrelevant prose because it was told only “investigate.” What design error occurred?

**Answer:** The handoff lacked bounded scope, context, constraints, and an output format.

**Explanation:** A subagent starts with isolated context and knows only what is explicitly provided.

### 35. Should you spawn ten agents for ten tiny, tightly coupled edits?

**Answer:** Usually no.

**Explanation:** Coordination and merging overhead can exceed the benefit; stay inline for small interdependent work.

### 36. A plan lists edits but no tests or risk checks. Is it complete?

**Answer:** No.

**Explanation:** A good plan identifies affected components, ordered implementation steps, and validation/risk considerations.

## Questions 37–45: Hooks, SDK, and CI/CD

### 37. You must stop `rm -rf` before execution. Which hook is required?

**Answer:** `PreToolUse` on Bash.

**Explanation:** It runs before the command and can block. `PostToolUse` is too late.

### 38. You want to run Prettier after Claude edits a JavaScript file. Which hook fits?

**Answer:** `PostToolUse` on Write/Edit.

**Explanation:** Formatting reacts to the completed write and can operate on the saved file.

### 39. A hook exits with code 1. Does it block the tool under the PDF's convention?

**Answer:** No.

**Explanation:** Exit 2 is the block signal. Other nonzero codes are logged as non-blocking errors.

### 40. Where are hooks registered?

**Answer:** In Claude Code settings.

**Explanation:** Hooks are deterministic shell-level configuration, not natural-language instructions in `CLAUDE.md`.

### 41. A `PostToolUse` hook notices a destructive database command after it ran. Can the hook undo it?

**Answer:** No.

**Explanation:** Post hooks can react or report but cannot retroactively prevent side effects. Prevention needed `PreToolUse`.

### 42. A long task should send Slack a completion message. Which hook is appropriate?

**Answer:** A `Stop` hook.

**Explanation:** It runs at the end of the agent turn and is suited to cleanup, summaries, and completion notifications.

### 43. An application must run multi-step file edits and stream tool events. SDK or direct API?

**Answer:** Claude Agent SDK.

**Explanation:** The SDK manages agentic sessions and exposes streaming events; a direct API call is lower-level prompt/response unless you build the orchestration.

### 44. A CI job launches `claude "review this change"` and hangs. What is the likely fix?

**Answer:** Use non-interactive print mode: `claude -p "review this change"`.

**Explanation:** Without `-p`, the CLI expects interactive input that a headless runner cannot provide.

### 45. A pipeline grants every tool, embeds the API key in YAML, and ignores exit codes. Name three corrections.

**Answer:** Grant only required tools/permissions, store the API key in the CI secret store, and fail or handle the step based on nonzero exit codes.

**Explanation:** Least privilege limits impact, secret storage prevents credential exposure, and exit-code handling stops failures from appearing successful. Also validate structured output and add a timeout.

---

# Rapid-Fire True/False

1. **Claude Code is only an autocomplete system.** False.
2. **Compaction can reclaim context space.** True.
3. **`/init` output should be accepted without review.** False.
4. **Plan mode edits files while showing a plan.** False.
5. **`acceptEdits` removes the need to review diffs.** False.
6. **Managed policy is appropriate for organization-wide enforcement.** True.
7. **Project team rules belong only in one developer's user config.** False.
8. **Subdirectory instructions erase root instructions.** False.
9. **A directory `CLAUDE.md` requires registration.** False.
10. **Deny wins over allow in merged permission rules.** True.
11. **A deny rule replaces OS-level secret protection.** False.
12. **Custom command filenames determine command names.** True.
13. **Skill frontmatter is mandatory according to the PDF.** False.
14. **Subagents automatically receive the full parent conversation.** False.
15. **Plan mode is a permission mode.** True.
16. **`PostToolUse` can stop an already executed action.** False.
17. **Exit code 2 is the hook block signal in the PDF.** True.
18. **Multiple hooks for the same event should depend on execution order.** False.
19. **The SDK is suitable when application code drives an agentic session.** True.
20. **A normal interactive CLI invocation is ideal for headless CI.** False.

---

# Must-Memorize Decision Map

| Scenario clue | Best answer |
|---|---|
| “Explore without changing files” | Plan mode |
| “Auto-accept code edits while I review” | `acceptEdits` |
| “Deny anything not pre-approved” | `dontAsk` |
| “Completely isolated environment; no prompts” | `bypassPermissions` |
| “Company-wide mandatory rule” | Managed policy |
| “My preference in every repo” | User `CLAUDE.md` |
| “Shared team repository rule” | Project `CLAUDE.md`, version-controlled |
| “My private note for this repo” | Gitignored `CLAUDE.local.md` |
| “Only frontend rules” | `/frontend/CLAUDE.md` |
| “Keep a secret file unreadable” | `Read(...)` deny rule |
| “Allow and deny collide” | Deny wins |
| “Reusable manual shortcut” | Slash command |
| “Reusable instruction package with resources” | Skill |
| “Isolated delegated work” | Subagent |
| “Prevent a tool action” | `PreToolUse`, exit 2 |
| “React after an edit” | `PostToolUse` |
| “End-of-turn cleanup/completion” | `Stop` |
| “Route an alert” | `Notification` |
| “Application drives multi-step agent work” | Claude Agent SDK |
| “Headless CI invocation” | `claude -p` |

---

# Common Exam Traps

- **Trap:** Treating `CLAUDE.md` as an auto-updating memory database.  
  **Correction:** It is user-authored standing context.
- **Trap:** Assuming project instructions always override user instructions.  
  **Correction:** Avoid contradictory natural-language instructions; do not rely on deterministic overriding.
- **Trap:** Using `.claudeignore` as the security answer.  
  **Correction:** The PDF's answer is a Read deny rule in settings.
- **Trap:** Using `PostToolUse` to prevent damage.  
  **Correction:** Only a pre-action control can prevent execution.
- **Trap:** Returning exit 1 to block.  
  **Correction:** The PDF says exit 2 blocks.
- **Trap:** Assuming subagents know the parent conversation.  
  **Correction:** Provide an explicit handoff.
- **Trap:** Using `bypassPermissions` merely to save time.  
  **Correction:** Reserve it for a contained isolated environment.
- **Trap:** Starting an interactive CLI in CI.  
  **Correction:** Use `-p`, machine-readable output, timeouts, and exit-code checks.

---

# Last-Minute Cram Sheet

Memorize these statements:

1. Claude Code is an **agentic CLI**, not autocomplete.
2. The loop is **plan → act → observe → repeat**.
3. Context is finite; **compaction** summarizes older material.
4. `/init` creates a starting `CLAUDE.md`; **review it**.
5. `default` prompts, `acceptEdits` auto-accepts edits, and `plan` explores read-only.
6. `dontAsk` denies unapproved actions; bypass is for isolation only.
7. Scope is **managed, user, project, local**.
8. Shared standards belong in version-controlled project configuration.
9. Subdirectory `CLAUDE.md` files lazy-load and **augment** parents.
10. Exclude secrets with **Read deny rules**; **deny beats allow**.
11. Instructions should be specific, actionable, concise, and repository-specific.
12. Command filename = slash command; `$ARGUMENTS` adds dynamic input.
13. A skill is a directory with `SKILL.md`; description drives matching.
14. A subagent has isolated context; explicitly pass task, context, and output format.
15. Plan mode separates investigation from execution; approval is the gate.
16. Hooks run at shell level and are registered in settings.
17. `PreToolUse` prevents; `PostToolUse` reacts; `Stop` finishes; `Notification` alerts.
18. Hook exit `0` continues and exit `2` blocks.
19. The SDK is for code-driven agentic sessions; the direct API is for model calls/custom orchestration.
20. CI/CD uses `claude -p`, least privilege, structured output, exit-code checks, and timeouts.

## Final Self-Test Standard

You are exam-ready on this PDF when you can:

- score at least **41/45** on the scenarios without notes;
- explain every wrong option, not merely remember the right one;
- recreate the decision map from memory;
- correctly choose among `CLAUDE.md`, settings/deny rules, commands, skills, subagents, hooks, SDK, and `-p` for a new scenario.

---

# Diagram Explanations and Example Codebase Structure

This section converts the PDF's visual ideas into text diagrams and connects them to a realistic repository. Read each diagram from top to bottom in the direction of the arrows.

## Diagram 1: The Claude Code Agentic Loop

```text
               User gives a goal
                      |
                      v
              +---------------+
              | 1. PLAN       |
              | Choose a step |
              +-------+-------+
                      |
                      v
              +---------------+
              | 2. ACT        |
              | Call a tool   |
              +-------+-------+
                      |
                      v
              +---------------+
              | 3. OBSERVE    |
              | Read result   |
              +-------+-------+
                      |
                Task complete?
                 /          \
               no            yes
               |              |
               +---> PLAN     +---> Final result
```

### What it means

Claude Code does not have to solve a task in one response. Suppose the user says, “Fix the failing login test.” Claude may:

1. plan to inspect the failing test;
2. use Read to inspect it;
3. observe that the expected status is `401`;
4. inspect the login handler;
5. edit the handler;
6. run the test using Bash;
7. observe whether the test passed;
8. repeat if another failure appears.

**Exam clue:** Words such as “repeatedly,” “observes the result,” or “continues until complete” point to the **agentic/planning loop**.

## Diagram 2: Tools Inside the Loop

```text
                         +----------------+
                         |  Claude Code   |
                         +-------+--------+
                                 |
             +-------------------+-------------------+
             |          |          |         |       |
             v          v          v         v       v
           Read       Write       Edit      Bash    Web
        inspect a   create/full  targeted   execute  current
           file       replace     change    command  facts
                                 |
                                 v
                              Agent
                        delegate an isolated,
                           bounded subtask
```

### How to choose

- “Look at this file” → Read.
- “Create this new configuration” → Write.
- “Change only this function” → Edit.
- “Run tests/build/git” → Bash.
- “Find the latest release” → Web.
- “Investigate this independent area and report back” → Agent/subagent.

The permission system checks a tool call before it is allowed to execute.

## Diagram 3: Context Window

```text
+----------------------------------------------------------+
|                    CONTEXT WINDOW                        |
|----------------------------------------------------------|
| System and managed instructions                          |
| User, project, local, and directory CLAUDE.md files      |
| Current conversation                                     |
| Files Claude has read                                    |
| Shell output, test results, logs, and tool results       |
| Plans, edits, and other working information              |
+----------------------------------------------------------+
                 finite space: all items compete
                              |
                     when space becomes tight
                              v
                  +------------------------+
                  | COMPACTION             |
                  | Summarize older detail |
                  +------------------------+
```

### Why this matters

Loading a 500-line generic style guide leaves less space for source files and debugging output. Put only durable, behavior-changing instructions in `CLAUDE.md`; keep generated files, large logs, and unrelated documentation out of context.

## Diagram 4: Permission and Hook Gates

```text
Claude proposes a tool call
           |
           v
+-------------------------+
| Permission evaluation   |
| mode + allow/deny rules |
+------------+------------+
             |
      denied | permitted
       stop  | or approved
             v
+-------------------------+
| PreToolUse hook         |
| exit 0 = continue       |
| exit 2 = block          |
+------------+------------+
             |
             v
       TOOL EXECUTES
             |
             v
+-------------------------+
| PostToolUse hook        |
| react, format, test, log|
+-------------------------+
```

### What the diagram teaches

Permissions and hooks are related but different controls:

- A permission mode decides whether Claude may perform an action automatically, must ask, or must deny it.
- A deny rule blocks matching access.
- A `PreToolUse` hook performs a deterministic last check before execution.
- A `PostToolUse` hook runs after the side effect and therefore cannot prevent it.

**Exam trap:** If damage must be prevented, the answer cannot be `PostToolUse`.

## Diagram 5: `CLAUDE.md` Scope

```text
Broadest
   |
   v
+-------------------------------+
| Managed policy                |  company-wide requirements
+-------------------------------+
| User CLAUDE.md                |  one person's cross-project preferences
+-------------------------------+
| Project CLAUDE.md             |  shared repository standards
+-------------------------------+
| CLAUDE.local.md               |  private notes for this user and repo
+-------------------------------+
   |
   v
Narrowest

When Claude reads src/frontend/Button.tsx:

Project CLAUDE.md
        +
src/frontend/CLAUDE.md
        =
global project rules plus frontend-specific rules
```

### The important detail

This is an **additive instruction hierarchy**, not a guaranteed “last file wins” system. Avoid contradictions. A child `CLAUDE.md` adds focused guidance when Claude reads within that directory.

## Diagram 6: Commands, Skills, and Subagents

```text
Need instructions for Claude
            |
            v
Does the rule apply throughout ordinary project work?
       / yes                         \ no
      v                               v
 CLAUDE.md                  Is it a reusable workflow?
                                  / yes       \ no
                                 v             v
                        Command or Skill    ordinary prompt
                              |
            +-----------------+------------------+
            |                                    |
    short/manual template              richer instruction package
      slash command                           skill

Need isolated delegated work with its own context?
                         |
                        yes
                         v
                     subagent
```

### Quick interpretation

- `CLAUDE.md`: always-on standards.
- Command: reusable user-invoked prompt, such as `/review-api 184`.
- Skill: reusable packaged expertise with instructions and possibly supporting files.
- Subagent: a separate worker that needs an explicit task, context, constraints, and output contract.

## Diagram 7: Plan Mode Approval Flow

```text
Complex or risky request
          |
          v
    Enter plan mode
          |
          v
 Read files and investigate
          |
          v
     Propose a plan
          |
          v
 User reviews files, steps,
 tests, risks, and assumptions
       /              \
 revise                approve
   |                      |
   +--> improve plan      v
                    execution mode
                           |
                           v
                    edit and validate
```

Approval is not a formality. It is the control point between read-only investigation and mutation.

## Diagram 8: SDK and CI/CD

```text
Human at terminal                    Application or pipeline
       |                                      |
       v                                      v
Claude Code CLI                       Agent SDK / claude -p
       |                                      |
       +---------------+----------------------+
                       v
               agentic execution loop
                       |
                       v
              tool events and output

CI/CD safe path:

secret store --> API credential
pipeline     --> claude -p "atomic task"
permissions  --> minimum required tools
stdout       --> JSON validation/parsing
exit code    --> pass or fail pipeline
timeout      --> stop runaway execution
```

The CLI and SDK expose similar agentic power through different control surfaces. A human normally drives the interactive CLI; code drives the SDK or a non-interactive `-p` run.

---

## Complete Example Codebase Structure

The following fictional project shows where each configuration mechanism belongs:

```text
secure-shop/
|
+-- CLAUDE.md                         # Shared root project instructions
+-- CLAUDE.local.md                   # Personal repo notes; gitignored
+-- .gitignore
+-- package.json
+-- README.md
|
+-- .claude/
|   +-- settings.json                 # Shared permissions and hooks
|   +-- settings.local.json           # Personal settings; gitignored
|   |
|   +-- commands/
|   |   +-- review-api.md             # Invoked as /review-api
|   |   +-- run-feature-check.md      # Invoked as /run-feature-check
|   |
|   +-- skills/
|   |   +-- api-security-review/
|   |       +-- SKILL.md              # Reusable specialist instructions
|   |       +-- checklist.md          # Supporting skill reference
|   |
|   +-- agents/
|       +-- test-reviewer.md           # Example custom subagent definition
|
+-- hooks/
|   +-- block-destructive-command.js  # PreToolUse safety check
|   +-- format-after-edit.js           # PostToolUse automation
|
+-- src/
|   +-- backend/
|   |   +-- CLAUDE.md                  # Backend-only conventions
|   |   +-- routes/
|   |   |   +-- login.ts
|   |   +-- services/
|   |       +-- auth.ts
|   |
|   +-- frontend/
|       +-- CLAUDE.md                  # Frontend-only conventions
|       +-- components/
|           +-- LoginForm.tsx
|
+-- tests/
|   +-- CLAUDE.md                      # Test-only conventions
|   +-- auth.test.ts
|
+-- generated/
|   +-- CLAUDE.md                      # Do-not-edit warning
|   +-- api-client.ts
|
+-- secrets/                           # Denied to Claude Code
    +-- production-key.pem
```

### How Claude processes this structure

If Claude starts at `secure-shop/`, the root `CLAUDE.md` provides shared behavior. When it reads `src/frontend/components/LoginForm.tsx`, the frontend `CLAUDE.md` is added. When it later reads `tests/auth.test.ts`, the test-specific instructions are added. A Read deny rule should prevent access to `secrets/` regardless of a prompt asking Claude to inspect it.

---

## Example Root `CLAUDE.md`

```markdown
# Secure Shop Project Instructions

## Commands

- Install dependencies with `npm ci`.
- Run the complete test suite with `npm test`.
- Run linting with `npm run lint`.

## Architecture

- Keep HTTP routing in `src/backend/routes`.
- Keep business logic in `src/backend/services`.
- Do not import frontend modules from backend code.

## Required validation

- After changing TypeScript, run `npm run lint`.
- Run the smallest relevant test suite before the full suite.

## Security

- Never place credentials or tokens in source code.
- Never modify files under `generated/` manually.
```

Why this works: it contains precise repository commands, architecture boundaries, validation steps, and security constraints. It avoids generic instructions such as “write high-quality code.”

## Example Frontend-Scoped `CLAUDE.md`

Location: `src/frontend/CLAUDE.md`

```markdown
# Frontend Instructions

- Name React components in PascalCase.
- Name custom hooks with the `use` prefix.
- Co-locate each component test with its component.
- Use the shared design tokens; do not insert literal color values.
- Run `npm test -- frontend` after frontend changes.
```

These instructions apply in addition to the root project instructions when Claude reads frontend files.

## Example Backend-Scoped `CLAUDE.md`

Location: `src/backend/CLAUDE.md`

```markdown
# Backend Instructions

- Route handlers validate input and delegate business logic to services.
- Return the standard error object: `{ "error": { "code": "...", "message": "..." } }`.
- Add authentication-failure and validation-failure tests for every endpoint change.
- Never log passwords, tokens, session identifiers, or full request bodies.
```

## Example Generated-Code `CLAUDE.md`

Location: `generated/CLAUDE.md`

```markdown
# Generated Files

- Do not edit files in this directory.
- Regenerate them with `npm run generate-client`.
- Make required changes in the source schema, then run the generator.
```

This is an instruction-level safeguard. For stronger enforcement, also use permissions or a `PreToolUse` hook.

## Example Permission and Hook Settings

Location: `.claude/settings.json`

```json
{
  "permissions": {
    "allow": [
      "Read(src/**)",
      "Read(tests/**)",
      "Bash(npm test:*)",
      "Bash(npm run lint:*)"
    ],
    "deny": [
      "Read(.env)",
      "Read(.env.*)",
      "Read(secrets/**)",
      "Read(**/*.pem)"
    ]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "node hooks/block-destructive-command.js"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "node hooks/format-after-edit.js"
          }
        ]
      }
    ]
  }
}
```

This example is conceptual and based on the PDF's configuration model. Exact keys and matcher syntax should be checked against the installed Claude Code version.

### Reading the example

- The allow list permits source/test reads and selected npm commands.
- The deny list protects environment files, the secrets tree, and PEM keys.
- `PreToolUse` inspects Bash before execution.
- `PostToolUse` formats content after Edit or Write.
- A matching deny still beats an allow.

## Example Slash Command

Location: `.claude/commands/review-api.md`

```markdown
Review API endpoint: $ARGUMENTS

1. Identify its route handler and service implementation.
2. Check input validation, authentication, authorization, and error handling.
3. Check tests for success, authentication failure, authorization failure,
   and invalid input.
4. Do not modify files.
5. Return findings as a table with severity, file, issue, and recommendation.
```

Invoking `/review-api POST /login` substitutes `POST /login` for `$ARGUMENTS`.

## Example Skill

Location: `.claude/skills/api-security-review/SKILL.md`

```markdown
---
name: api-security-review
description: Review API endpoint changes for authentication, authorization,
  input validation, secret exposure, and unsafe logging.
---

# API Security Review

Use `checklist.md` as the review sequence.

For every finding, report:

- severity: critical, high, medium, or low;
- evidence: exact file and relevant behavior;
- impact: what an attacker or user could cause;
- remediation: the smallest safe correction;
- verification: a test that proves the correction.

Do not claim a vulnerability without code evidence.
```

The description clearly states when the skill is relevant. The body defines a reliable output contract, and the adjacent checklist provides supporting knowledge.

## Example Subagent Handoff

```text
Task:
Review only the authentication tests for missing cases.

Context:
- Implementation: src/backend/services/auth.ts
- Tests: tests/auth.test.ts
- Expected status for invalid credentials: 401

Constraints:
- Do not edit files.
- Do not inspect secrets/ or environment files.
- Report only evidence visible in the supplied files.

Output:
Return a Markdown table with columns:
Priority | Missing case | Evidence | Proposed test name

Failure behavior:
If either required file cannot be read, report BLOCKED and name the file.
```

This handoff succeeds because it defines task, context, constraints, output, and failure reporting. “Please review authentication” would be too vague.

## Example PreToolUse Hook Logic

The conceptual flow for `hooks/block-destructive-command.js` is:

```text
Read JSON tool context from stdin
             |
             v
Is the tool Bash?
       / no       \ yes
   exit 0          inspect command
                         |
            contains destructive pattern?
                   / no        \ yes
               exit 0           write reason to stderr
                                      |
                                    exit 2
```

Use an allowlist where practical because a blocklist can miss spelling, quoting, aliases, or new destructive commands. Test safe, unsafe, and malformed inputs before enabling enforcement.

## Example CI/CD Shape

```yaml
steps:
  - name: Claude review
    env:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
    run: >-
      claude -p
      --output-format json
      "Review the changed TypeScript files. Do not edit. Return valid JSON."
```

For a real pipeline, additionally set the minimum tool allowlist, validate the JSON schema, impose a timeout, and fail on a nonzero exit code. Exact CLI flags should be confirmed for the installed version.

---

## Codebase-Structure Scenario Questions

### 46. Claude reads `src/frontend/components/LoginForm.tsx`. Which project instructions should affect it?

**Answer:** The root project `CLAUDE.md` plus `src/frontend/CLAUDE.md`, along with applicable managed, user, and local context.

**Explanation:** Directory-scoped guidance augments the broader instructions when a file in that directory is read.

### 47. The backend team rule is accidentally placed in one developer's `CLAUDE.local.md`. What is the consequence?

**Answer:** Other contributors will not reliably receive the rule.

**Explanation:** Shared standards belong in a version-controlled project or directory-scoped `CLAUDE.md`.

### 48. The generated folder says “do not edit,” but the organization needs deterministic blocking. What should be added?

**Answer:** A suitable permission restriction or `PreToolUse` enforcement hook.

**Explanation:** Natural-language instructions guide behavior; deterministic controls enforce it before a tool action.

### 49. A security review is repeated, has a checklist, and should return the same schema every time. Which mechanism is best?

**Answer:** A skill.

**Explanation:** It is a recurring specialized workflow with supporting resources and a defined output contract.

### 50. The pipeline runs Claude successfully but downstream parsing fails intermittently. What configuration improvements help?

**Answer:** Request structured JSON, validate its schema, make the prompt atomic and explicit, check exit codes, and set a timeout.

**Explanation:** Automation must treat model output as data requiring validation, not assume that any stdout is structurally correct.
