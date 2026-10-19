# Setup: your first AI-assisted Databricks build

Never used an AI coding assistant? This is what to have in place before your first prompt, in order. It
describes **what** to set up and why; for **how**, follow each vendor's official documentation, which is
kept current by the vendor. The safety steps are part of the setup: they're what keep you the one who decides.

> **Read first:** see the [disclaimer](#disclaimer) at the end. You are responsible for any software you
> install or use and for any costs it incurs.

## 1. An AI coding assistant

Choose one and install it from the vendor's official site. The build in the videos used **Claude Code**
(Anthropic), so it's the example used on this page. Others (such as Codex, Cursor and Gemini CLI) have their
own setup steps. Check your chosen tool's terms and pricing yourself.

## 2. A project folder with Git

One folder per data product, with Git from the start. Git records saved versions of your files (commits).
Save the approved expected test results, your **answer key**, as their own version before any pipeline code
is written. A commit records a version; it is not a permission lock, so the review is still yours.

## 3. A sandbox Databricks workspace, never production

- Use a workspace that is separate from anything real, and use **synthetic data only**.
- Before your first prompt, understand how that workspace is billed and check the spending controls its
  provider offers. An alert is not a hard spending cap. Choosing an account, plan or workspace type, and
  its costs, is your decision and your responsibility.
- Never point the AI at a workspace that holds real or production data.

## 4. The Databricks CLI and a named profile

Install the Databricks command-line tool (CLI) and sign in by following the Databricks documentation. A
**named profile** labels the connection to your sandbox, so you can check which workspace a command targets.
It does not limit permissions. Sign in yourself, keep credentials out of chat with the AI, and learn how to
revoke that access.

**The AI can do anything the signed-in account can do.** A request such as "delete all catalogs" could
succeed if the assistant's tools and the account's permissions allow it, and the damage and cost can be
large. So:

- Sign in with an account that has **least privilege**: only the access this project needs, in this sandbox.
- Keep deletion and administrative work (dropping catalogs, schemas or tables, changing permissions,
  workspace or billing settings) outside that account's permissions, with an **admin acting by hand**.
- Write the same limits into your rules file (step 6). Written rules guide the AI; account permissions and
  tool restrictions enforce boundaries. You need both.

## 5. Databricks skills for your assistant

Skills are written instructions that help an AI assistant work with a platform. The claims build loaded two
skills from Databricks' AI Dev Kit (`databricks-solutions/ai-dev-kit` on GitHub): **Spark Declarative
Pipelines** and **AI/BI dashboards**. For a new setup, follow the vendor's current guide: the AI Dev Kit's
README now points to **Databricks AI Tools**. Read the license of whatever you install before you use it.

## 6. A rules file

A project rules file states what the assistant must and must not do. For Claude Code it's `CLAUDE.md` in the
project folder; other assistants use their own file (Codex reads `AGENTS.md`). Check that the assistant has
actually loaded your rules before you rely on them. An example to adapt:

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

These rules are guidance the AI follows, not a lock. Account permissions and tool restrictions are what
actually prevent an action, so set both.

## 7. Permission settings

Review the assistant's permission settings. Avoid any mode that bypasses approval. Some actions may already
be allowed by your settings, so don't assume every command or file change will trigger a prompt. Read each
request, check what it targets, and only then approve it.

## 8. The gate skill

Copy the `answer-key-first` folder from this kit into your project's `.claude/skills/` folder (Claude Code;
other assistants have their own location). It instructs the assistant to draft expected results and stop for
your approval before pipeline code. Check the answers yourself: the skill is guidance, not an enforcement lock.

## 9. A second reviewer

A different AI, used only to review the finished build against a written brief (the build in the videos
used Codex, which returned five findings). The builder can't see its own blind spots. A second AI adds a
check; it doesn't replace your review.

## 10. Your first prompt

Start with requirements, not code. Paste the requirements prompt from [starter-prompts.md](starter-prompts.md)
with your own business problem, and keep the [one rule checklist](one-rule-checklist.md) next to you.

## Disclaimer

For education only; not professional, legal or financial advice. You are responsible for any software or services you use, their costs, and anything an AI assistant does with your account's access: use a non-production environment and least-privilege accounts. Not affiliated with any company named. Read the full disclaimer: https://github.com/agentic-data-engineer/automations/blob/main/DISCLAIMER.md

---
From The Agentic Data Engineer starter kit. CC BY 4.0.
