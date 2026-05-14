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
