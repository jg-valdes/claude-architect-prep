# CCA-F Prep

An interactive study guide for the **Claude Certified Architect – Foundations (CCA-F)** exam, powered by Claude Code as a built-in study coach.

This repo gives you structured notes for all 5 exam domains and an AI coach that guides you through the material, quizzes you with real exam-style questions, and tracks your progress — all from your terminal.

---

## What's Inside

| Path | Purpose |
|---|---|
| `CLAUDE.md` | Study coach instructions — tells Claude how to guide you |
| `plan.md` | 6-day study plan with checkboxes for progress tracking |
| `notes/en/domain1/` | Agentic Architecture & Orchestration (27%) — 7 files |
| `notes/en/domain2/` | Tool Design & MCP Integration (18%) — 5 files |
| `notes/en/domain3/` | Claude Code Configuration & Workflows (20%) — 6 files |
| `notes/en/domain4/` | Prompt Engineering & Structured Output (20%) — 4 files |
| `notes/en/domain5/` | Context Management & Reliability (15%) — 6 files |
| `notes/es/` | Spanish translations of all notes |

---

## Language Support

This repo supports **English** and **Spanish (Español)**. When you open Claude Code, it will ask which language you prefer before starting. All notes, quizzes, and coaching are fully available in both languages.

| | English | Español |
|---|---|---|
| Notes | `notes/en/` | `notes/es/` |
| Study plan | `plan.md` | `plan.es.md` |

---

## Prerequisites

- [Claude Code](https://claude.ai/code) installed and authenticated
- A CCA-F exam registration (optional until Day 5): https://anthropic.skilljar.com/

---

## Getting Started

```bash
git clone https://github.com/jg-valdes/claude-architect-prep.git
cd claude-architect-prep
claude
```

Once Claude Code opens, just say:

> **"I want to start studying for the CCA-F exam"**

Claude will read your current progress from `plan.md`, tell you what to study next, and guide you from there.

---

## How It Works

The `CLAUDE.md` file turns Claude Code into a study coach that knows this repo. It will:

1. **Orient you** — check `plan.md` to find where you are and what's next
2. **Point you to the notes** — tell you which files to read for the current domain
3. **Quiz you** — after you read and say "quiz me", run an exam-style quiz (one question at a time, 4 options, 3 plausible — same format as the real exam)
4. **Explain every answer** — right or wrong, Claude explains why and covers the trap options
5. **Track your progress** — check off completed items in `plan.md` automatically after each quiz

You can pause and resume any time. Claude always reads `plan.md` to find your current position — no need to explain where you left off.

---

## Study Plan Overview

| Day | Domains | Coverage |
|---|---|---|
| 1 | Domain 3 | Claude Code config, CLAUDE.md, hooks, CI/CD, permissions |
| 2 | Domains 1 & 5 | Multi-agent systems, agentic loops, context management, reliability |
| 3 | Domains 2 & 4 | MCP, tool design, prompt engineering, structured output |
| 4 | All | External practice exams |
| 5 | Weak areas | Targeted review + book the exam |
| 6 | — | Exam day |

---

## Exam Overview

| | |
|---|---|
| **Full name** | Claude Certified Architect – Foundations (CCA-F) |
| **Questions** | 60 multiple choice |
| **Duration** | 120 minutes |
| **Passing score** | 720 / 1000 |
| **Cost** | $99 (free via Claude Partner Network) |
| **Format** | Proctored, no notes |
| **Register** | https://anthropic.skilljar.com/ |

---

## External Resources

| Resource | Type | Cost |
|---|---|---|
| [claudecertificationguide.com/learn](https://claudecertificationguide.com/learn) | **Primary notes source** — 30 task-statement pages, all 5 domains | Free |
| [Anthropic Academy – Claude Code in Action](https://anthropic.skilljar.com/claude-code-in-action) | Official course | Free |
| [Tutorials Dojo CCA-F Study Guide](https://tutorialsdojo.com/cca-f-claude-certified-architect-foundations-study-guide/) | Study guide | Free |
| [claudecertifications.com](https://claudecertifications.com/) | Practice questions | Free |
| [certificationpractice.com](https://certificationpractice.com/practice-exams/anthropic-claude-certified-architect-foundations) | 360 practice questions | Free/Paid |
| [certstud.com](https://certstud.com/certifications/anthropic/claude-architect) | 300+ practice questions | Free |

---

## Contributing

Found an error in the notes? Want to add a domain, improve a quiz topic, or add practice questions? PRs are welcome. Please keep notes focused, concise, and exam-relevant.

---

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
