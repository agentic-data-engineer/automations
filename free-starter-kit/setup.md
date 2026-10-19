# Setup: your first AI-assisted Databricks build

Never used an AI coding assistant? This is what to have in place before your first prompt, in order. It
describes **what** to set up and why; for **how**, follow each vendor's official documentation, which is
kept current by the vendor. The safety steps are part of the setup: they're what keep you the one who decides.

> **Read first:** see the [disclaimer](#disclaimer) at the end. You are responsible for any software you
> install or use and for any costs it incurs.

## 1. An AI coding assistant

Pick one and install it from the vendor's official site. The build in the videos used **Claude Code**
(Anthropic). Others include Codex, Cursor and Gemini CLI. Check each one's terms and pricing yourself.

## 2. A project folder under version control

One folder per data product, with git from the start. Git is how you lock decisions: the answer key gets
its own commit before any code exists, and every phase ends with a commit you approved.

## 3. A sandbox Databricks workspace, never production

- Use a workspace that is separate from anything real, and use **synthetic data only**.
- Before your first prompt, understand how that workspace is billed and set the spending controls its
  provider offers. Choosing an account, plan or workspace type, and its costs, is your decision and your
  responsibility.
- Never point the AI at a workspace that holds real or production data.

## 4. The Databricks CLI, signed in with a named profile

Install and sign in by following the Databricks CLI documentation. Use a **named profile** for the
sandbox. The AI then works through a login you control: it never needs your password, and you can revoke
access at any time.

**The AI can do anything the signed-in account can do.** A single prompt such as "delete all catalogs" is
possible, and the damage and cost can be large. So:

- Sign in with an account that has **least privilege**: only the rights this project needs, in this sandbox.
- Keep destructive and administrative actions (dropping catalogs, schemas or tables, changing permissions,
  workspace or billing settings) with an **admin, done by hand**, outside the AI's account.
- Write the same limits into your rules file (step 6). Rules guide the AI; account permissions enforce them.
  You need both.

## 5. The Databricks AI Dev Kit

Databricks' own toolkit for AI coding assistants, published by Databricks on GitHub
(`databricks-solutions/ai-dev-kit`). It adds skills with current Databricks patterns (Spark Declarative
Pipelines, AI/BI dashboards, Unity Catalog, bundles and more) and an optional MCP server. In the claims
build, its **Spark Declarative Pipelines** and **AI/BI dashboards** skills shaped the pipelines and the
dashboard.

Install it by following its own README, and read its license first (at the time of writing, the
Databricks License: use only with Databricks services; not open source).

## 6. A rules file

The assistant reads it at the start of every session, so your rules hold without repeating them. For
Claude Code it's `CLAUDE.md` in the project folder; Codex reads `AGENTS.md`. An example to adapt:

```markdown
# Rules for this project

- Databricks: use only the CLI profile named for the sandbox. Never print a host, token or password.
- Data: synthetic only. Never use real people, organizations or licensed code sets.
- This product must never: [submit, approve, pay, delete, contact a customer...].
- Gates: at each phase gate, stop and wait for my explicit approval. Your confidence or a passing check is not approval.
- Propose, don't decide: give options with a recommendation; record my decision and reason in docs/decisions.md.
- Expected results are written by hand and committed before any pipeline code.
- If you hit something the design didn't plan for, stop and ask. Don't work around a rule.
- Log every mistake you make in docs/ai-mistakes.md: what was wrong, how it was caught, the fix.
- Install, sign up for or pay for nothing without asking me first.
- Never drop, delete or truncate a catalog, schema, table, volume or pipeline, and never change permissions,
  workspace settings or billing. Those are done by an admin, by hand. If one seems needed, stop and ask.
- Never run a command against all objects at once (for example, every catalog or every table).
```

These rules are guidance the AI follows, not a lock. The account's permissions are what actually prevent
an action, so set both.

## 7. Permission prompts on

Keep the assistant in its default mode, where it asks before running commands or changing files. Read and
approve each action yourself until you trust the pattern.

## 8. The gate skill

Copy the `answer-key-first` folder from this kit into your project's `.claude/skills/` folder. It makes
the AI define what "right" looks like, and stop for your approval, before any code exists.

## 9. A second reviewer

A different AI, used only to review the finished build against a written brief (the build in the videos
used Codex). The builder can't see its own blind spots.

## 10. Your first prompt

Start with requirements, not code. Paste the requirements prompt from [starter-prompts.md](starter-prompts.md)
with your own business problem, and keep the [one rule checklist](one-rule-checklist.md) next to you.

## Disclaimer

For education only; not professional, legal or financial advice. You are responsible for any software or services you use, their costs, and anything an AI assistant does with your account's access: use a non-production environment and least-privilege accounts. Not affiliated with any company named. Read the full disclaimer: https://github.com/agentic-data-engineer/automations/blob/main/DISCLAIMER.md

---
From The Agentic Data Engineer starter kit. CC BY 4.0.
