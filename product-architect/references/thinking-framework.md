# Thinking Framework - Deep Dive

Detailed guidance for each of the 5 thinking phases. Consult this when working through complex or ambiguous product ideas.

---

## Phase 1: Decompose - Detailed

The goal is to reduce a fuzzy idea to four clear answers: Who, What Problem, Why Now, and Value Proposition.

### Questioning Strategy

When information is missing, ask questions that are:
- **Specific** - "Who is your primary user?" not "Tell me about the product"
- **Decision-linked** - Every question must explain what design decision it affects
- **Bounded** - Maximum 5 questions per round. More than that overwhelms the user.

**Question template:**
```
Before I start designing, I need clarity on a few things:

1. [Question] - This affects [which design decision]
2. [Question] - This determines [what architectural choice]
3. [Question] - Without this, I can't [specific thing]

Answer what you can. For anything you're unsure about, say "not sure" and I'll propose options.
```

### Common Decomposition Mistakes

| Mistake | Example | Fix |
|---------|---------|-----|
| Too broad persona | "Everyone who uses the internet" | Narrow to behavior: "Freelancers who invoice more than 5 clients monthly" |
| Vague problem | "Communication is hard" | Be specific: "Remote teams lose context when discussions happen across Slack, email, and meetings" |
| Solution masquerading as problem | "We need a dashboard" | Ask: "What problem does the dashboard solve? What decisions will users make from it?" |
| No competitive context | "Nothing like this exists" | Challenge: "How do people solve this today? Spreadsheets? Manual process? Different tool?" |

### The "Why Not Spreadsheet" Test

For any product idea, ask: "Could this be solved with a spreadsheet/Google Form/Notion page?" If yes, the product either needs a stronger differentiator or should start as something simpler. This isn't dismissive - it's scope calibration.

### Extracting Value Proposition

The value proposition must pass the "so what" test. Keep asking "so what?" until you reach something a user would pay for or change behavior for.

- "It organizes tasks" -> So what? -> "Teams can see who's doing what" -> So what? -> "Managers stop interrupting people to ask for status updates"
- The last answer is the value proposition.

---

## Phase 2: Scope - Detailed

Scoping is the most important phase. Most products fail not from bad execution but from building too much.

### The MVP Litmus Test

A feature belongs in MVP only if ALL three conditions are true:
1. Without it, the core use case cannot be completed
2. There is no acceptable manual workaround
3. It can be built within the time/resource constraints

If any condition fails, the feature goes to v2.

### Scoping Techniques

**Feature Priority Matrix:**

| Feature | Solves core problem? | Manual workaround? | Effort | Verdict |
|---------|---------------------|-------------------|--------|---------|
| User auth | Yes | No | Low | MVP |
| Email notifications | No | User checks manually | Medium | v2 |
| Admin dashboard | No | Direct DB queries | High | v2 |
| Data export | Partially | Copy-paste | Low | Maybe MVP |

**The "One User" Test:**
Imagine only one person will ever use this product. What features do they absolutely need? Build that first.

### Handling Scope Creep

When the user adds features mid-discussion:

1. Acknowledge the idea is good
2. Evaluate against the MVP litmus test
3. If it fails, propose it for v2 with a reason
4. If it passes, add it and re-evaluate the timeline

Template response:
```
Good idea. Let me evaluate: [Feature] is useful but [the core use case works without it / 
there's a manual workaround for now]. I'd put this in v2 and keep MVP focused on [core value]. 
This way we ship in [timeline] instead of [longer timeline]. Sound good?
```

### Risk Assessment

Risks fall into three categories:

| Category | Examples | Mitigation approach |
|----------|---------|-------------------|
| Technical | "Can we build real-time sync?" | Spike / prototype before committing |
| Business | "Will anyone pay for this?" | Launch free MVP, measure usage |
| UX | "Will users understand the workflow?" | User testing with prototype |

Identify the top 2-3 risks. Each must have a mitigation strategy. "Hope for the best" is not a strategy.

### Assumptions Log

Every product plan rests on assumptions. Make them explicit:

```
## Critical Assumptions
1. Users are willing to create an account (validates with: conversion rate on signup)
2. The primary workflow takes < 5 minutes (validates with: user testing)
3. Existing tools (Notion, Spreadsheets) are insufficient (validates with: user interviews)
```

If an assumption is proven wrong, the plan must change. Acknowledging this upfront prevents costly surprises.

---

## Phase 3: Architect - Detailed

### Research Gate (Mandatory Before Any Recommendation)

Phase 3 is where training-data reliance causes the most damage. Before recommending any technology, service, or pattern, complete the research gate:

**Step 1: Verify currency**
For every technology you're about to recommend, search for:
- `"[technology] latest version"` — Is the version in your memory still current?
- `"[technology] changelog"` or `"[technology] releases"` — Any breaking changes since your training cutoff?
- `"[technology] deprecated"` or `"[technology] end of life"` — Is it still alive?

**Step 2: Check alternatives**
Search for `"[technology] vs alternatives [current year]"` or `"best [category] [current year]"`. The landscape shifts constantly:
- New entrants may outperform your default recommendation (e.g., Drizzle emerging as Prisma alternative, Bun emerging as Node alternative)
- Existing tools may have pivoted or changed pricing models
- Community sentiment may have shifted (e.g., adoption of one tool dropping due to licensing changes)

**Step 3: Validate pricing and limits**
For any paid service (hosting, auth, database, email, etc.):
- Search for current pricing page
- Check free tier limits specifically — these change frequently
- Note any recent pricing changes that might affect the recommendation

**Step 4: Confirm patterns**
For architecture patterns and best practices:
- Check if the framework's official docs still recommend this approach
- Look for recent posts from core maintainers — they often signal upcoming changes
- Verify that code examples use current APIs, not deprecated ones

**Research output format in ADRs:**
```
### AD-001: Next.js App Router over Pages Router
- Context: Need SSR + API routes for a SaaS dashboard
- Researched: [date] — Next.js 15.x stable, App Router is now the default.
  Pages Router still supported but no new features.
- Options: Next.js (App Router), Remix, SvelteKit
- Decision: Next.js App Router
- Rationale: Largest ecosystem, team familiarity with React, server components
  reduce client bundle. Remix considered but smaller ecosystem for dashboard
  components. SvelteKit considered but team has no Svelte experience.
- Freshness risk: Next.js releases frequently. Pin to specific version in
  package.json and review upgrade path quarterly.
```

### Technology Selection Framework

Choose technology based on these criteria, in priority order:

1. **Team familiarity** - Technology the developer already knows
2. **Ecosystem maturity** - Well-documented, stable, active community
3. **Fit for scale needed** - Right-sized for the actual user count, not imaginary millions
4. **Deployment simplicity** - Can be deployed by one person without DevOps knowledge
5. **Current health** - Actively maintained, recent releases, responsive issue tracker (VERIFY — do not assume)

### Common Architecture Patterns by Product Type

| Product Type | Recommended Architecture | Why |
|-------------|------------------------|-----|
| SaaS web app | Monolith (Next.js/Rails/Django) + managed DB | Fast to build, easy to deploy |
| API/Platform | Monolith with good module boundaries | Can be split later if needed |
| Mobile app | React Native/Flutter + API backend | Single codebase, native feel |
| Internal tool | Low-code or simple CRUD framework | Speed over elegance |
| Static site + dynamic features | SSG + serverless functions | Low cost, low maintenance |

### Architecture Decision Records (ADRs)

Every significant technical choice gets documented as an ADR. "Significant" means: if reversed, it would require more than a day of work.

Good ADR:
```
### AD-001: PostgreSQL over MongoDB
- Context: Need a database for user data, projects, and tasks with relationships
- Options: PostgreSQL, MongoDB, SQLite
- Decision: PostgreSQL via Supabase
- Rationale: Relational data with clear schema, Supabase provides auth + realtime + hosting,
  team has SQL experience. MongoDB considered but data is clearly relational.
  SQLite considered but multi-user access and hosting options are limited.
- Consequences: Locked into SQL paradigm. Schema migrations needed for changes.
  Accepted because data model is well-understood.
```

Bad ADR:
```
### AD-001: Use PostgreSQL
- Decision: PostgreSQL
- Rationale: It's good
```

### Data Model Design

Start from use cases, not from tables:

1. List every noun in the use cases -> potential entities
2. List every verb -> potential relationships or actions
3. Draw relationships between entities
4. Add only the fields mentioned in requirements
5. Resist adding "might need later" fields

---

## Phase 4: Detail - Detailed

### Use Case Completeness Checklist

For each use case, verify:
- [ ] Happy path has clear start and end
- [ ] Each step specifies who acts (user or system)
- [ ] System responses include what the user sees
- [ ] At least 2 error paths are defined
- [ ] Edge cases specific to this use case are noted
- [ ] Preconditions are verifiable (not "user is motivated")
- [ ] Postconditions are testable (not "user is happy")

### Screen State Matrix

Build a state matrix for complex screens:

| State | Trigger | Display | User Can Do |
|-------|---------|---------|-------------|
| Default | Page load, data exists | List of items | Create, edit, delete, filter |
| Loading | Page load, fetching | Skeleton/spinner | Wait |
| Empty | Page load, no data | Empty state + CTA | Create first item |
| Error | API failure | Error message + retry | Retry, go back |
| Partial | Some items loaded | Partial list + loading more | Scroll, interact with loaded |

### API Design Rules

1. **One endpoint, one action** - Don't overload endpoints with query parameters that change behavior
2. **Consistent response shapes** - Every list endpoint returns `{ data: [], total: number, page: number }`
3. **Meaningful HTTP status codes** - 200 success, 201 created, 400 bad input, 401 not authenticated, 403 not authorized, 404 not found, 500 server error
4. **Validate at the boundary** - Every input field has type, format, and range validation
5. **Version from day one** - `/api/v1/...` costs nothing and prevents pain later

---

## Phase 5: Validate - Detailed

### Cross-Document Consistency Checks

After producing all documents, verify:

| Check | Between | What to verify |
|-------|---------|---------------|
| Feature coverage | PRD <-> Use Cases | Every FR maps to at least one UC |
| Screen coverage | User Flow <-> Use Cases | Every UC step has a screen |
| API coverage | API Spec <-> User Flow | Every screen action has an endpoint |
| Data coverage | Data Model <-> API Spec | Every API response field exists in the model |
| Scope alignment | All docs <-> Product Overview | Nothing exceeds defined MVP scope |

### The Developer Test

Read the complete documentation set and ask:
1. Can I set up the project from the tech stack section?
2. Can I create the database from the data model?
3. Can I build each screen from the flow + screen map?
4. Can I implement each API from the spec?
5. Can I verify my work against the acceptance criteria?

If any answer is "no" or "probably" or "I'd need to ask", the documentation has gaps. Fill them.

### The User Test

Read the complete documentation from a user's perspective:
1. Is there a clear moment where the product delivers value?
2. Is the time-to-value reasonable (< 5 minutes for most products)?
3. Are there dead-end states where the user gets stuck?
4. Does the onboarding flow make sense for someone who's never seen this product?

### Final Validation Checklist

- [ ] Every feature traces to a user problem
- [ ] No "nice to have" features in MVP
- [ ] No contradictions between documents
- [ ] All error states are handled
- [ ] Technical decisions have documented rationale
- [ ] Risks have mitigation strategies
- [ ] Timeline accounts for testing and deployment
- [ ] A developer can start coding from these documents alone
