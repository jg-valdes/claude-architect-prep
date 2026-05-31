# CCA-F Exam Study Plan

**Goal:** Pass the Claude Certified Architect – Foundations (CCA-F) exam
**Format:** 60 questions · 120 min · Passing score: 720/1000 · Cost: $99

---

## Exam Domains

| Domain | Weight | Priority |
|---|---|---|
| Agentic Architecture & Orchestration | 27% | High |
| Claude Code Configuration & Workflows | 20% | High |
| Prompt Engineering & Structured Output | 20% | High |
| Tool Design & MCP Integration | 18% | Medium |
| Context Management & Reliability | 15% | Medium |

---

## How to Use This Repo

Each day covers one or more domains. For each domain:

1. Read the notes in `notes/en/domainX/` at your own pace
2. Come back to Claude Code and say **"quiz me"** — Claude will run an exam-style quiz on what you just read
3. Check off items as you complete them
4. Move to the next domain

The notes and quizzes together cover all 5 exam domains with the same question style as the real exam (4 options, multiple look valid — pick the best one).

---

## Day 1: Foundations — Domain 3

- [ ] Read the official exam guide: https://www.claudedirectory.org/blog/anthropic-claude-certification-program
- [ ] Read the CCA-F study guide: https://tutorialsdojo.com/cca-f-claude-certified-architect-foundations-study-guide/
- [ ] Start Claude Code in Action on Anthropic Academy: https://anthropic.skilljar.com/claude-code-in-action
- [ ] Study Domain 3 – Claude Code Configuration & Workflows
  - [ ] `notes/en/domain3/01_claude_md_hierarchy.md`
  - [ ] `notes/en/domain3/02_custom_slash_commands.md`
  - [ ] `notes/en/domain3/03_cicd_integration.md`
  - [ ] `notes/en/domain3/04_hooks.md`
  - [ ] `notes/en/domain3/05_permissions_model.md`
  - [ ] `notes/en/domain3/06_iterative_refinement.md`
- [ ] Quiz session with Claude on Domain 3 topics

## Day 2: Architecture — Domains 1 & 5

- [ ] Study Domain 1 – Agentic Architecture & Orchestration (27% — biggest domain)
  - [ ] `notes/en/domain1/01_multi_agent_design_patterns.md`
  - [ ] `notes/en/domain1/02_task_decomposition.md`
  - [ ] `notes/en/domain1/03_agentic_loops.md`
  - [ ] `notes/en/domain1/04_subagent_coordination.md`
  - [ ] `notes/en/domain1/05_reliability_patterns.md`
  - [ ] `notes/en/domain1/06_agent_sdk_hooks.md`
  - [ ] `notes/en/domain1/07_session_state_resumption.md`
- [ ] Study Domain 5 – Context Management & Reliability
  - [ ] `notes/en/domain5/01_context_window_management.md`
  - [ ] `notes/en/domain5/02_long_running_task_reliability.md`
  - [ ] `notes/en/domain5/03_escalation_ambiguity.md`
  - [ ] `notes/en/domain5/04_error_propagation.md`
  - [ ] `notes/en/domain5/05_human_review_calibration.md`
  - [ ] `notes/en/domain5/06_information_provenance.md`
- [ ] Quiz session with Claude on Domain 1 & 5 topics

## Day 3: MCP & Prompting — Domains 2 & 4

- [ ] Study Domain 2 – Tool Design & MCP Integration
  - [ ] `notes/en/domain2/01_mcp_fundamentals.md`
  - [ ] `notes/en/domain2/02_stdio_vs_sse.md`
  - [ ] `notes/en/domain2/03_tool_schema_design.md`
  - [ ] `notes/en/domain2/04_tool_error_handling.md`
  - [ ] `notes/en/domain2/05_builtin_tools.md`
- [ ] Study Domain 4 – Prompt Engineering & Structured Output
  - [ ] `notes/en/domain4/01_prompt_engineering.md`
  - [ ] `notes/en/domain4/02_structured_output_patterns.md`
  - [ ] `notes/en/domain4/03_few_shot_prompting.md`
  - [ ] `notes/en/domain4/04_batch_processing.md`
- [ ] Quiz session with Claude on Domain 2 & 4 topics

## Day 4: Practice Exams

- [ ] Take full practice exam #1: https://claudecertifications.com/
- [ ] Review all wrong answers — identify weak domains
- [ ] Take practice exam #2: https://certificationpractice.com/practice-exams/anthropic-claude-certified-architect-foundations
- [ ] Review all wrong answers
- [ ] Take practice exam #3: https://certstud.com/certifications/anthropic/claude-architect
- [ ] Note down recurring weak areas for Day 5 review

## Day 5: Targeted Review + Exam Booking

- [ ] Review all flagged weak areas from Day 4
- [ ] Re-read key trade-off scenarios (the exam picks options where 3 of 4 seem valid)
- [ ] Final quiz session with Claude: full mock Q&A across all 5 domains
- [ ] Book the exam at https://anthropic.skilljar.com/
- [ ] Review exam logistics (proctored, 120 min, no notes)

## Day 6: Exam Day

- [ ] Light review of notes — no cramming
- [ ] Take the CCA-F exam
- [ ] Get certified

---

## Key Concepts Cheat Sheet

### Claude Code (Domain 3)
- `CLAUDE.md` is the primary config file — supports global (`~/.claude/CLAUDE.md`), project (`.claude/CLAUDE.md`), and local overrides
- More specific `CLAUDE.md` wins on conflicts; all levels are merged
- Custom slash commands: `.claude/commands/<name>.md` → `/name`, use `$ARGUMENTS` for input
- CI/CD: `--print` / `-p` flag required for non-interactive mode
- Hooks in `settings.json`: `PreToolUse` can block, `PostToolUse` reacts
- Permissions: deny always beats allow; `--allowedTools` is an allowlist

### Agentic Architecture (Domain 1)
- **Hub-and-spoke**: one orchestrator delegates to specialized subagents
- **Subagents are stateless** — orchestrator must pass all context explicitly
- Define success criteria and max iteration count before starting any loop
- Minimal footprint: request only necessary permissions, prefer reversible actions
- Human escalation is correct for irreversible or ambiguous actions

### Context Management (Domain 5)
- Summarize conversation history when context fills — don't restart
- Checkpoint long tasks after each major step so they can resume on failure
- Chunking: split large inputs, process each independently, aggregate results
- Idempotent operations make retries safe

### MCP (Domain 2)
- Protocol for connecting Claude to external tools and data sources
- **stdio**: local subprocess, no auth, one client — simple
- **SSE**: remote HTTP, requires auth, many clients — shared/scalable
- Tool descriptions drive Claude's selection behavior — be explicit about when to use each tool
- Use `enum` in schemas to constrain valid values

### Prompt Engineering (Domain 4)
- Use XML tags to structure complex prompts
- Few-shot examples go **before** the real input
- Three enforcement layers: prompt-only (weakest) → schema in prompt → tool_use API (strongest)
- Always validate structured output and retry with the specific error message
- Repeat critical format instructions at the end of the prompt (recency bias)

---

## Quick Decision Guide

| Problem | Answer |
|---|---|
| Tool selection wrong | Fix the **description** |
| Invalid tool output values | Add **enum** to schema |
| Unreliable JSON output | Use **tool_use** API enforcement |
| Multi-client MCP | **SSE** transport |
| Local/single-user MCP | **stdio** transport |
| Long task fails mid-way | **Checkpointing** |
| Irreversible action | **Human escalation** |
| Subagents sharing state | They can't — **orchestrator passes everything** |
| Context window full | **Summarize** history |
| Large input won't fit | **Chunking** |

---

## Resources

| Resource | Type | Cost |
|---|---|---|
| [claudecertificationguide.com/learn](https://claudecertificationguide.com/learn) | **Primary notes source** — 30 task-statement pages covering all 5 domains | Free |
| [Anthropic Academy – Claude Code in Action](https://anthropic.skilljar.com/claude-code-in-action) | Official course | Free |
| [Tutorials Dojo CCA-F Study Guide](https://tutorialsdojo.com/cca-f-claude-certified-architect-foundations-study-guide/) | Study guide | Free |
| [claudecertifications.com](https://claudecertifications.com/) | Practice questions | Free |
| [certificationpractice.com](https://certificationpractice.com/practice-exams/anthropic-claude-certified-architect-foundations) | 360 practice questions | Free/Paid |
| [certstud.com](https://certstud.com/certifications/anthropic/claude-architect) | 300+ practice questions | Free |
| [Complete Guide – Towards AI](https://pub.towardsai.net/claude-certified-architect-the-complete-guide-to-passing-the-cca-foundations-exam-9665ce7342a8) | Article | Free |
