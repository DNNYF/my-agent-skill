---
name: project-documenter
description: >
  This skill should be used when the user asks to "document this project",
  "create project documentation", "buat dokumentasi", "bikin docs", "write a
  project summary", "prepare a presentation", "buat PPT", "bikin PDF", "create
  a POC document", "write a Word doc for this project", "document the codebase",
  "create a handoff document", "buat laporan project", "project report",
  "technical documentation", or any task involving generating documentation,
  presentations, reports, or deliverables from an existing or completed project.
  Also trigger when the user wants to push documentation to external platforms
  like Notion, Jira, Canva, or Google Docs. This skill reads the codebase first,
  confirms every decision with the user, and produces expert-quality output.
version: 1.0.0
---

# Project Documenter

Read a codebase. Understand what was built. Produce documentation that a human expert would write — not AI-generated template filler.

This skill creates project documentation, presentations, reports, and deliverables across multiple formats (Markdown, PDF, Word, PowerPoint) and platforms (local files, Notion, Jira, Canva). It reads the actual codebase before writing anything, and confirms every decision with the user before acting.

---

## Core Philosophy

1. **Read before writing** — Scan the codebase first. Understand the architecture, tech stack, structure, and conventions before producing a single line of documentation. Never ask the user to explain what their code does when you can read it yourself.
2. **Confirm before acting** — Every action requires user confirmation. Where to save, what format, what platform, what to include, what to skip. Never assume.
3. **Check your capabilities** — Before promising any output format or platform, verify you have the required skills and MCPs. If something is missing, tell the user immediately with a specific explanation of what's needed.
4. **Write like you built it** — Documentation should read like it was written by the engineer who built the project, not by someone summarizing from the outside. Use the project's own terminology, reference actual file paths, cite real function names.
5. **Zero AI slop** — No filler, no padding, no generic language. Every sentence must carry specific information about THIS project. Read the anti-slop rules below.

---

## Confirm-First Protocol

**NEVER skip this.** Before producing any output, walk through this confirmation sequence with the user:

### Step 1: Understand the Goal

Ask (if not already clear). Do NOT proceed until you have answers:
- What is this document for? (internal reference, client presentation, POC demo, handoff, stakeholder update, academic submission, portfolio)
- Who is the audience? (developers, non-technical stakeholders, investors, professors, new team members)

**Hard rule**: If the user's initial message does not specify both purpose and audience, you MUST ask before scanning the codebase. A one-line question is fine: "Before I scan — who is this doc for and what's the occasion?"

### Step 2: Check Output Format

Ask:
- What format do you need? (Markdown, PDF, Word/DOCX, PowerPoint/PPTX, Notion page, Jira ticket/page, Canva design)
- If multiple: which is the primary, which are derivatives?

### Step 3: Check Destination

Ask:
- Where should this be created?
  - Local file: which directory? (suggest a sensible default like `docs/` in the project root)
  - Notion: which workspace/page?
  - Jira: which project/space?
  - Canva: new design or existing?
  - Other: specify

### Step 4: Confirm Scope

Present a brief outline of what you plan to document based on your codebase scan:
- "I've scanned the project. Here's what I plan to cover: [outline]. Does this look right, or should I add/remove anything?"

### Step 5: Confirm and Execute

Only after all confirmations, begin producing output. If the user changes their mind mid-way, stop and re-confirm.

**Minimum confirmation bar**: You need at least Step 1 (goal + audience) and Step 4 (scope outline approval) before writing ANY documentation content. Steps 2 and 3 can use sensible defaults if the user says "just pick whatever works" — but Steps 1 and 4 are non-negotiable.

---

## Capability Check Protocol

**Before promising ANY output, verify you can deliver it.** This is non-negotiable.

### How It Works

When the user requests a specific format or platform, immediately check whether the required skill or MCP is available. Do not assume — verify.

### Required Capabilities by Output

| Output | Required Skill / MCP | What to Check |
|--------|---------------------|---------------|
| PDF | `pdf` skill | Can you create/manipulate PDFs? |
| Word / DOCX | `docx` skill | Can you create/manipulate Word docs? |
| PowerPoint / PPTX | `pptx` skill | Can you create/manipulate presentations? |
| Notion page | Notion MCP tools (`notion_*`) | Are Notion tools available in your environment? |
| Jira ticket/page | Jira MCP tools (`jira_*`) | Are Jira tools available? |
| Canva design | Canva MCP tools (`canva-design_*`) | Are Canva tools available? |
| Markdown | (always available) | No check needed |
| Mermaid diagrams | `doc-output` skill (for rendering to PDF/DOCX) | Can you render Mermaid to images? |
| Excel / XLSX | `xlsx` skill | Can you create spreadsheets? |

### When a Capability Is Missing

If a required skill or MCP is not available, tell the user immediately. Be specific:

**Template:**
```
I don't have the [skill/MCP name] available in this environment, so I can't
create [format] directly.

Options:
1. I can create [alternative format] instead (e.g., Markdown instead of PDF)
2. You can install [specific skill/MCP] and I'll use it:
   - [installation instruction]
3. I can generate the content and you can manually [convert/upload] it

Which do you prefer?
```

**Examples:**

Missing PDF skill:
```
I don't have the `pdf` skill available, so I can't generate a PDF directly.

Options:
1. I can create a Markdown file — you can convert it to PDF with any Markdown
   editor or Pandoc
2. Install the pdf skill: copy the `pdf/` folder to your skills directory
3. I can create a Word doc instead (if the `docx` skill is available)

Which works for you?
```

Missing Notion MCP:
```
I don't see Notion MCP tools in this environment, so I can't push to Notion
directly.

Options:
1. I can create a Markdown file that you paste into Notion (formatting transfers
   well)
2. Connect the Notion MCP to your environment — check your MCP config
3. I can create in a different platform (Jira, local file, etc.)

Which do you prefer?
```

### Partial Capability

Some outputs need multiple capabilities. For example:
- A PDF with embedded diagrams needs `pdf` skill + Mermaid rendering (via `doc-output`)
- A Notion page with attached spreadsheet needs Notion MCP + `xlsx` skill

Check ALL requirements before starting. If you can deliver the core but not extras, say so:
```
I can create the Notion page, but I can't attach an Excel file because the
`xlsx` skill isn't available. I can include the data as a table in the Notion
page instead. OK?
```

---

## Codebase Scanning

Before writing documentation, scan the project systematically. This is not optional — documentation written without reading code is fiction.

### What to Scan

1. **Project structure** — Directory tree, key folders, entry points
2. **Package/dependency files** — `package.json`, `requirements.txt`, `go.mod`, `Cargo.toml`, `pyproject.toml`, etc. Extract: project name, version, dependencies, scripts
3. **README and existing docs** — Read what already exists. Don't contradict it. Build on it.
4. **Configuration files** — `.env.example`, docker configs, CI/CD, linter configs. These reveal deployment and quality patterns.
5. **Source entry points** — `main.*`, `app.*`, `index.*`, `server.*`. Follow the dependency tree from here.
6. **Database/schema files** — Migrations, models, schema definitions. These are the source of truth for data.
7. **API definitions** — Route files, OpenAPI specs, GraphQL schemas
8. **Test files** — What's tested reveals what matters. Test structure reveals project priorities.

### What to Extract

- Tech stack (languages, frameworks, libraries, databases, infrastructure)
- Architecture pattern (monolith, microservices, serverless, etc.)
- Key features and functionality
- Data model / entities
- API surface
- External integrations
- Development workflow (build, test, deploy)
- Project maturity signals (test coverage, CI/CD, documentation state)

### Scanning Discipline

- Read actual files. Do not guess from directory names alone.
- Cross-reference: if `package.json` lists `prisma`, find and read the schema file.
- Note gaps: if there are no tests, that's worth documenting. If there's no CI/CD, say so.
- Capture the real state, not the ideal state. If the code has tech debt, the docs should acknowledge it.

---

## Document Types

This skill produces multiple document types. Each has a specific purpose and audience. See `references/document-types.md` for full templates.

### Quick Reference

| Document | Purpose | Typical Audience | Format |
|----------|---------|-----------------|--------|
| Project Overview | What is this, why, how | Anyone | Markdown, Notion, PDF |
| Technical Documentation | Architecture, setup, API | Developers | Markdown, Notion |
| Handoff Document | Everything a new dev needs | New team members | Markdown, PDF, Notion |
| POC Report | Prove feasibility | Stakeholders, investors | PDF, Word, PPT |
| Project Presentation | Summarize for an audience | Mixed | PPT, Canva, PDF |
| Status Report | Progress update | Management, clients | Word, PDF, Notion, Jira |
| API Documentation | Endpoint reference | Developers, consumers | Markdown, Notion |
| Database Documentation | Schema, relations, queries | Developers, DBAs | Markdown, PDF |
| Deployment Guide | How to deploy and maintain | DevOps, developers | Markdown, Notion |
| Changelog / Release Notes | What changed | Users, developers | Markdown, Notion |

The user may request any combination. Always confirm which ones before starting.

---

## Key Principles

1. **Specificity over completeness** — A 2-page doc that says real things about this project beats a 10-page template with generic filler.
2. **Reference real artifacts** — Link to actual files, cite real function names, show real config values (redact secrets). `"Authentication is handled by NextAuth in src/lib/auth.ts"` beats `"The system implements a robust authentication mechanism."`.
3. **Opinionated structure** — Don't present 5 alternatives when the project already made a choice. Document what IS, not what COULD BE.
4. **State the non-obvious** — Skip things anyone can see from `ls`. Document WHY decisions were made, WHERE the tricky parts are, WHAT the gotchas are.
5. **Honest about gaps** — If there are no tests, say "No test suite exists." If deployment is manual, say so. Don't pretend the project is more mature than it is.
6. **Match the audience** — A developer handoff document uses different language than an investor presentation. Adjust depth and terminology accordingly.
7. **Research before claiming** — If the documentation references external services, APIs, or libraries, verify they're current. Don't document an integration against an API version that no longer exists. Apply the research protocol.
8. **"Why This, Not That" for tech stack** — Every technology listed in a tech stack section MUST include a one-sentence reason WHY it was chosen and WHAT the main alternative would be. `"Next.js 14 (App Router) — chosen for built-in SSR and file-based routing; alternative: Remix if loader-centric data fetching is preferred"` beats a bare bullet `"Next.js"`. Infer the reasoning from the codebase (dependencies, config, patterns) when possible. If the reasoning is genuinely unclear from code alone, say so: `"Reason for choosing X over Y is not evident from the codebase."`

---

## Zero AI Slop - Hard Rules

Documentation AI slop is different from code AI slop. It's more insidious because docs are supposed to be polished — which makes it easy to mistake polished filler for real content.

### Documentation Slop Patterns (NEVER produce these)

- **The Introduction That Says Nothing**: "This document provides a comprehensive overview of the project architecture and its key components." — Delete it. Start with what the project IS.
- **The Padded Feature List**: Turning 3 features into 8 by splitting "user authentication" into "login", "registration", "password reset", "session management", "security". If they're one module, document them as one thing.
- **The Non-Specific Architecture Section**: "The application follows a modern, scalable architecture with clear separation of concerns." — Which architecture? What's separated from what? Name the layers.
- **The Buzzword Stack**: "Cloud-native, event-driven, microservice-based platform leveraging cutting-edge AI/ML capabilities." — Name the actual services. What cloud? What events? What services? What model?
- **Generic Recommendations**: "Future improvements could include enhanced security, better performance, and improved user experience." — These apply to literally every project. Name specific improvements.
- **The Recap Conclusion**: "In conclusion, this project demonstrates a well-structured approach to solving [problem]." — If you need a conclusion, add new information. Otherwise, just end.

### What Expert Documentation Looks Like

- **Starts with what matters**: "InvoiceTracker is a Next.js 14 app that manages freelance invoices with Stripe integration. It uses Supabase for auth and storage, deployed on Vercel."
- **References real things**: "Rate limiting is configured in `middleware.ts` using Upstash Redis — 60 requests per minute per IP, configurable via `RATE_LIMIT_RPM` env var."
- **States decisions and trade-offs**: "We chose SQLite over Postgres because this is a single-user desktop app. If it goes multi-user, migrate to Postgres — the Prisma schema is already compatible."
- **Acknowledges gaps honestly**: "Email notifications are stubbed but not implemented. The `sendEmail()` function in `lib/email.ts` logs to console. Connect a provider (Resend, SendGrid) before production."

### The Documentation Slop Test

Before finalizing any section:

1. **Swap test**: Could this section appear in documentation for a completely different project? If yes, rewrite with specific details.
2. **Developer test**: Would a developer joining tomorrow learn something useful from this sentence? If no, cut it.
3. **Read-aloud test**: Read it aloud. Does it sound like a person explaining their project, or like a template being filled in? If template, rewrite.
4. **Length test**: Is this section long because there's a lot to say, or because you're padding? Cut padding ruthlessly.

For expanded anti-slop patterns specific to documentation, consult `references/anti-slop-docs.md`.

---

## Research-First Protocol

Documentation that references outdated APIs, deprecated libraries, or wrong pricing is worse than no documentation — it actively misleads.

### When to Research

- **External service references** — If the project uses Stripe, Supabase, Vercel, AWS, etc., verify current API versions, pricing, and configuration approaches before documenting them.
- **Library versions** — If you're documenting dependencies, check their current status. Is the library still maintained? Has it been renamed? Is there a migration guide for the version used?
- **Deployment platforms** — If documenting deployment, verify current CLI commands, dashboard paths, and configuration formats. These change frequently.
- **Framework patterns** — If documenting "how to add a feature to this project," verify the framework's current recommended approach matches what you're about to write.

### How Research Appears in Docs

Show currency:
```markdown
## Deployment (Vercel)

Deployed via Vercel CLI v37.x. Push to `main` triggers auto-deploy.

Environment variables configured in Vercel Dashboard > Settings > Environment Variables.
Required vars listed in `.env.example`. Current Vercel free tier allows 100 deployments/day
(verified May 2025).
```

Not:
```markdown
## Deployment

The application is deployed using modern cloud infrastructure with automated CI/CD pipelines.
```

---

## Language & Tone

- Match the user's language. Indonesian prompt gets Indonesian docs. Mixed is fine — use English for technical terms (API, database, deploy, middleware).
- Tone: clear, direct, like an engineer writing for other engineers. When the audience is non-technical, shift to plain language without dumbing down.
- Be concise. A handoff doc that takes 5 minutes to read is better than one that takes 30 and says the same things.
- Use the project's own naming. If the codebase calls it `InvoiceService`, the docs call it `InvoiceService`, not "the invoice management subsystem."

---

## Workflow Summary

```
1. User requests documentation
2. Read the codebase (scan files, extract structure)
3. CONFIRM: goal, audience, format, destination, scope
4. CHECK: required skills/MCPs available? If not, tell user with options
5. CONFIRM: present outline for approval
6. Write documentation
7. VERIFY: slop test, accuracy check, format check
8. Deliver to confirmed destination
```

Every step with CONFIRM or CHECK requires user input before proceeding.

---

## Additional Resources

### Reference Files

- **`references/document-types.md`** — Templates and structure for each document type with field-by-field guidance
- **`references/platform-guide.md`** — Platform-specific instructions for Notion, Jira, Canva, and local file output
- **`references/anti-slop-docs.md`** — Expanded documentation anti-slop patterns with before/after rewrites
