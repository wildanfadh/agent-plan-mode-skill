# 🔒 Agent Plan Mode Skill

> **Planning-first, edit-later.** A reusable skill that brings plan-mode behavior to any AI coding agent or tool — even ones that don't natively support it.

Perfect for vibe coders, solo devs, and teams who want the assistant to **inspect, analyze, and propose** before touching code, databases, or infrastructure.

---

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-active-brightgreen.svg)](https://github.com/wildanfadh/agent-plan-mode-skill)

## 🎯 What It Does

This skill forces the AI agent into **read-only planning mode** until you explicitly approve execution. While planning:

| ✅ Allowed | ❌ Forbidden |
|----------|------------|
| Read files & directories | Edit, create, or delete files |
| Inspect schemas & configs | Run database writes (`CREATE`, `ALTER`, etc.) |
| Run `git status`, `git diff`, `git log` | Stage, commit, push, or rebase |
| Research, search, grep | Install/remove/update dependencies |
| Query DB with `SELECT` (read-only) | Start/stop/mutate services or containers |
| Dry-run commands | Formatters that rewrite files |

Once you say **"lanjut"**, **"implement"**, or **"approved"**, the agent switches to full implementation mode following the approved plan.

## 📦 Quick Install

### For pi Coding Agent
```bash
# The skill is already in your pi agent directory:
ls ~/.pi/agent/skills/agent-plan-mode/
```

### For Other AI Coding Agents

Copy the `SKILL.md` file into your agent's skills/prompts directory:

```bash
git clone https://github.com/wildanfadh/agent-plan-mode-skill.git
cp agent-plan-mode-skill/SKILL.md ~/your-agent/skills/plan-mode.md
```

Then add it to your agent's skill library. Most agents auto-discover `.md` skill files in their skills directory.

### Manual Usage

Just paste the content of [`SKILL.md`](SKILL.md) into your prompt or system instructions at the start of a conversation. The skill's rules will take effect immediately.

## 🚀 How to Trigger

Start any conversation with one of these triggers:

| English | Bahasa Indonesia |
|---------|-----------------|
| "plan mode" | "mode plan" |
| "don't implement yet" | "jangan edit dulu" |
| "give me a plan first" | "buat rencana dulu" |
| "read-only analysis" | "analisis dulu" |
| "no changes yet" | "read-only dulu" |

The agent will:
1. Inspect the relevant code/files (read-only)
2. Produce a structured plan with impacted files, steps, risks, and verification
3. **Wait for your explicit approval** before making any changes

## 📋 Example Output

```
**Tujuan**
Perbaiki bug login: session tidak persisten setelah refresh.

**Konteks Yang Ditemukan**
- Auth middleware di `middleware/auth.ts` tidak menyimpan token ke localStorage.

**Rencana Implementasi**
1. Update `middleware/auth.ts` — simpan token ke `localStorage`.
2. Update `pages/login.tsx` — panggil `auth.persist()` setelah login sukses.
3. Tambah unit test di `tests/auth.test.ts`.

**File/Area Terdampak**
- `middleware/auth.ts`: tambah logika persist
- `pages/login.tsx`: panggil persist
- `tests/auth.test.ts`: test case baru

**Verifikasi**
- `npm test`: semua test pass
- Manual: login → refresh browser → session tetap ada

**Risiko / Pertanyaan**
- Apakah token disimpan plaintext? Pertimbangkan httpOnly cookie.

**Menunggu Persetujuan**
Saya belum akan mengubah file sampai Anda menyetujui rencana ini.
```

## 🗂 File Structure

```
agent-plan-mode-skill/
├── SKILL.md          # The actual skill — copy this into any agent
├── evals/
│   └── evals.json    # Evaluation prompts for testing the skill
├── LICENSE           # MIT License
└── README.md         # This file
```

## 🧪 Evaluations

The `evals/` directory contains test prompts to validate the skill works correctly across different agents. Use them to ensure the agent respects the planning-only boundary before deploying to production workflows.

## 🤔 Why This Exists

Many AI coding tools rush into edits. They're great at writing code, but sometimes you want them to **think first**. This skill creates a safety boundary:

- **Prevents accidental file changes** when you just want analysis
- **Improves code quality** by forcing the agent to plan before coding
- **Builds trust** — you approve the approach before any bytes change
- **Works everywhere** — no special agent features needed, just a skill/prompt file

Made for vibe coders who ship fast but want guardrails. 🛡️

## 📄 License

MIT — see [LICENSE](LICENSE) for details.

## 👤 Author

**Wildan Fadh** — [github.com/wildanfadh](https://github.com/wildanfadh)

---

⭐ **Star this repo** if it saves you from premature AI edits!
