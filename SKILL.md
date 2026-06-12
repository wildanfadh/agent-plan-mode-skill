---
name: agent-plan-mode
description: Use this skill whenever the user asks for plan mode, planning mode, planning-first workflow, read-only analysis before implementation, or wants an agent/tool that lacks native plan mode to avoid making changes until a plan is approved. This skill is especially important when the user says phrases like "mode plan", "buat rencana dulu", "jangan edit dulu", "analisis dulu", "read-only dulu", "no changes yet", or asks to use an agent while preventing edits. It makes the agent inspect context safely, produce a clear execution plan, and wait for explicit user approval before modifying files, running write operations, committing, pushing, or changing system state.
---

# Agent Plan Mode

Use this skill to emulate plan mode for agents and tools that do not have a native planning-only state. The goal is to let the assistant gather context and propose a safe plan without accidentally changing files, databases, services, repositories, or external systems.

## Core Behavior

Treat the first phase as read-only. During this phase, your job is to understand the request, inspect the relevant context, identify risks, and produce a practical plan. Do not implement until the user explicitly approves execution.

This matters because users often ask for planning when they want control over scope, risk, cost, or approach. A premature edit breaks that trust even if the edit is technically correct.

## When This Skill Is Active

Use this workflow when the user asks to:

- use plan mode or planning mode
- make a plan before implementation
- analyze first and not edit yet
- keep work read-only until approval
- inspect an issue before changing code
- use an agent/tool that does not support native plan mode
- compare approaches before deciding
- produce an implementation plan, migration plan, debugging plan, or rollout plan

Common trigger phrases include:

- "mode plan"
- "plan mode"
- "buat rencana dulu"
- "analisis dulu"
- "jangan edit dulu"
- "read-only dulu"
- "no changes yet"
- "don't implement yet"
- "give me a plan first"
- "pakai agent tapi jangan langsung ubah file"

## Allowed During Planning

You may perform actions that only observe or analyze state:

- Read files and directories.
- Search file names and content.
- Inspect repository structure and configuration.
- Read logs, diagnostics, documentation, schemas, and local metadata.
- Run safe commands that only report information, such as `git status`, `git diff`, `git log`, `php -l`, `npm test -- --help`, `composer show`, or framework-specific dry-run commands.
- Query databases using read-only statements such as `SELECT`, `DESCRIBE`, `SHOW CREATE TABLE`, or metadata inspection, when database access is allowed by the project rules.
- Use web or documentation lookup tools for research.

Prefer the smallest amount of inspection needed to make a good plan. Avoid broad scans if targeted reads are enough.

## Forbidden During Planning

Do not change state while planning. Avoid these actions until the user approves execution:

- Editing, creating, moving, renaming, or deleting files.
- Using patch/edit/write tools.
- Running formatters that rewrite files.
- Installing, removing, or updating dependencies.
- Running database writes such as `CREATE`, `ALTER`, `INSERT`, `UPDATE`, `DELETE`, `DROP`, migrations, seeders, or destructive maintenance commands.
- Starting, stopping, recreating, or mutating containers, services, queues, daemons, cloud resources, or external integrations.
- Changing environment variables, credentials, permissions, secrets, local config, git config, or system settings.
- Staging, committing, amending, merging, rebasing, tagging, pushing, opening PRs, or changing branches.
- Executing commands known to generate or modify files unless the user explicitly allowed that specific command.

If a command might write files as a side effect, treat it as forbidden unless it has a reliable dry-run mode and you use that dry-run mode.

## Planning Workflow

Follow this sequence:

1. Restate the goal in one sentence if the request is ambiguous.
2. Inspect only the context needed to understand the task.
3. Identify constraints from project instructions, user requirements, existing code patterns, and external systems.
4. Call out any blocker or decision that materially changes the plan.
5. Produce a concrete plan with files, commands, verification, risks, and rollback notes when relevant.
6. Ask for explicit approval before implementation.

If the user already supplied enough context, do not over-research. A plan can be short when the task is small.

## Output Format

Use this structure unless the user asks for a different format:

```markdown
**Tujuan**
[One-sentence objective.]

**Konteks Yang Ditemukan**
- [Relevant facts discovered from read-only inspection.]

**Rencana Implementasi**
1. [Step.]
2. [Step.]
3. [Step.]

**File/Area Terdampak**
- `[path or area]`: [expected change or reason]

**Verifikasi**
- `[command or check]`: [what it proves]

**Risiko / Pertanyaan**
- [Risk, assumption, or question. Use "Tidak ada" if none.]

**Menunggu Persetujuan**
Saya belum akan mengubah file atau menjalankan operasi write sampai Anda menyetujui rencana ini.
```

Use the user's language when possible. For Indonesian projects or Indonesian prompts, write the plan in Bahasa Indonesia.

## Approval Boundary

Only leave planning mode after the user clearly approves implementation. Examples of approval:

- "lanjut"
- "implementasikan"
- "terapkan"
- "execute"
- "approved"
- "go ahead"
- "buat perubahannya"
- "ok implement"

Questions, clarifications, or feedback on the plan are not approval. Revise the plan and keep read-only behavior until approval is clear.

## After Approval

Once approved:

- State briefly that you are switching from planning to implementation.
- Follow the approved scope; do not expand beyond it without asking.
- Respect project-specific rules, especially around database writes, secrets, destructive commands, tests, commits, and pushes.
- If new information invalidates the approved plan, pause and present the updated plan before continuing.

## Handling Tool-Specific Constraints

When delegating to another agent or tool that lacks plan mode, include explicit instructions in the delegated prompt:

```text
You are operating in planning-only mode. Do not create, edit, delete, move, or modify files. Do not run write operations, dependency installs, migrations, commits, pushes, or destructive commands. You may only inspect and analyze. Return a concise implementation plan, affected files, verification steps, risks, and questions. Wait for explicit approval before implementation.
```

This reduces accidental writes by making the safety boundary visible to the downstream tool.

## Examples

**Example 1**
User: "Pakai agent buat cek bug login ini, tapi mode plan dulu. Jangan edit file."

Response behavior: inspect auth-related files and logs read-only, then return a debugging and implementation plan. Do not patch code.

**Example 2**
User: "Tool ini tidak punya plan mode. Tolong buat rencana migrasi database tanpa eksekusi."

Response behavior: inspect schema and migration files, then propose migration steps and verification. Do not run write SQL or migrations.

**Example 3**
User: "Analisis dulu komponen checkout ini, nanti saya approve kalau sudah cocok."

Response behavior: read checkout code, identify likely changes, list affected files and tests, then wait for approval.
