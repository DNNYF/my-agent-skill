# Document Templates

Complete templates for all 8 product documents. Each template includes field descriptions and guidance for filling them in. Produce documents in this order - each builds on the previous one.

---

## 1. Product Overview

The executive summary. Written first because it forces clarity on fundamentals before diving into detail.

```markdown
# [Product Name] - Product Overview

## Summary
[2-3 sentences: what the product is, who it's for, what problem it solves.
Not a marketing pitch - a clear factual description.]

## Problem Statement
[Describe the problem users experience. Include context on why this matters
and why it matters now. Be specific - "developers waste 2 hours per sprint
manually syncing API docs" not "documentation is hard".]

## Proposed Solution
[High-level solution description. What the product does, not how it does it.
One paragraph maximum.]

## Target User
[Behavior-based persona. Not demographics ("Sarah, 28, Jakarta") but
behaviors and needs ("Solo developer building SaaS products who ships
fast but loses track of what they built").]

## Key Value Proposition
[Why a user would choose this over alternatives. What's the unfair advantage?
If there's no clear answer, the product idea needs more work.]

## MVP Scope

### Included
- [Feature 1]: [one sentence explaining what it does and why it's in MVP]
- [Feature 2]: [one sentence]
- [Feature 3]: [one sentence]

### Excluded (v1)
- [Item 1]: [why it's deferred - "not needed for core value" or "complex, validates separately"]
- [Item 2]: [why deferred]

## Success Metrics
- [Metric 1]: [measurable target, e.g. "50 active users within 2 weeks of launch"]
- [Metric 2]: [measurable target]
- [Metric 3]: [measurable target]
```

**Guidance:**
- If the user can't articulate the problem statement clearly, the idea isn't ready for architecture yet. Go back to Phase 1.
- Success metrics should be measurable and time-bound. "Users like it" is not a metric. "70% day-7 retention" is.
- MVP scope should be 3-5 features maximum. If it's more, scope is too wide.

---

## 2. User Persona & Use Cases

Connects the product to real human behavior. Use cases become the backbone for requirements, flows, and testing.

```markdown
# User Persona & Use Cases

## Personas

### [Persona Name - descriptive, not a human name]
- **Who**: [one sentence description]
- **Situation**: [when/where they need this product]
- **Frustration**: [what annoys them about current solutions]
- **Expectation**: [what they want to happen]
- **Tech Comfort**: [relevant tech skill level]

## Use Cases

### UC-001: [Use Case Name]
- **Actor**: [which persona]
- **Trigger**: [what initiates this use case]
- **Precondition**: [what must be true before starting]
- **Happy Path**:
  1. [Step 1 - user action]
  2. [Step 2 - system response]
  3. [Step 3 - user action]
  4. [Step 4 - system response / outcome]
- **Postcondition**: [what's true after completion]
- **Alternative Paths**:
  - Step [X] - [variation]: [what happens differently]
- **Error Paths**:
  - Step [X] fails ([reason]): [system response, user sees what]

### UC-002: [Use Case Name]
[same structure]
```

**Guidance:**
- Persona names should be role-descriptive: "The Solo Shipper", "The Team Lead", not "Sarah" or "John".
- Happy path steps alternate between user actions and system responses. If two user actions happen in a row, a system response is missing.
- Every use case must trace to at least one MVP feature. Orphan use cases indicate scope creep.
- Error paths are not optional. At minimum, cover: invalid input, network failure, auth failure.

---

## 3. User Flow & Screen Map

Translates use cases into visual navigation and screen structure.

```markdown
# User Flow & Screen Map

## Flow: [Flow Name - e.g., "First-Time Setup"]

[Step-by-step description of the flow]

### Screen: [Screen Name]
- **Purpose**: [why this screen exists - one sentence]
- **Key Elements**:
  - [Element 1]: [what it does]
  - [Element 2]: [what it does]
- **User Actions**:
  - [Action] -> navigates to [Next Screen]
  - [Action] -> triggers [effect/feedback]
- **States**:
  - Default: [what user sees on arrival]
  - Loading: [what user sees while waiting]
  - Empty: [what user sees when no data exists]
  - Error: [what user sees on failure]
  - Success: [what user sees on completion, if applicable]

## Screen Navigation Map

[Use simple ASCII or text-based notation]

Landing --> [Sign Up] --> [Dashboard]
                     \--> [Login] --> [Dashboard]

Dashboard --> [Create New] --> [Editor] --> [Preview] --> [Publish]
         \--> [Settings]
         \--> [History] --> [Detail View]
```

**Guidance:**
- Every screen must have all 4 base states defined (default, loading, empty, error). Skipping states causes bugs in implementation.
- The navigation map should fit on one screen. If it doesn't, the product has too many screens for MVP.
- Dead-end screens (no forward action) are usually a UX problem. Every screen should have a clear next step.

---

## 4. PRD (Product Requirements Document)

The contract between product thinking and engineering execution.

```markdown
# PRD: [Product Name]

**Version**: [x.x]
**Date**: [date]
**Status**: [Draft / Review / Approved]

## 1. Objective
[What this product achieves, from business/user perspective.
Not technical - "Enable developers to..." not "Build a REST API that..."]

## 2. Background & Context
[Why this is being built now. What triggered it.
Market context, user research findings, or pain points observed.]

## 3. Requirements

### 3.1 Functional Requirements

#### FR-001: [Requirement Name]
- **Priority**: [Must / Should / Could]
- **Description**: [What the system must be able to do]
- **Acceptance Criteria**:
  - [ ] [Criterion 1 - specific and testable]
  - [ ] [Criterion 2 - specific and testable]
  - [ ] [Criterion 3 - specific and testable]
- **Dependencies**: [other FRs that must be done first, if any]
- **Traces to**: [UC-XXX]

#### FR-002: [Requirement Name]
[same structure]

### 3.2 Non-Functional Requirements

#### NFR-001: [Requirement Name]
- **Category**: [Performance / Security / Usability / Reliability / Accessibility]
- **Description**: [specific and measurable requirement]
- **Target**: [concrete number when possible]
- **Measurement**: [how to verify this is met]

## 4. Out of Scope
[Explicit list of what is NOT included. Important for preventing scope creep.]
- [Item 1]: [why excluded]
- [Item 2]: [why excluded]

## 5. Assumptions
[Things assumed to be true that, if wrong, would change the plan]
- [Assumption 1]
- [Assumption 2]

## 6. Risks & Mitigations
| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| [Risk 1] | [H/M/L] | [H/M/L] | [Strategy] |
| [Risk 2] | [H/M/L] | [H/M/L] | [Strategy] |

## 7. Timeline
| Phase | Deliverable | Estimate | Dependencies |
|-------|-------------|----------|-------------|
| [Phase 1] | [Output] | [Duration] | [None / Phase X] |
| [Phase 2] | [Output] | [Duration] | [Phase 1] |
```

**Guidance:**
- Every FR must have acceptance criteria. "User can log in" is not a requirement. "User can log in with email/password and receives a session token valid for 24 hours" is.
- Priority uses MoSCoW: Must (product doesn't work without it), Should (important but workaround exists), Could (nice to have).
- "Traces to" links every requirement back to a use case. Requirements without a use case trace are likely scope creep.
- Non-functional requirements need concrete targets. "System should be fast" is meaningless. "Page load under 2 seconds on 3G" is testable.

---

## 5. Technical Architecture

Translates product requirements into system design.

```markdown
# Technical Architecture: [Product Name]

## 1. Architecture Overview
[High-level description. What approach is used and why.
"Monolithic Next.js application with Supabase backend. Chosen for
speed of development and reduced operational complexity for a solo developer."]

## 2. Tech Stack
| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Frontend | [tech] | [why this over alternatives] |
| Backend | [tech] | [why] |
| Database | [tech] | [why] |
| Auth | [tech] | [why] |
| Hosting | [tech] | [why] |
| File Storage | [tech] | [why, if applicable] |

## 3. System Components

### [Component 1 - e.g., "Web Application"]
- **Responsibility**: [what it does]
- **Input**: [what it receives]
- **Output**: [what it produces]
- **Communicates with**: [other components]

### [Component 2 - e.g., "API Layer"]
[same structure]

## 4. Architecture Decisions

### AD-001: [Decision - e.g., "REST over GraphQL"]
- **Context**: [situation requiring a decision]
- **Options considered**: [alternatives evaluated]
- **Decision**: [what was chosen]
- **Rationale**: [why - connect to project constraints]
- **Consequences**: [trade-offs accepted]

### AD-002: [Decision]
[same structure]

## 5. Security Considerations
- Authentication: [approach]
- Authorization: [approach]
- Data protection: [approach]
- Input validation: [approach]

## 6. Infrastructure & Deployment
- Environment strategy: [dev/staging/prod or simpler]
- Deployment method: [how code gets to production]
- Monitoring: [what's tracked, if anything for v1]

## 7. Scalability Notes
[Which parts will need attention at scale, and at what threshold.
For MVP, this section can be brief - acknowledge limits without over-designing.]
```

**Guidance:**
- The rationale column in Tech Stack is mandatory. "Because it's popular" is not a rationale. "Because the developer already knows it, reducing learning overhead for a solo project" is.
- Architecture Decisions (ADs) document the "why" behind choices. Future developers (including the same person 3 months later) will thank you.
- For MVP/solo projects, security section can be minimal but must exist. At minimum: auth mechanism, input sanitization, HTTPS.
- Scalability Notes should be honest about limits. "This architecture handles ~1000 concurrent users. Beyond that, [specific component] needs [specific change]."

---

## 6. Data Model

The structural foundation. Gets this wrong and everything built on top is unstable.

```markdown
# Data Model: [Product Name]

## Entities

### [Entity Name - e.g., "User"]
| Field | Type | Constraints | Notes |
|-------|------|-------------|-------|
| id | UUID | PK | Auto-generated |
| email | VARCHAR(255) | UNIQUE, NOT NULL | Login identifier |
| [field] | [type] | [constraints] | [notes] |
| created_at | TIMESTAMP | NOT NULL | Auto-generated |
| updated_at | TIMESTAMP | NOT NULL | Auto-updated |

### [Entity Name - e.g., "Project"]
[same structure]

## Relationships
- User --[1:N]--> Project: [a user owns many projects]
- Project --[1:N]--> Task: [a project contains many tasks]
- User --[M:N]--> Project via ProjectMember: [users can collaborate on projects]

## Indexes
- `idx_project_user_id` on Project(user_id): [speeds up "my projects" query]
- `idx_task_status` on Task(project_id, status): [speeds up filtered task lists]

## Data Flow
[How data moves from user input through the system to storage and back.
Describe the primary read and write paths.]

### Write Path (Creating a Task)
1. User submits form on frontend
2. Frontend sends POST to /api/tasks
3. Backend validates input
4. Backend writes to database
5. Backend returns created task
6. Frontend updates UI optimistically / on response

### Read Path (Loading Task List)
1. User navigates to project page
2. Frontend sends GET to /api/projects/:id/tasks
3. Backend queries database with pagination
4. Backend returns task array
5. Frontend renders list
```

**Guidance:**
- Every entity needs `id`, `created_at`, `updated_at` at minimum.
- Soft delete (`deleted_at`) is almost always preferable to hard delete for user-facing data.
- Define indexes based on actual query patterns from use cases, not speculation.
- Data flow section prevents the "where does this data come from?" question during implementation.

---

## 7. API Specification

The contract between frontend and backend. Must be precise enough to implement without clarification.

```markdown
# API Specification: [Product Name]

## Overview
- **Base URL**: `https://api.example.com/v1` (or `/api` for monolith)
- **Auth**: [Bearer token / Session cookie / API key]
- **Content-Type**: `application/json`
- **Error Format**:
  ```json
  {
    "error": {
      "code": "VALIDATION_ERROR",
      "message": "Human-readable description",
      "details": {}
    }
  }
  ```

## Endpoints

### Auth

#### POST /auth/register
- **Description**: Create a new user account
- **Auth**: None
- **Request Body**:
  ```json
  {
    "email": "string (required, valid email)",
    "password": "string (required, min 8 chars)"
  }
  ```
- **Response 201**:
  ```json
  {
    "user": {
      "id": "uuid",
      "email": "string"
    },
    "token": "string (JWT)"
  }
  ```
- **Errors**:
  - `400 VALIDATION_ERROR`: Invalid email or weak password
  - `409 CONFLICT`: Email already registered

### [Resource Group]

#### [METHOD] [path]
- **Description**: [what it does]
- **Auth**: [Required / Optional / None]
- **Path Parameters**: [if any]
- **Query Parameters**: [if any, with defaults]
- **Request Body**:
  ```json
  {
    "field": "type (required/optional, validation rules)"
  }
  ```
- **Response [status]**:
  ```json
  {
    "field": "type (description)"
  }
  ```
- **Errors**:
  - `[status] [CODE]`: [when this happens]
```

**Guidance:**
- Every field must specify: type, required/optional, and validation rules.
- List all possible error responses, not just 200. Frontend developers need to know what can go wrong.
- Use consistent naming: camelCase or snake_case, pick one and stick with it.
- Pagination format should be defined once and referenced. Don't reinvent per endpoint.

---

## 8. Implementation Roadmap

Translates the full specification into buildable phases with clear milestones.

```markdown
# Implementation Roadmap: [Product Name]

## Development Approach
[Strategy - e.g., "Vertical slices: each phase delivers a usable feature
end-to-end rather than building all backend first, then all frontend."]

## Phase 1: Foundation ([time estimate])

### Milestone: [concrete definition of "done"]
[e.g., "User can register, log in, and see an empty dashboard"]

### Tasks
- [ ] Project setup (repo, tooling, CI) - [estimate]
- [ ] Database schema + migrations - [estimate]
- [ ] Auth flow (register/login/logout) - [estimate]
- [ ] Base layout + navigation shell - [estimate]

### Validates
[How to verify this phase is complete]
- User can create account with email
- User can log in and see dashboard
- User can log out

## Phase 2: Core Feature ([time estimate])

### Milestone: [definition of "done"]
### Tasks
- [ ] [Task 1] - [estimate]
- [ ] [Task 2] - [estimate]

### Validates
[verification steps]

## Phase 3: Polish & Ship ([time estimate])

### Milestone: [definition of "done"]
### Tasks
- [ ] Error handling + edge cases - [estimate]
- [ ] Loading states + empty states - [estimate]
- [ ] Basic responsive design - [estimate]
- [ ] Deploy to production - [estimate]

### Validates
[verification steps]

## Phase Dependencies
Phase 1 -> Phase 2: [auth required before features]
Phase 2a || Phase 2b: [can be built in parallel because no shared state]

## Accepted Technical Debt
- [Item 1]: [why accepted, when to address it]
- [Item 2]: [why accepted, trigger for addressing it]

## Post-MVP Backlog
- [Feature 1]: [brief description, estimated complexity]
- [Feature 2]: [brief description]
```

**Guidance:**
- Each phase must produce something testable. "Set up infrastructure" alone is not a phase - add at least one user-facing behavior.
- Time estimates are ranges, not points. "2-4 days" is more honest than "3 days".
- If estimate confidence is low, say "needs spike first" instead of guessing.
- Technical debt section is mandatory. Acknowledging shortcuts makes them intentional decisions, not accidents.
- Post-MVP backlog captures ideas that were scoped out. This prevents losing good ideas while keeping MVP focused.


---

## Enterprise Extensions

**When to include**: Only when Enterprise mode is active (Phase 0). Only include sections relevant to the user's specific compliance/governance/operational answers.

**Integration rule**: These sections are inserted INTO the base templates above, not appended as a separate document. They should feel like natural parts of the document.

---

### Extension A: PRD — Compliance & Data Governance

Insert after section 3.2 (Non-Functional Requirements) in the PRD template:

```markdown
### 3.3 Compliance Requirements

#### CR-001: [Requirement Name — e.g., "GDPR Right to Erasure"]
- **Regulation**: [GDPR Article 17 / SOC2 CC6.1 / HIPAA §164.312 / etc.]
- **Obligation**: [What the regulation requires, in plain language]
- **System Impact**: [How this affects architecture — e.g., "All user PII must be deletable within 30 days, including backups and audit logs older than retention period"]
- **Acceptance Criteria**:
  - [ ] [Testable criterion — e.g., "DELETE /api/users/:id removes all PII from primary DB within 24h"]
  - [ ] [Testable criterion — e.g., "Backup purge job removes PII from backups within 30 days"]
- **Verification Method**: [How compliance is proven — automated test, manual audit, third-party assessment]
- **Owner**: [Team/role responsible for maintaining compliance]

#### CR-002: [Next requirement]
[same structure]

### 3.4 Data Governance

#### Data Classification
| Entity | Classification | Justification | Handling |
|--------|---------------|---------------|----------|
| User.email | Confidential (PII) | Personally identifiable | Encrypted at rest, access logged |
| User.health_record | Restricted (PHI) | HIPAA-protected | Encrypted, BAA-compliant storage, minimum necessary access |
| Invoice.amount | Internal | Business-sensitive | Access control, no public exposure |
| Product.description | Public | Marketing content | No special handling |

#### Data Retention Policy
| Data Type | Retention Period | Justification | Deletion Method |
|-----------|----------------|---------------|-----------------|
| User PII | Account lifetime + 30 days | GDPR requirement | Hard delete + backup purge |
| Audit logs | 6 years | SOC2 / HIPAA requirement | Automated archival then deletion |
| Transaction records | 7 years | Tax/financial regulation | Archive to cold storage |
| Session data | 24 hours | No business need beyond session | Auto-expire |

### 3.5 Governance & Approval Workflows

| Action | Requires Approval From | SLA | Automation |
|--------|----------------------|-----|-----------|
| [Action — e.g., "Bulk data export"] | [Role — e.g., "Data Protection Officer"] | [Time — e.g., "72h"] | [How — e.g., "Slack notification + approval button"] |
| [Action — e.g., "New third-party integration"] | [Role — e.g., "Security Lead + Compliance"] | [Time] | [How] |
| [Action — e.g., "Production schema migration"] | [Role — e.g., "Tech Lead + DBA"] | [Time] | [How — e.g., "PR approval gate in CI/CD"] |
```

**Guidance:**
- Every compliance requirement must trace to a specific regulation clause, not just "GDPR" generically.
- Data classification drives architecture decisions. If you classify something as Restricted but store it in an unencrypted field, that's a contradiction.
- Approval workflows should be automatable. If it requires a meeting for every approval, it won't scale.

---

### Extension B: Technical Architecture — Security & Observability

Insert after section 5 (Security Considerations) in the Technical Architecture template:

```markdown
## 5b. Security Architecture

### Threat Model (STRIDE)

#### Flow: [Critical Flow — e.g., "User Authentication"]
| Threat | Attack Vector | Likelihood | Impact | Mitigation |
|--------|--------------|-----------|--------|-----------|
| Spoofing | Stolen credentials, session hijacking | Medium | High | MFA, short-lived sessions, device fingerprinting |
| Tampering | Modified JWT claims | Low | Critical | Signed tokens, server-side validation |
| Repudiation | User denies action | Medium | Medium | Immutable audit log with timestamp + IP |
| Info Disclosure | SQL injection, verbose errors | Medium | High | Parameterized queries, generic error messages |
| DoS | Brute force login | High | Medium | Rate limiting (10 attempts/min), CAPTCHA after 5 |
| Elevation | Role manipulation | Low | Critical | Server-side role check on every request, no client-side role storage |

#### Flow: [Next Critical Flow]
[same structure]

### Identity & Access Management

| Aspect | Design Decision | Rationale |
|--------|----------------|-----------|
| Identity Provider | [e.g., Auth0 / Okta / self-hosted Keycloak] | [why — e.g., "SOC2-compliant, supports SAML for enterprise SSO"] |
| Authorization Model | [RBAC / ABAC / ReBAC] | [why — e.g., "RBAC sufficient for 3 roles, ABAC overkill at this scale"] |
| Session Management | [JWT / session cookie / opaque token] | [why — trade-offs] |
| Service-to-Service Auth | [mutual TLS / API key / OAuth client credentials] | [why] |
| Secrets Management | [Vault / AWS Secrets Manager / env vars] | [why] |

### Network Security
- **Perimeter**: [WAF, DDoS protection — e.g., Cloudflare, AWS Shield]
- **Segmentation**: [VPC layout, subnet strategy, security groups]
- **Internal**: [Service mesh / mutual TLS / network policies]
- **Egress**: [What can reach the internet, what can't]

## 6b. Observability & Operational Readiness

### SLO Definitions
| Service | SLI | SLO | Error Budget (monthly) |
|---------|-----|-----|----------------------|
| [API] | Availability (2xx+3xx / total) | 99.9% | 43 minutes downtime |
| [API] | Latency p99 | < 500ms | 0.1% requests above threshold |
| [Worker] | Job completion rate | 99.5% | 0.5% failed jobs |
| [Frontend] | Core Web Vitals (LCP) | < 2.5s | — |

### Monitoring Stack
| Layer | Tool | What It Covers |
|-------|------|---------------|
| Infrastructure | [e.g., CloudWatch / Datadog] | CPU, memory, disk, network |
| Application | [e.g., OpenTelemetry + Grafana] | Traces, metrics, logs |
| Business | [e.g., Mixpanel / custom dashboard] | User actions, revenue, conversion |
| Alerting | [e.g., PagerDuty / Opsgenie] | On-call routing, escalation |

### Deployment Strategy
- **Method**: [Blue-green / Canary / Rolling]
- **Rollback**: [Automated on error rate spike / manual / both]
- **Database migrations**: [Forward-only with backward compat / reversible]
- **Feature flags**: [Tool — e.g., LaunchDarkly / Unleash / env vars]

### Incident Response
| Severity | Definition | Response Time | Escalation |
|----------|-----------|---------------|-----------|
| P1 — Critical | Service down, data loss risk | 15 min | Immediate page to on-call + engineering lead |
| P2 — Major | Degraded performance, partial outage | 1 hour | Page on-call |
| P3 — Minor | Non-critical feature broken | Next business day | Ticket |
```

**Guidance:**
- STRIDE analysis is mandatory for any flow handling Confidential or Restricted data. For Internal/Public data flows, a lighter risk assessment suffices.
- SLOs must be defined BEFORE launch. Retrofitting SLOs after an outage is reactive, not engineering.
- Deployment strategy must include rollback. "We'll figure it out" is not a rollback plan.

---

### Extension C: Implementation Roadmap — Operational Readiness Gates

Add to each phase in the Implementation Roadmap:

```markdown
### Operational Readiness Gate — Phase [N]

Before proceeding to Phase [N+1], verify:
- [ ] Health check endpoint responds correctly
- [ ] Monitoring covers all new components (no blind spots)
- [ ] Alerts configured with documented runbooks
- [ ] Rollback tested and documented for this phase's changes
- [ ] Security review completed for new data flows (if any)
- [ ] Compliance checklist passed for new data handling (if any)
- [ ] Load tested at 2x expected traffic for this phase
- [ ] On-call rotation updated to cover new components
- [ ] Architecture documentation updated
```

Add at the end of the Implementation Roadmap:

```markdown
## Operational Maturity Roadmap

Beyond feature delivery, the system must mature operationally:

| Milestone | Target | Criteria | Owner |
|-----------|--------|----------|-------|
| **L1: Basic** | Launch day | Health checks, error alerting, manual deployment, basic logging | Engineering |
| **L2: Observable** | Launch + 30d | Distributed tracing, custom dashboards, structured logging, automated deployment | Engineering + DevOps |
| **L3: Resilient** | Launch + 60d | Runbooks for all P1/P2, on-call rotation, automated rollback, dependency circuit breakers | DevOps + Engineering |
| **L4: Proactive** | Launch + 90d | Chaos engineering, load testing in CI, SLO-based alerting, capacity planning | Platform team |
| **L5: Compliant** | Launch + 120d | All audit controls documented, evidence collection automated, pen-test completed, compliance audit ready | Security + Compliance |

### Post-Launch Review Schedule
| Review | Frequency | Participants | Output |
|--------|-----------|-------------|--------|
| Architecture review | Quarterly | Tech leads | ADR updates, tech debt prioritization |
| Security review | Bi-annually | Security + Engineering | Threat model update, vulnerability assessment |
| Compliance review | Annually | Compliance + Legal + Engineering | Audit readiness report |
| Capacity review | Monthly | DevOps + Engineering | Scaling plan, cost optimization |
```

**Guidance:**
- Operational readiness gates are blocking — don't skip them to ship faster. Shipping without monitoring is shipping blind.
- The maturity roadmap is a target, not a deadline. Adjust timelines to team size and product criticality.
- Not every product needs L5. A B2B SaaS with 10 customers might stop at L3. A healthcare platform must reach L5 before handling real patient data.
