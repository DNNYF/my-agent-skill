# Diagram Patterns for Documentation

Mermaid diagram patterns for common documentation scenarios. Use these as starting points — adapt to the actual project structure.

**Rule**: A diagram earns its place only if it communicates relationships that prose cannot efficiently convey. Don't diagram what a bullet list can say.

---

## When to Use Diagrams

| Scenario | Diagram Type | When to Skip |
|----------|-------------|--------------|
| 3+ components communicating | Architecture / Component | If it's just "frontend → backend → DB" — a sentence suffices |
| Multi-step user flow | Sequence | If it's linear with no branching — use numbered list |
| Data relationships | ER Diagram | If <3 entities with obvious relations |
| Deployment topology | Deployment | If single-platform deploy (just Vercel, just Heroku) |
| State transitions | State Diagram | If <3 states |
| Decision logic | Flowchart | If it's a simple if/else — use prose |

---

## Architecture / Component Diagram

Shows how major system components connect.

### Pattern: Web App with External Services

```mermaid
graph LR
    Client[Browser/Mobile] --> App[Next.js App<br/>Vercel]
    App --> DB[(PostgreSQL<br/>Supabase)]
    App --> Auth[Auth Provider<br/>NextAuth]
    App --> Payment[Stripe API]
    App --> Email[Resend API]
    Payment -->|webhook| App
```

### Pattern: Microservices

```mermaid
graph TD
    Gateway[API Gateway] --> UserSvc[User Service]
    Gateway --> OrderSvc[Order Service]
    Gateway --> PaySvc[Payment Service]
    UserSvc --> UserDB[(Users DB)]
    OrderSvc --> OrderDB[(Orders DB)]
    PaySvc --> PayDB[(Payments DB)]
    OrderSvc -->|event| Queue[Message Queue]
    Queue --> PaySvc
    Queue --> NotifSvc[Notification Service]
```

### Pattern: Serverless / Event-Driven

```mermaid
graph LR
    Trigger[API Gateway / Event] --> Lambda[Lambda Function]
    Lambda --> DynamoDB[(DynamoDB)]
    Lambda --> S3[S3 Bucket]
    Lambda --> SQS[SQS Queue]
    SQS --> Worker[Worker Lambda]
    Worker --> External[External API]
```

### Best Practices

- Label nodes with **what it is** + **what technology**: `App[Next.js App<br/>Vercel]` not just `App`
- Show direction of data flow with arrows
- Mark async/webhook connections distinctly: `-->|webhook|` or `-.->|async|`
- Max 8-10 nodes per diagram. If more, split into sub-diagrams.
- Use `[(  )]` for databases, `[  ]` for services, `{  }` for decisions

---

## Sequence Diagram

Shows how components interact over time for a specific flow.

### Pattern: API Request with Auth

```mermaid
sequenceDiagram
    participant C as Client
    participant M as Middleware
    participant A as API Route
    participant DB as Database

    C->>M: POST /api/invoices
    M->>M: Validate JWT
    alt Invalid token
        M-->>C: 401 Unauthorized
    end
    M->>A: Forward request
    A->>A: Validate body (Zod)
    A->>DB: INSERT invoice
    DB-->>A: Created record
    A-->>C: 201 { invoice }
```

### Pattern: OAuth Flow

```mermaid
sequenceDiagram
    participant U as User
    participant App as App
    participant Provider as OAuth Provider
    participant DB as Database

    U->>App: Click "Sign in with Google"
    App->>Provider: Redirect to OAuth
    Provider->>U: Show consent screen
    U->>Provider: Approve
    Provider->>App: Callback with code
    App->>Provider: Exchange code for token
    Provider-->>App: Access token + profile
    App->>DB: Upsert user record
    App-->>U: Set session cookie, redirect to dashboard
```

### Pattern: Webhook Processing

```mermaid
sequenceDiagram
    participant Ext as Stripe
    participant App as Webhook Handler
    participant DB as Database
    participant Email as Email Service

    Ext->>App: POST /api/webhooks/stripe
    App->>App: Verify signature
    App->>App: Parse event type
    alt payment_intent.succeeded
        App->>DB: Update invoice status = paid
        App->>Email: Send receipt to customer
    else payment_intent.failed
        App->>DB: Update invoice status = failed
        App->>Email: Notify owner
    end
    App-->>Ext: 200 OK
```

### Best Practices

- Name participants with short aliases + full name: `participant C as Client`
- Show error/alt paths — they're the most valuable part
- Keep to one flow per diagram. "User creates invoice" is one diagram, "User pays invoice" is another.
- Max 6 participants. If more, you're diagramming too much at once.

---

## Entity-Relationship Diagram

Shows data model relationships.

### Pattern: Basic Relations

```mermaid
erDiagram
    USER ||--o{ INVOICE : creates
    USER {
        uuid id PK
        string email
        string name
        enum role "admin | member"
        timestamp created_at
    }
    INVOICE ||--|{ LINE_ITEM : contains
    INVOICE {
        uuid id PK
        uuid user_id FK
        uuid client_id FK
        enum status "draft | sent | paid | overdue"
        decimal total
        date due_date
    }
    CLIENT ||--o{ INVOICE : receives
    CLIENT {
        uuid id PK
        string name
        string email
        string company
    }
    LINE_ITEM {
        uuid id PK
        uuid invoice_id FK
        string description
        int quantity
        decimal unit_price
    }
```

### Best Practices

- Include field types and constraints — not just entity names
- Mark PK/FK explicitly
- Show cardinality: `||--o{` (one-to-many), `||--||` (one-to-one), `}o--o{` (many-to-many)
- Only include entities relevant to the section being documented. Full schema → separate Database Documentation.
- Add enum values inline when they're important for understanding the domain

---

## Deployment Diagram

Shows infrastructure topology.

### Pattern: Typical Jamstack/Serverless

```mermaid
graph TD
    subgraph Vercel
        App[Next.js App]
        Edge[Edge Middleware]
        Serverless[API Routes<br/>Serverless Functions]
    end
    subgraph Supabase
        DB[(PostgreSQL)]
        Auth[Auth Service]
        Storage[File Storage]
    end
    subgraph External
        Stripe[Stripe]
        Resend[Resend]
        Upstash[Upstash Redis]
    end

    Edge --> App
    App --> Serverless
    Serverless --> DB
    Serverless --> Auth
    Serverless --> Storage
    Serverless --> Stripe
    Serverless --> Resend
    Serverless --> Upstash
```

### Pattern: Container-Based

```mermaid
graph TD
    subgraph Production [AWS ECS]
        LB[ALB Load Balancer]
        subgraph Service Cluster
            API1[API Container x3]
            Worker1[Worker Container x2]
        end
    end
    subgraph Data
        RDS[(RDS PostgreSQL)]
        Redis[(ElastiCache Redis)]
        S3[S3 Bucket]
    end
    subgraph CI/CD
        GH[GitHub Actions]
        ECR[ECR Registry]
    end

    LB --> API1
    API1 --> RDS
    API1 --> Redis
    Worker1 --> RDS
    Worker1 --> S3
    GH --> ECR
    ECR --> API1
    ECR --> Worker1
```

### Best Practices

- Use `subgraph` to group by provider/environment
- Include scaling info: `x3`, `x2` for replicas
- Show CI/CD pipeline if it's part of the deployment story
- Label connections only when the protocol matters: `-->|HTTPS|`, `-->|gRPC|`

---

## State Diagram

Shows entity lifecycle transitions.

### Pattern: Order/Invoice Status

```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> Sent: User clicks Send
    Sent --> Viewed: Client opens link
    Viewed --> Paid: Payment received
    Sent --> Overdue: Past due date
    Viewed --> Overdue: Past due date
    Overdue --> Paid: Late payment received
    Paid --> [*]
    Draft --> Cancelled: User cancels
    Sent --> Cancelled: User cancels
    Cancelled --> [*]
```

### Best Practices

- Label transitions with the trigger/event, not just the target state
- Show terminal states clearly with `[*]`
- Include error/cancel paths — they're often undocumented in code
- Keep to one entity per diagram

---

## Flowchart

Shows decision logic or process flow.

### Pattern: Request Processing Pipeline

```mermaid
flowchart TD
    Start[Request received] --> Auth{Authenticated?}
    Auth -->|No| Reject[401 Unauthorized]
    Auth -->|Yes| RateLimit{Rate limit OK?}
    RateLimit -->|No| TooMany[429 Too Many Requests]
    RateLimit -->|Yes| Validate{Body valid?}
    Validate -->|No| BadReq[400 Bad Request]
    Validate -->|Yes| Process[Process request]
    Process --> DB[Write to DB]
    DB --> Response[200 Success]
```

### Best Practices

- Use `{  }` for decision nodes, `[  ]` for actions, `(  )` for start/end
- Keep decisions binary when possible (Yes/No)
- Don't flowchart simple linear processes — use a numbered list instead
- Max 12 nodes. If more, break into sub-processes.

---

## Anti-Patterns

### Don't Do This

1. **The Everything Diagram** — One massive diagram showing every component, every connection, every database table. Nobody can read it. Split by concern.

2. **The Obvious Diagram** — Diagramming `Browser → Server → Database` adds nothing. Only diagram when there are non-obvious connections or multiple paths.

3. **The Unlabeled Diagram** — Nodes named `Service A`, `Service B`, `Database`. Use real names from the codebase.

4. **The Stale Diagram** — A diagram that doesn't match the code is worse than no diagram. When updating docs, verify diagrams still reflect reality.

5. **The Prose Diagram** — Putting full sentences inside nodes. Keep node labels to 2-4 words. Use notes or surrounding text for explanation.

### Size Rules

- **Architecture**: Max 10 nodes
- **Sequence**: Max 6 participants, max 15 messages
- **ER**: Max 8 entities per diagram (split by domain if larger)
- **Flowchart**: Max 12 nodes
- **State**: Max 8 states
