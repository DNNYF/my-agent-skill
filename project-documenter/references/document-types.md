# Document Types Reference

Templates and structure for each document type the project-documenter skill produces. Every template is a skeleton — fill it with real, specific information from the codebase. Never leave placeholders unfilled.

**Rule**: These templates exist to remind you what to cover, not to be filled in mechanically. Skip sections that don't apply. Add sections the project needs that aren't listed. The project dictates the structure, not the template.

---

## 1. Project Overview

**Purpose**: One document that tells anyone what this project is, why it exists, and how it works at a high level.
**Audience**: Anyone — developers, managers, stakeholders, new team members.
**Typical length**: 1-3 pages.

### Structure

```markdown
# [Project Name]

[One paragraph: what it does, who it's for, what problem it solves. No fluff.]

## Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Frontend | [actual framework + version] | [actual reason] |
| Backend | [actual framework + version] | [actual reason] |
| Database | [actual DB] | [actual reason] |
| Auth | [actual provider] | [actual reason] |
| Hosting | [actual platform] | [actual reason] |

## Architecture

[1-2 paragraphs describing the actual architecture. Name the patterns.
Include a diagram if the architecture has more than 3 components.]

## Key Features

[Bullet list of what the app actually does. Not marketing copy — functional
descriptions. "Users can create, edit, and delete invoices with line items"
not "Powerful invoice management platform".]

## Project Structure

[Annotated directory tree showing the important folders and what lives in them.
Skip node_modules, .git, and other obvious ones.]

## Getting Started

[Actual steps to run the project locally. Copy-pasteable commands.
Include env var setup, database seeding, etc.]

## Current State

[Honest assessment: what works, what's incomplete, what's known broken.
Include test coverage if available.]
```

### Anti-Slop Notes

- The "Why" column in Tech Stack must contain actual reasoning, not "industry standard" or "widely adopted."
- Key Features are things the app DOES, not things it IS. "Sends email reminders 3 days before invoice due date" not "Email notification system."
- Current State must be honest. If there are no tests, say so. If the login page is broken, say so.

---

## 2. Technical Documentation

**Purpose**: Everything a developer needs to understand the system deeply enough to modify it confidently.
**Audience**: Developers (current team or future).
**Typical length**: 5-15 pages depending on complexity.

### Structure

```markdown
# Technical Documentation — [Project Name]

## Architecture

[Detailed architecture description with diagram. Name every major component,
how they communicate, and what protocols they use.]

### Component Breakdown

[For each major component/module:]

#### [Component Name]
- **Location**: `src/modules/[name]/`
- **Responsibility**: [what it does, one sentence]
- **Key files**:
  - `handler.ts` — [what this file does]
  - `service.ts` — [what this file does]
  - `types.ts` — [what this file does]
- **Dependencies**: [what this component depends on]
- **Depends on this**: [what depends on this component]

## Data Model

[Entity-relationship description. Include actual field names, types,
and relationships. A diagram helps here.]

### [Entity Name]
| Field | Type | Description | Constraints |
|-------|------|-------------|-------------|
| id | UUID | Primary key | auto-generated |
| ... | ... | ... | ... |

**Relations**: [actual relationships with other entities]

## API Reference

[For each endpoint group:]

### [Resource] Endpoints

#### `[METHOD] /api/[path]`
- **Auth**: [required/optional, what role]
- **Body**: [actual request shape]
- **Response**: [actual response shape with status codes]
- **Notes**: [gotchas, rate limits, special behavior]

## Authentication & Authorization

[How auth works in this specific project. Not "the system uses JWT" but
"Auth is handled by NextAuth with Google and GitHub providers, configured in
src/lib/auth.ts. Session tokens are stored in HTTP-only cookies. Role-based
access uses middleware in src/middleware.ts — only 'admin' role can access
/api/admin/* routes."]

## External Integrations

[For each external service:]

### [Service Name]
- **Purpose**: [what it's used for in this project]
- **Configuration**: [which env vars, which config files]
- **API version**: [version used, date last verified]
- **Gotchas**: [rate limits, quirks, known issues]

## Development Workflow

### Running Locally
[Exact commands, in order]

### Testing
[How to run tests, what's covered, what's not]

### Building
[Build command, what it produces, where output goes]

### Deploying
[Step-by-step deployment, or CI/CD description]

## Environment Variables

| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| DATABASE_URL | Yes | [actual description] | postgresql://... |
| ... | ... | ... | ... |

[Note which are secrets vs configuration. Never include actual secret values.]

## Known Issues & Tech Debt

[Bullet list of real issues. Not aspirational "future improvements" but
actual problems someone will encounter.]
```

### Anti-Slop Notes

- Component breakdown must reference actual files. If you write `handler.ts` but the file is actually called `controller.ts`, you've introduced a lie.
- API reference uses actual endpoint paths from the codebase, not invented ones.
- "Known Issues" is not "Future Improvements." List real problems: "The search endpoint does a full table scan on queries over 1000 rows" not "Search performance could be enhanced."

---

## 3. Handoff Document

**Purpose**: Everything a new developer needs to become productive on this project within a day.
**Audience**: Developer joining the project for the first time.
**Typical length**: 3-8 pages.

### Structure

```markdown
# Handoff — [Project Name]

## What This Is

[2-3 sentences. What the project does, who uses it, current state.]

## Getting Running in 5 Minutes

[Numbered steps, copy-pasteable. Include every prerequisite:
Node version, database setup, env vars, seed data.]

1. Clone: `git clone [repo]`
2. Install: `[package manager] install`
3. Set up env: `cp .env.example .env` — fill in [specific vars]
4. Database: `[migration command]`
5. Seed: `[seed command]` (optional but recommended)
6. Run: `[start command]`
7. Open: `http://localhost:[port]`

## Where Things Live

[Annotated project structure — only the parts that matter]

```
src/
├── app/          # Next.js App Router pages
│   ├── (auth)/   # Auth-gated routes
│   └── api/      # API routes
├── components/   # React components (organized by feature)
├── lib/          # Shared utilities, configs, DB client
└── types/        # TypeScript type definitions
```

## How Things Work

[3-5 key flows explained in plain language. The most important user
journeys and how they map to code.]

### [Flow 1: e.g., "User creates an invoice"]
1. User fills form in `components/InvoiceForm.tsx`
2. Form submits to `POST /api/invoices`
3. Handler in `app/api/invoices/route.ts` validates with Zod schema
4. Prisma creates record in `invoices` table
5. If `sendEmail` flag is set, queues email via `lib/email.ts`

### [Flow 2: e.g., "Authentication"]
[Same pattern — trace the actual code path]

## Things That Will Bite You

[The stuff nobody writes down but everyone discovers the hard way]

- [Gotcha 1: e.g., "The dev server takes 30s to start because Prisma
  generates types on every restart. Run `prisma generate` once first."]
- [Gotcha 2: e.g., "Tests use a separate SQLite database. If you see
  'table not found', run `npm run test:migrate` first."]
- [Gotcha 3: e.g., "The Stripe webhook endpoint must use raw body
  parsing. Don't add body-parser middleware to that route."]

## Who to Ask / Where to Look

[Links to related docs, Slack channels, other team members, design files]

## What's Not Done Yet

[Honest list of incomplete features, known bugs, planned work]
```

### Anti-Slop Notes

- "Getting Running" must be tested. If step 4 fails because the migration command changed, the handoff doc is worse than nothing.
- "Things That Will Bite You" is the most valuable section. Mine the codebase for non-obvious setup requirements, weird environment dependencies, and silent failure modes.
- Don't include generic advice like "review the code to understand the architecture." They will. Point them at the specific files that matter.

---

## 4. POC Report

**Purpose**: Prove that something works (or doesn't). Show feasibility, demonstrate technical decisions, present results.
**Audience**: Stakeholders, investors, technical leads, academic reviewers.
**Typical length**: 5-10 pages. Often delivered as PDF or Word.

### Structure

```markdown
# POC Report: [What Was Proved]

## Executive Summary

[3-5 sentences. What was the question, what did you build, what's the answer.
A busy person should be able to read only this and know the outcome.]

## Problem Statement

[What question or hypothesis this POC addresses. Be specific:
"Can we process 10,000 invoices per minute using serverless functions?"
not "Evaluate system scalability."]

## Approach

[What was built, which technologies were used, and why. Include architecture
diagram if the system has multiple components.]

### Technology Choices

| Choice | Selected | Alternative Considered | Rationale |
|--------|----------|----------------------|-----------|
| Runtime | [actual] | [actual alternative] | [actual reason with data] |
| Database | [actual] | [actual alternative] | [actual reason with data] |

## Implementation

[Key technical details of what was built. Include code snippets only when
they illustrate an important decision or technique. Don't dump code.]

## Results

[Actual measurements, screenshots, data. This is the proof.]

### Performance
[If applicable: benchmarks, response times, throughput numbers]

### Functionality
[What works, what partially works, what doesn't work]

### Screenshots / Demos
[Visual evidence of the working system]

## Analysis

[What the results mean. Did the POC succeed or fail? What were the surprises?
What would be different in a production version?]

## Recommendation

[Clear, opinionated recommendation based on results.
"Proceed to production" / "Pivot to alternative X" / "Needs more investigation
because Y". Not "Further analysis is recommended."]

## Appendix

[Raw data, full benchmarks, environment details, reproduction steps]
```

### Anti-Slop Notes

- Executive Summary must contain the conclusion, not just set up the question. "We proved X works at Y scale" or "X does not meet requirements because Z."
- Results must include actual numbers, actual screenshots. Not "The system demonstrated acceptable performance." What was the p99 latency? How many concurrent users?
- Recommendation must be a clear yes/no/conditional. Never "stakeholders should evaluate options."

---

## 5. Project Presentation (PPT / Canva)

**Purpose**: Summarize the project for a live audience. Slides support the speaker, not replace them.
**Audience**: Mixed — varies by context (demo day, client meeting, internal review).
**Typical length**: 8-15 slides.

### Slide Structure

```
Slide 1: Title
  [Project name, subtitle, date, presenter]

Slide 2: The Problem
  [What problem exists, who has it, why it matters.
  One clear statement, one supporting visual.]

Slide 3: The Solution
  [What you built, in one sentence. Screenshot or mockup.]

Slide 4-5: How It Works
  [Architecture or user flow. Diagram, not bullets.
  Show the system, don't describe it.]

Slide 6-7: Key Features
  [2-3 features with screenshots. One feature per slide if
  they're visually rich. Group if they're simple.]

Slide 8: Tech Stack
  [Visual layout — logos or icons, not a text list.
  Include WHY each choice was made in speaker notes.]

Slide 9: Demo / Results
  [Live demo link, or screenshots of working features.
  If POC: include performance numbers as large stats.]

Slide 10: What's Next
  [Concrete next steps, not vague "future plans."
  "Ship auth by March 15" not "Enhanced security features."]

Slide 11: Q&A / Contact
  [Clean closing slide]
```

### Anti-Slop Notes

- Slides are visual. If a slide has more than 30 words, it has too many words.
- Never put the same text in speaker notes AND on the slide. Slide = visual anchor, notes = what you say.
- Screenshots must be real, not mockups (unless the feature isn't built yet — then label it "mockup").
- "How It Works" uses a diagram, not a bullet list of components.

---

## 6. Status Report

**Purpose**: Where the project is, what happened since last update, what's blocking.
**Audience**: Management, clients, stakeholders.
**Typical length**: 1-2 pages.

### Structure

```markdown
# Status Report — [Project Name]
**Period**: [date range]
**Author**: [name]

## Summary

[2-3 sentences: overall status (on track / at risk / blocked), key accomplishment,
key concern]

## Completed This Period

- [Specific deliverable]: [result]. Example: "Invoice PDF export: implemented
  and deployed. Users can download invoices as PDF from the detail page."
- [Specific deliverable]: [result]

## In Progress

- [Task]: [% complete or current state, expected completion]
- [Task]: [% complete or current state, expected completion]

## Blocked / At Risk

- [Issue]: [what's blocking, what's needed to unblock, who owns it]

## Metrics (if applicable)

| Metric | Previous | Current | Target |
|--------|----------|---------|--------|
| [metric] | [value] | [value] | [value] |

## Next Period Plan

- [Planned deliverable with target date]
- [Planned deliverable with target date]
```

### Anti-Slop Notes

- "Completed" items must be verifiable. Not "Worked on invoice feature" but "Invoice CRUD endpoints shipped, PR #47 merged."
- "Blocked" must include who can unblock and what they need to do.
- Metrics use real numbers. If you don't have metrics, skip the section — don't invent them.

---

## 7. API Documentation

**Purpose**: Complete reference for every endpoint the API exposes.
**Audience**: Developers consuming the API (internal or external).
**Typical length**: Varies — one page per endpoint group.

### Structure

Follow the actual API structure from the codebase. Group by resource (users, invoices, payments), not by HTTP method.

```markdown
# API Reference — [Project Name]

**Base URL**: `[actual URL]`
**Auth**: [actual auth method — Bearer token, API key, session cookie]

## Authentication

[How to authenticate. Actual example with curl or fetch.]

## [Resource Name]

### List [resources]
`GET /api/[resources]`

**Query params**:
| Param | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| page | int | no | 1 | Page number |
| limit | int | no | 20 | Items per page (max 100) |
| status | string | no | all | Filter: active, archived, all |

**Response** `200`:
```json
{
  "data": [...],
  "meta": { "total": 42, "page": 1, "limit": 20 }
}
```

**Errors**:
| Status | Meaning |
|--------|---------|
| 401 | Missing or invalid auth token |
| 403 | Insufficient permissions |

### Create [resource]
`POST /api/[resources]`

**Body**:
```json
{
  "name": "string (required, 1-100 chars)",
  "email": "string (required, valid email)",
  "role": "string (optional, default: 'member', options: 'admin' | 'member')"
}
```

[Continue for each endpoint...]
```

### Anti-Slop Notes

- Extract endpoint shapes from actual route handlers. Don't invent request/response bodies.
- Include actual validation rules (min/max length, regex patterns, enum values).
- Error responses must be real — check what the error handler actually returns.
- If the API has no rate limiting, don't add a "Rate Limiting" section saying "TBD."

---

## 8. Changelog / Release Notes

**Purpose**: What changed between versions.
**Audience**: Users and developers.
**Typical length**: Half a page per release.

### Structure

```markdown
# Changelog

## [version] — [date]

### Added
- [Feature]: [what it does, not how it was implemented]

### Changed
- [What changed]: [from what to what]

### Fixed
- [Bug]: [what was wrong, what it does now]

### Removed
- [What was removed]: [why, and what to use instead]

### Breaking
- [What breaks]: [migration steps]
```

### Anti-Slop Notes

- Write for users, not developers. "Invoice PDF now includes tax breakdown" not "Added tax fields to PDF render pipeline."
- Breaking changes must include migration steps. Not "API changed" but "POST /api/invoices now requires `currency` field — add `"currency": "USD"` to existing integrations."
- Don't pad. If a release has one fix, it has one line. Don't manufacture "improvements" to fill space.

---

## 9. Deployment Guide

**Purpose**: How to deploy and maintain the project in production.
**Audience**: DevOps engineers, developers handling deployment.
**Typical length**: 2-5 pages.

### Structure

```markdown
# Deployment Guide — [Project Name]

## Prerequisites

[Exact versions, accounts, and access needed]

- [Runtime]: version [X] or higher
- [Platform account]: with [specific permissions]
- [CLI tool]: installed via [command]

## Environment Setup

[Every environment variable with description. Never include actual secrets.]

## Deployment Steps

### First-Time Setup
[Numbered steps for initial deployment]

### Subsequent Deployments
[Steps for updating an existing deployment]

### CI/CD (if configured)
[How the pipeline works, what triggers it, where config lives]

## Infrastructure

[What runs where — diagram if complex]

| Service | Platform | Region | Scaling |
|---------|----------|--------|---------|
| App | [actual] | [actual] | [actual config] |
| Database | [actual] | [actual] | [actual config] |

## Monitoring & Alerts

[What's monitored, where to check, how to respond to alerts]

## Rollback

[Exact steps to roll back a bad deployment]

## Troubleshooting

[Common deployment failures and their fixes]
```

### Anti-Slop Notes

- Every command must be copy-pasteable. Test them.
- "Prerequisites" means exact versions, not "recent version of Node."
- If there's no monitoring, say "No monitoring configured" — don't write an aspirational monitoring section.
