# Enterprise Framework

Enterprise-level guidance activated when the user selects "Enterprise" in Phase 0. Only inject sections relevant to the user's specific answers — not all enterprises need everything.

**Core rule**: Enterprise complexity is additive, not replacement. The base 5-phase workflow stays intact. Enterprise mode adds depth where the stakes demand it.

---

## 1. Governance & Compliance

### Data Classification Matrix

Before designing any data architecture, classify all data the system handles:

| Classification | Definition | Examples | Handling Requirements |
|---------------|-----------|----------|----------------------|
| **Public** | No restriction | Marketing content, public API docs | No special handling |
| **Internal** | Company-only | Internal metrics, employee names | Access control, no public exposure |
| **Confidential** | Business-sensitive | Revenue data, contracts, PII | Encryption at rest + transit, access logging, retention policy |
| **Restricted** | Regulatory-controlled | Health records (HIPAA), payment data (PCI), biometric | Full audit trail, encryption, access approval, data residency, breach notification |

**In architecture docs**: Every data entity must be tagged with its classification. This determines storage, access, encryption, and retention decisions.

### Compliance Framework Quick Reference

#### GDPR (EU/EEA data subjects)
- **Triggers**: Any EU user data, even if company is outside EU
- **Architecture impact**:
  - Right to erasure → soft delete + purge pipeline
  - Data portability → export API (JSON/CSV)
  - Consent management → consent service + audit log
  - Data minimization → collect only what's needed, document why
  - Privacy by design → default to most restrictive access
- **Required components**: Consent management, data export, erasure pipeline, DPA (Data Processing Agreement) with sub-processors, privacy impact assessment

#### SOC 2 (Type I & II)
- **Triggers**: B2B SaaS serving US companies, enterprise sales
- **Architecture impact**:
  - Audit logging → immutable log of all data access and changes
  - Access control → RBAC with principle of least privilege
  - Change management → all changes through CI/CD with approval
  - Monitoring → alerting on anomalous access patterns
  - Encryption → at rest and in transit, key management
- **Required components**: Centralized audit log, RBAC system, CI/CD with approval gates, monitoring + alerting, incident response process, vendor management

#### HIPAA (US healthcare data)
- **Triggers**: Any PHI (Protected Health Information) — patient names, diagnoses, treatment, billing
- **Architecture impact**:
  - BAA (Business Associate Agreement) required with all vendors
  - Minimum necessary access → role-based, need-to-know
  - Audit trail → who accessed what PHI, when, why
  - Encryption → AES-256 at rest, TLS 1.2+ in transit
  - Data residency → US-only for PHI
- **Required components**: BAA-compliant infrastructure (AWS/GCP/Azure with BAA), access audit system, encryption key management, breach notification system (72-hour rule)

#### PCI-DSS (Payment card data)
- **Triggers**: Storing, processing, or transmitting cardholder data
- **Architecture impact**:
  - Network segmentation → CDE (Cardholder Data Environment) isolated
  - Tokenization → never store raw card numbers, use payment processor tokens
  - Logging → all access to CDE logged and monitored
  - Vulnerability management → regular scans, patching SLA
- **Best practice**: Use Stripe/Adyen/similar to avoid handling card data directly. Reduces PCI scope to SAQ-A (minimal).

#### Indonesian Regulations
- **PP 71/2019 (GR 71)**: Electronic system operators must register with Kominfo, store certain data locally
- **OJK regulations**: Fintech/lending platforms need OJK licensing, specific data handling rules
- **PBI (Bank Indonesia)**: E-money, payment systems, QRIS compliance
- **UU PDP (Personal Data Protection Law)**: Indonesia's GDPR equivalent — consent, data subject rights, breach notification

### Audit Trail Architecture

For any system requiring compliance, implement immutable audit logging:

```
Audit Log Entry:
- timestamp: ISO 8601 with timezone
- actor: user_id + role + IP + session_id
- action: CRUD operation + specific method
- resource: entity type + entity ID
- before_state: snapshot before change (for updates/deletes)
- after_state: snapshot after change (for creates/updates)
- reason: business justification (if required by policy)
- compliance_tags: [GDPR, HIPAA, PCI] — which regulations this event is relevant to
```

**Storage**: Append-only store (not the application database). Options: separate database with no DELETE permissions, cloud audit services (AWS CloudTrail, GCP Audit Logs), or dedicated audit SaaS.

**Retention**: Define per compliance requirement. HIPAA = 6 years. SOC2 = varies. GDPR = as long as necessary for purpose.

### Approval Workflows

Enterprise products often need human approval gates:

| Action | Approver | SLA | Escalation |
|--------|----------|-----|-----------|
| New user provisioning | Team lead | 24h | Auto-approve after SLA |
| Role elevation | Security team | 48h | Escalate to CISO |
| Data export (bulk) | Data owner + compliance | 72h | Block until approved |
| Production deployment | Tech lead + QA | 4h | Escalate to engineering manager |
| Data deletion request | Compliance officer | 30 days (GDPR max) | Legal escalation |

**Architecture**: Implement as a state machine with notification hooks. Don't build custom — use existing workflow engines or simple queue + state table.

---

## 2. Multi-Team Coordination

### RACI Matrix Template

For enterprise products with multiple teams:

| Decision/Activity | Product | Engineering | Security | Compliance | DevOps | QA |
|------------------|---------|-------------|----------|------------|--------|-----|
| Feature requirements | **R/A** | C | I | C | I | I |
| Architecture decisions | C | **R/A** | C | I | C | I |
| Security review | I | C | **R/A** | C | C | I |
| Deployment approval | I | C | C | I | **R/A** | C |
| Data model changes | C | **R/A** | C | C | I | C |
| Incident response | I | C | C | I | **R/A** | I |

R = Responsible, A = Accountable, C = Consulted, I = Informed

### Service Boundaries & Team Ownership

When multiple teams build and maintain parts of the system:

```
Service: [name]
Owner: [team name]
On-call: [rotation or team]
SLA to consumers: [response time, uptime]
API contract: [link to spec]
Change notification: [how consumers are notified of breaking changes]
Dependencies: [services this depends on]
Consumers: [services that depend on this]
```

### Integration Contracts

Between teams/services, define explicit contracts:

```markdown
## Contract: [Service A] → [Service B]

### Interface
- Protocol: REST / gRPC / Event (Kafka/SQS)
- Endpoint: [URL or topic]
- Auth: [service-to-service auth method]

### SLA
- Availability: 99.9%
- Latency p99: < 200ms
- Throughput: 1000 RPS

### Schema
- Format: JSON / Protobuf
- Versioning: URL path (/v1, /v2) or header
- Breaking change policy: 90-day deprecation notice, dual-run period

### Failure Handling
- Circuit breaker: [threshold]
- Retry policy: [max retries, backoff]
- Fallback: [degraded behavior when dependency is down]
```

### Communication Cadence

| Meeting | Frequency | Attendees | Purpose |
|---------|-----------|-----------|---------|
| Architecture review | Bi-weekly | Tech leads all teams | Cross-cutting decisions, dependency alignment |
| API contract review | Per change | Affected teams | Breaking change assessment |
| Security review | Per feature | Engineering + Security | Threat assessment for new features |
| Incident review | Post-incident | All affected | Root cause, prevention |

---

## 3. Security Architecture

### STRIDE Threat Modeling

For each major component/flow, assess:

| Threat | Question | Mitigation Pattern |
|--------|----------|-------------------|
| **S**poofing | Can someone pretend to be another user/service? | Strong auth, mutual TLS for service-to-service |
| **T**ampering | Can data be modified in transit or at rest? | Integrity checks, signed payloads, immutable audit log |
| **R**epudiation | Can someone deny performing an action? | Audit logging, non-repudiation signatures |
| **I**nformation Disclosure | Can unauthorized parties access data? | Encryption, access control, data classification |
| **D**enial of Service | Can the system be made unavailable? | Rate limiting, auto-scaling, circuit breakers |
| **E**levation of Privilege | Can someone gain unauthorized access levels? | Least privilege, role validation at every layer |

**When to apply**: Every new feature that handles Confidential or Restricted data, every new external-facing endpoint, every new service-to-service communication path.

### Zero-Trust Architecture Principles

For enterprise systems, assume the network is hostile:

1. **Never trust, always verify** — Every request authenticated and authorized, even internal
2. **Least privilege access** — Minimum permissions needed, time-bounded when possible
3. **Assume breach** — Design so that compromise of one component doesn't cascade
4. **Explicit verification** — Identity, device health, context all verified per request

**Implementation patterns**:
- Service mesh (Istio/Linkerd) for mutual TLS between services
- Short-lived tokens (< 1 hour) over long-lived API keys
- Network segmentation — services can only reach what they need
- Identity-aware proxy for internal tools (BeyondCorp model)

### IAM Design

```markdown
## Identity & Access Management

### Identity Providers
- Internal users: [SSO provider — Okta, Azure AD, Google Workspace]
- External users: [auth method — OAuth, SAML, magic link]
- Service accounts: [how services authenticate to each other]

### Authorization Model
- Pattern: [RBAC / ABAC / ReBAC]
- Roles: [list with permissions]
- Permission granularity: [resource-level, field-level, row-level]

### Access Lifecycle
- Provisioning: [how access is granted — manual, SCIM, JIT]
- Review: [periodic access review cadence]
- Deprovisioning: [how access is revoked — immediate on offboarding]

### Privileged Access
- Admin access: [how, when, by whom — break-glass procedure]
- Production access: [who can access prod data, under what conditions]
- Audit: [all privileged access logged and reviewed]
```

### Security Review Gates

| Gate | When | Blocker? | Reviewer |
|------|------|----------|----------|
| Design review | Before implementation | Yes | Security team |
| Dependency scan | Every PR | Yes (critical/high) | Automated + security |
| SAST (static analysis) | Every PR | Yes (critical) | Automated |
| DAST (dynamic testing) | Pre-release | Yes (critical) | Security team |
| Penetration test | Quarterly or major release | Advisory | External vendor |
| Compliance audit | Annually | Yes | External auditor |

---

## 4. Observability & Operational Readiness

### SLO/SLI Definition

Define before launch, not after the first outage:

```markdown
## Service Level Objectives

### [Service/Feature Name]

| SLI (Indicator) | SLO (Objective) | Measurement | Alert Threshold |
|-----------------|-----------------|-------------|-----------------|
| Availability | 99.9% (8.7h downtime/year) | Successful responses / total requests | < 99.5% over 5min |
| Latency (p50) | < 100ms | Server-side response time | > 200ms over 5min |
| Latency (p99) | < 500ms | Server-side response time | > 1000ms over 5min |
| Error rate | < 0.1% | 5xx responses / total | > 1% over 5min |
| Data freshness | < 30s | Time since last sync | > 60s |

### Error Budget
- Monthly budget: [calculated from SLO]
- When exhausted: freeze non-critical deployments, focus on reliability
- Reset: monthly
```

### Monitoring Strategy

| Layer | What to Monitor | Tool Category | Alert On |
|-------|----------------|---------------|----------|
| Infrastructure | CPU, memory, disk, network | APM / Cloud metrics | Threshold breach |
| Application | Request rate, error rate, latency | APM / Custom metrics | SLO breach |
| Business | Signups, transactions, revenue | Analytics / Custom | Anomaly detection |
| Security | Auth failures, privilege escalation, data access | SIEM | Pattern match |
| Dependencies | Third-party API health, latency | Synthetic monitoring | Degradation |

### Runbook Template

For each critical alert:

```markdown
## Runbook: [Alert Name]

### What This Means
[Plain language: what's happening, what's the user impact]

### Severity
[P1/P2/P3 — who gets paged, response time expectation]

### Diagnosis Steps
1. Check [specific dashboard/log]
2. Look for [specific pattern]
3. Verify [specific component]

### Resolution Steps
1. [Step 1 — specific command or action]
2. [Step 2]
3. [Step 3]

### Escalation
- If not resolved in [time]: escalate to [team/person]
- If data loss suspected: invoke incident response

### Post-Resolution
- [ ] Verify service recovered
- [ ] Check for data inconsistency
- [ ] Update incident log
- [ ] Schedule post-mortem if P1/P2
```

### Deployment & Rollback Strategy

```markdown
## Deployment Strategy

### Approach: [Blue-Green / Canary / Rolling]
- Rationale: [why this approach for this system]

### Pre-Deployment Checklist
- [ ] All tests passing (unit, integration, e2e)
- [ ] Security scan clean
- [ ] Database migration tested on staging
- [ ] Rollback plan verified
- [ ] On-call engineer identified

### Canary Process (if applicable)
1. Deploy to 5% of traffic
2. Monitor for 15 minutes: error rate, latency, business metrics
3. If healthy: expand to 25% → 50% → 100%
4. If unhealthy: automatic rollback

### Rollback Procedure
1. [Specific rollback command/process]
2. Verify rollback successful
3. Notify stakeholders
4. Investigate root cause

### Database Migration Rollback
- Forward-only migrations: [how to handle if rollback needed]
- Backward-compatible migrations: [dual-write period, cleanup]
```

---

## 5. Migration & Legacy Integration

### Brownfield Assessment

Before designing integration with existing systems:

```markdown
## Legacy System Assessment

### System: [Name]
- **Age**: [years in production]
- **Technology**: [stack, version]
- **Data volume**: [records, storage size]
- **Active users**: [count]
- **Owner/maintainer**: [team, knowledge level]
- **Documentation state**: [good/partial/none]
- **API surface**: [REST/SOAP/DB-direct/file-based]
- **Change frequency**: [active development / maintenance only / frozen]
- **Known issues**: [tech debt, performance, security]

### Integration Constraints
- Can we modify the legacy system? [yes/no/limited]
- Data format: [structured/semi/unstructured]
- Real-time or batch? [latency requirements]
- Data ownership: [who is source of truth during migration]
```

### Integration Patterns

| Pattern | When to Use | Complexity | Risk |
|---------|------------|-----------|------|
| **Strangler Fig** | Gradually replacing legacy | Medium | Low — incremental |
| **Anti-Corruption Layer (ACL)** | Legacy has bad data model | Medium | Low — isolates mess |
| **Event Bridge** | Systems need to stay in sync | High | Medium — eventual consistency |
| **Database View/Materialized View** | Read-only access to legacy data | Low | Low — no writes |
| **Dual Write** | Transition period, both systems active | High | High — consistency risk |
| **ETL/Batch Sync** | Non-real-time data needs | Low | Low — but stale data |

### Data Migration Strategy

```markdown
## Migration Plan: [Source] → [Target]

### Approach: [Big Bang / Phased / Parallel Run]
- Rationale: [why this approach]

### Data Mapping
| Source Field | Target Field | Transformation | Notes |
|-------------|-------------|----------------|-------|
| [field] | [field] | [none/convert/derive] | [edge cases] |

### Migration Phases
1. **Schema setup** — Create target schema, indexes
2. **Historical data** — Bulk migrate existing records
3. **Delta sync** — Catch up changes during migration window
4. **Cutover** — Switch traffic to new system
5. **Validation** — Verify data integrity (row counts, checksums, spot checks)
6. **Cleanup** — Decommission old system (after bake period)

### Rollback Plan
- Point of no return: [when rollback becomes impossible/impractical]
- Before PONR: [how to roll back]
- After PONR: [forward-fix strategy]

### Validation Criteria
- [ ] Row count matches (within tolerance: [X]%)
- [ ] Checksum validation on critical fields
- [ ] Business logic spot checks ([specific scenarios])
- [ ] Performance within SLO on new system
- [ ] No data loss for records created during migration window
```

---

## 6. Document Versioning

### Architecture Document Versioning

Enterprise architecture docs are living documents. Track changes:

```markdown
## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | [date] | [name] | Initial architecture |
| 1.1 | [date] | [name] | Added caching layer (AD-005) |
| 2.0 | [date] | [name] | Migration to microservices (AD-012) |

## Review Schedule
- Architecture review: quarterly
- Security review: bi-annually or after major changes
- Compliance review: annually or after regulation changes
```

### API Versioning Strategy

| Strategy | When | Example |
|----------|------|---------|
| URL path | Public APIs, clear major versions | `/api/v1/users`, `/api/v2/users` |
| Header | Internal APIs, fine-grained control | `API-Version: 2024-01-15` |
| Query param | Simple APIs, backward compat | `/api/users?version=2` |

**Breaking change policy**:
1. Announce deprecation (minimum 90 days for external APIs)
2. Dual-run period (old + new both active)
3. Migration guide published
4. Sunset old version (with monitoring for stragglers)

---

## 7. Enterprise Extensions to Core Documents

When enterprise mode is active, these sections are added to the base document templates.

### PRD Extensions

Add after section 3.2 (Non-Functional Requirements):

```markdown
### 3.3 Compliance & Data Governance Requirements

#### CR-001: [Compliance Requirement]
- **Regulation**: [GDPR / SOC2 / HIPAA / PCI-DSS / local]
- **Requirement**: [specific obligation]
- **Implementation**: [how the system satisfies this]
- **Verification**: [how compliance is tested/audited]
- **Owner**: [team/role responsible]

### 3.4 Governance & Approval Requirements

#### GR-001: [Governance Requirement]
- **Process**: [what requires approval]
- **Approver**: [role/team]
- **SLA**: [time to approve]
- **Escalation**: [what happens if SLA breached]
```

### Technical Architecture Extensions

Add after section 5 (Security Considerations):

```markdown
## 5b. Security Architecture (Enterprise)

### Threat Model
[STRIDE analysis for top 3-5 critical flows]

### Identity & Access Management
[IAM design — providers, authorization model, lifecycle]

### Network Security
[Segmentation, firewall rules, service mesh, WAF]

### Data Protection
[Encryption strategy per data classification, key management, tokenization]

## 6b. Observability & Operations

### SLO/SLI Definitions
[Per-service objectives with measurement approach]

### Monitoring Architecture
[What's monitored, tools, alert routing]

### Incident Response
[Severity levels, response times, escalation paths]

### Deployment Strategy
[Blue-green/canary/rolling, rollback procedure]

## 7b. Migration & Integration Architecture (if applicable)

### Legacy System Landscape
[Assessment of systems to integrate with]

### Integration Patterns
[Chosen patterns with rationale]

### Migration Plan
[Phased approach with rollback points]
```

### Implementation Roadmap Extensions

Add operational readiness gates to each phase:

```markdown
### Operational Readiness Gate — Phase [N]

Before proceeding to next phase, verify:
- [ ] Monitoring covers all new components
- [ ] Alerts configured with runbooks
- [ ] On-call rotation updated
- [ ] Rollback tested for this phase's changes
- [ ] Security review completed (if new data flows)
- [ ] Compliance checklist passed (if new data handling)
- [ ] Load test passed at expected scale
- [ ] Documentation updated (architecture, runbooks, API docs)
```

Add at the end of the roadmap:

```markdown
## Operational Maturity Roadmap

| Milestone | Target Date | Criteria |
|-----------|-------------|----------|
| Basic monitoring | Launch | Health checks, error alerting |
| Full observability | Launch + 30d | Distributed tracing, custom dashboards |
| Incident response | Launch + 60d | Runbooks for all P1 scenarios, on-call rotation |
| Chaos engineering | Launch + 90d | Regular failure injection, resilience validation |
| Compliance audit ready | Launch + 120d | All controls documented, evidence collected |
```

---

## Usage Rules

1. **Only activate what's relevant** — If the user says "SOC2 + 2 teams + greenfield," skip HIPAA, skip migration, skip PCI. Don't dump everything.
2. **Integrate, don't append** — Enterprise content should feel natural within the document, not like a bolted-on appendix.
3. **Maintain the anti-slop standard** — Enterprise docs are especially prone to generic compliance language. Every requirement must be specific to THIS system. "The system shall implement appropriate security controls" is slop. "All API endpoints require JWT validation via Auth0, with role claims checked against the RBAC table in PostgreSQL" is not.
4. **Scale to actual risk** — A B2B SaaS with 50 customers needs different depth than a healthcare platform with 10M patient records. Don't over-engineer governance for small-scale enterprise.
5. **Research compliance requirements** — Regulations change. GDPR enforcement evolves. SOC2 criteria get updated. Apply the Research-First Protocol to compliance claims just as rigorously as to technology claims.
