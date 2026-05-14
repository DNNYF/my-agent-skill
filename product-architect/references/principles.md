# Principles & Anti-Patterns

Decision-making heuristics and failure modes to avoid when architecting products.

---

## Core Principles - Expanded

### 1. No Overengineering

Design for the scale needed today, not the scale imagined for next year.

**Heuristic**: If the user count will be < 1000 in the first 6 months, the architecture should be the simplest possible thing that works. That usually means:

| Instead of | Use | When to upgrade |
|-----------|-----|----------------|
| Microservices | Monolith | When deploy frequency or team size demands it |
| Kubernetes | Single server / PaaS (Vercel, Railway) | When you need auto-scaling |
| Event-driven architecture | Direct function calls | When async processing becomes a bottleneck |
| GraphQL | REST | When frontend teams need flexible querying at scale |
| Redis cache | In-memory / no cache | When DB queries measurably slow down response times |
| Message queue | Cron job | When job reliability or ordering becomes critical |

The "when to upgrade" column is the key. Build the simple version and define the trigger for upgrading. This prevents both over-engineering and under-engineering.

### 2. No Assumptions

Every assumption is a potential failure point. The more confident the assumption, the more dangerous it is.

**Common false assumptions:**
- "Users will read the onboarding tutorial" - They won't.
- "We need real-time updates" - Polling every 30 seconds covers 90% of use cases.
- "Mobile support is essential for v1" - Often it's not. Ask the user.
- "Users will create accounts" - Are they motivated enough? What's the minimum viable auth?
- "The data model won't change" - It always changes. Design for migration, not permanence.

**The assumption challenge protocol:**
When an assumption surfaces, ask: "What happens if this assumption is wrong?" If the answer is "we'd need to redesign everything", that assumption must be validated before building.

### 3. Explicit > Implicit

Every decision left implicit becomes a bug in understanding. Implicit decisions include:
- Default behaviors ("what happens when the user does nothing?")
- Error handling ("what happens when the API fails?")
- Edge cases ("what if the list has 10,000 items?")
- Auth boundaries ("can user A see user B's data?")

**Rule**: If two reasonable developers could interpret a requirement differently, it's not explicit enough.

### 4. Trace to User Value

The traceability chain: User Problem -> Use Case -> Requirement -> Feature -> Implementation

If any link in this chain breaks, something is wrong:
- Feature without a requirement = scope creep
- Requirement without a use case = speculative engineering
- Use case without a problem = solution looking for a problem

**The "for whom?" test**: For any feature, answer "who specifically benefits from this, and how?" If the answer is vague ("everyone" or "it's just good practice"), the feature is suspect.

### 5. Scope Ruthlessly

Scope is a one-way door during development. Adding scope is easy; removing it is painful (sunk cost, emotional attachment, "but we already built it").

**Scope protection strategies:**
1. Write the "Not Included" section before the "Included" section
2. For every feature added, ask: "What can we remove to keep the timeline?"
3. Define "done" for MVP on day one and resist moving the goalpost
4. Separate "must work" from "must look good" - functionality before polish

**The 2-week test**: If the MVP can't be built in 2 weeks (for a solo developer) or 4 weeks (for a small team), the scope is probably too wide. This isn't a hard rule, but it's a useful pressure test.

### 6. Think in States

Screens and components are not static layouts - they're state machines. Common states that are frequently forgotten:

| State | What causes it | Often forgotten? |
|-------|---------------|-----------------|
| Loading (first time) | Initial data fetch | No |
| Loading (refresh) | User pulls to refresh | Yes |
| Empty | No data exists yet | Yes |
| Empty (filtered) | Data exists but filter returns nothing | Yes |
| Error (network) | API unreachable | Sometimes |
| Error (server) | API returns 500 | Sometimes |
| Error (permission) | User lacks access | Yes |
| Partial | Some data loaded, more loading | Yes |
| Stale | Data loaded but possibly outdated | Yes |
| Offline | No internet connection | Yes |

Not every screen needs every state. But every screen needs the team to consciously decide which states apply.

### 7. Boring Technology Wins

"Boring" means: well-documented, widely deployed, failure modes are well-understood, easy to hire for, easy to debug.

**The boring technology test:**
- Can I find the answer to most problems on Stack Overflow? -> Boring (good)
- Does the documentation require reading the source code? -> Not boring (risky)
- Has this technology been used in production for > 3 years? -> Boring (good)
- Is the community excited about it? -> Possibly not boring (check maturity)

---

## Anti-Patterns - Detailed

### 1. Template-Filler Documents

**Symptom**: Documents that follow a template but contain generic, interchangeable content.

**Example of bad PRD section:**
```
## Security Requirements
- The system shall implement appropriate security measures
- User data shall be protected according to industry standards
- Authentication shall be secure
```

**This says nothing.** Every product could paste this in. A good version:
```
## Security Requirements
- NFR-S01: Passwords hashed with bcrypt (cost factor 12)
- NFR-S02: JWT tokens expire after 24 hours, refresh tokens after 30 days
- NFR-S03: API rate limited to 100 requests/minute per user
- NFR-S04: User data encrypted at rest (Supabase default AES-256)
```

### 2. Buzzword Architecture

**Symptom**: Architecture uses trendy terms without connecting them to actual requirements.

**Red flags:**
- "Event-driven microservice architecture" for a CRUD app
- "AI-powered" when a simple rule engine would work
- "Blockchain-based" when a regular database is sufficient
- "Real-time collaborative" when async is perfectly fine
- "Serverless" when a $5/month VPS would do

**Fix**: For every technology choice, complete this sentence: "We chose [tech] over [simpler alternative] because [specific requirement that the simpler option cannot meet]." If the sentence can't be completed, use the simpler option.

### 3. Feature Creep Disguised as Requirements

**Symptom**: Features sneak in through vague language or "while we're at it" reasoning.

**Common disguises:**
- "The system should support extensibility" -> Too vague. Extensibility for what, specifically?
- "Users should be able to customize their experience" -> Customize what? Theme? Layout? Functionality?
- "We need an admin panel" -> For what operations, specifically? Often a database GUI suffices for v1.
- "Let's add analytics" -> What metrics, specifically? Do we need them before launch?

**Fix**: Every requirement must be decomposable into specific, implementable tasks. If a requirement cannot be turned into a task with clear acceptance criteria, it's too vague to include.

### 4. Premature Abstraction

**Symptom**: Building generic, configurable systems before understanding the specific use cases.

**Examples:**
- Building a "plugin system" before having any plugins
- Creating a "template engine" before having 3+ templates
- Designing a "multi-tenant architecture" with one customer
- Building a "workflow engine" for one workflow

**Rule of three**: Don't abstract until you have three concrete instances. Build the first instance inline. Build the second instance with copy-paste. On the third, refactor to an abstraction.

### 5. Ignoring the Onboarding Cliff

**Symptom**: Product works great once set up, but the path from "first visit" to "first value" is unclear or too long.

**Fix**: Map the onboarding path explicitly:
1. How does the user discover the product?
2. What's the first screen they see?
3. How many steps to first value moment?
4. What's the absolute minimum information needed to start?

**Target**: Time to first value < 5 minutes. If it takes longer, either simplify onboarding or reduce prerequisites.

### 6. Estimation Without Basis

**Symptom**: Timelines are numbers pulled from thin air. "Should take about 2 weeks."

**Fix options:**
1. **Analogous estimation**: "Feature X is similar to [past feature], which took [time]"
2. **Decomposition**: Break into sub-tasks, estimate each, add 30% buffer
3. **Spike-first**: "I need to build a prototype of [risky part] before I can estimate the whole thing"
4. **Honest uncertainty**: "Estimate confidence is low. Range is 1-3 weeks depending on [specific uncertainty]"

Never give a single-point estimate. Always give a range and name what determines where in the range the actual effort falls.

---

## Decision-Making Heuristics

Quick rules of thumb for common product architecture decisions:

| Decision | Heuristic |
|----------|-----------|\n| Build vs. buy | Buy if it's not your core value proposition |
| SQL vs. NoSQL | SQL unless your data is genuinely unstructured (it usually isn't) |
| SPA vs. SSR | SSR unless you need offline or complex client-side state |
| REST vs. GraphQL | REST unless frontend needs flexible queries across many resources |
| Auth build vs. service | Service (Auth0, Supabase Auth, Clerk) unless auth IS the product |
| Monorepo vs. multirepo | Monorepo unless teams need fully independent deploy cycles |
| Testing strategy | Integration tests > unit tests for most web apps. Test user flows. |
| Mobile approach | Responsive web first. Native only if push notifications or hardware access are core |
| Hosting | PaaS (Vercel, Railway, Fly.io) until you need something they can't provide |
| CI/CD | Keep it simple. GitHub Actions covers most needs. |

These are defaults, not rules. Override when specific requirements demand it - but document why.

**Critical caveat**: Every heuristic above references specific products and services. These may have changed pricing, capabilities, or market position since your training data. Before writing any of these into a document, verify the current state. "Vercel" may have changed its free tier. "Clerk" may have changed its pricing model. "Railway" may have shut down or pivoted. Check first.

---

## Zero AI Slop - Comprehensive Guide

Every document produced by this skill must read like it was written by a human expert who has shipped products, made mistakes, learned from them, and has strong opinions backed by experience. Not by an AI that memorized a product management textbook.

This section exists because the default AI writing mode produces text that looks professional but carries no real information. A senior engineer or PM can smell it instantly. The goal is output that a hiring manager would look at and think "this person knows what they're doing."

### The Slop Taxonomy

#### Category 1: Empty Authority

Sentences that sound important but say nothing actionable.

| Slop | What an expert would write instead |
|------|-----------------------------------|
| "Security is a top priority for this application" | "Passwords hashed with bcrypt, cost 12. JWT expires in 1h. Rate limit: 60 req/min per IP." |
| "The system architecture is designed for scalability" | "Single Postgres instance handles ~5000 concurrent users. If we pass that, add read replicas. Connection pooling via PgBouncer." |
| "We will implement comprehensive error handling" | "API returns structured errors: `{code, message, field}`. Frontend shows inline field errors on 422, toast on 500, redirect on 401." |
| "The database design follows best practices" | "Normalized to 3NF. Denormalized `user_display_name` on `comments` table to avoid JOIN on the feed query." |
| "Performance optimization will be applied where needed" | "Feed query must return in <200ms. Index on `(user_id, created_at DESC)`. Paginate with cursor, not offset." |

**Pattern**: Slop announces a quality. Expert shows the specific implementation.

#### Category 2: Hedge Padding

Avoiding commitment by softening every statement.

| Slop | Expert version |
|------|---------------|
| "This could potentially help improve user engagement" | "Push notifications for new messages. Expect 15-20% reactivation based on industry benchmarks." |
| "We might consider implementing caching" | "Cache the dashboard query in Redis, TTL 30 seconds. Invalidate on write." |
| "Users may find it useful to have filtering capabilities" | "Filter by: status (active/archived), date range, assigned user. All server-side, all indexed." |
| "It would be beneficial to explore various authentication options" | "Use Supabase Auth. Email/password for v1. Google OAuth in v2 if adoption justifies it." |

**Pattern**: Slop hedges. Expert decides and states the decision.

#### Category 3: Filler Transitions

Words that exist only to connect sections, adding zero information.

**Kill these on sight:**
- "Now let's take a look at..."
- "Moving on to the next important aspect..."
- "It's worth noting that..."
- "In this section, we will discuss..."
- "As mentioned earlier..."
- "With that in mind, let's explore..."
- "Another key consideration is..."
- "It goes without saying that..." (then don't say it)
- "Last but not least..."
- "At the end of the day..."

**Fix**: Just start the next section. If the content is clear, it doesn't need a verbal segue.

#### Category 4: Inflated Lists

Stretching 3 real points into 7 by rephrasing the same idea.

**Slop version** (7 items that say 2 things):
```
Benefits of this approach:
- Improved user experience
- Better usability for end users
- More intuitive interface
- Enhanced user satisfaction
- Reduced learning curve
- Streamlined workflow
- Simplified interaction patterns
```

**Expert version** (2 items that are actually different):
```
Benefits:
- Users complete the checkout flow in 3 steps instead of 7. Tested with 5 users, 
  average completion time dropped from 4 minutes to 90 seconds.
- No account required for first purchase. Guest checkout converts at 2-3x 
  vs forced registration (Baymard Institute data).
```

**Pattern**: Slop counts items. Expert makes each item carry weight.

#### Category 5: Pseudo-Specificity

Looks detailed but doesn't actually constrain the implementation.

| Slop | Expert version |
|------|---------------|
| "The API should return appropriate status codes" | "200 for success, 201 for creation, 400 for validation, 401 for unauth, 404 for missing, 409 for conflict, 429 for rate limit. No 500 should reach the client without a structured error body." |
| "The system should handle edge cases gracefully" | "Empty state: show illustration + 'Create your first project' CTA. Error state: show what failed + retry button. Never show a blank screen or raw error." |
| "Data validation should be implemented on both client and server" | "Client: instant field validation on blur (email format, password 8+ chars, name non-empty). Server: re-validate everything, client validation is UX only, not security." |

**Pattern**: Slop says "handle it properly." Expert defines what "properly" means.

#### Category 6: Template Placeholders Left to Die

Brackets and placeholders that were never filled in, signaling the document was generated, not thought through.

**Never leave these in a final document:**
- `[TBD]`, `[TODO]`, `[Insert here]`
- `[Company Name]` when the company name is known
- `[describe the approach]` - actually describe it
- `[add specific metrics]` - add them or explain why they're unknown

**Rule**: If a placeholder can't be filled because the information genuinely doesn't exist yet, write that explicitly: "Success metric TBD - requires 2 weeks of baseline data post-launch to set meaningful targets." That's honest. `[TBD]` is lazy.

### Writing Calibration Examples

#### Problem Statement

**AI slop:**
> "In today's fast-paced digital landscape, users face numerous challenges when it comes to managing their daily tasks and productivity. Traditional task management solutions often fall short of meeting the diverse needs of modern professionals, leading to frustration and inefficiency. There is a growing need for a more intuitive and comprehensive solution that can seamlessly integrate into users' existing workflows."

**Expert:**
> "Freelance developers juggle 3-5 client projects simultaneously. They track tasks across Notion, Slack threads, email, and their own memory. Things fall through the cracks. The usual fix - a project management tool - is designed for teams with a PM. Solo devs need something lighter: one screen that shows 'what am I supposed to do today, across all clients?' without the overhead of maintaining a project management system."

**Why the expert version works**: Names the specific user (freelance dev). Quantifies the situation (3-5 clients). Describes the actual behavior (juggling across tools). Identifies why existing solutions fail (designed for teams). States the core need in the user's own words.

#### Architecture Decision

**AI slop:**
> "After careful consideration of various architectural approaches, we have decided to adopt a microservices architecture to ensure scalability, maintainability, and flexibility. This modern approach will allow our development team to work independently on different services, enabling faster development cycles and easier deployment."

**Expert:**
> "Monolith. One Next.js app, one Postgres database, deployed on Vercel. 
> 
> Reasoning: solo developer, expected user base under 1000 for the first year, all features share the same data model. Microservices would add deployment complexity, network latency between services, and distributed transaction headaches - none of which solve a problem we actually have.
> 
> Revisit if: team grows past 3 developers working on different features simultaneously, or if a specific component needs independent scaling (e.g., file processing)."

**Why the expert version works**: Makes a decisive choice. States the constraints that drove the decision. Explains what was rejected and why. Defines the trigger for revisiting.

#### Requirement

**AI slop:**
> "FR-001: User Authentication
> The system shall provide a secure and user-friendly authentication mechanism that allows users to create accounts, log in, and manage their sessions effectively. The authentication system should support industry-standard security practices and provide a seamless user experience."

**Expert:**
> "FR-001: Auth - Email/Password Login
> - Register with email + password (min 8 chars, at least 1 number)
> - Login returns JWT (1h expiry) + refresh token (30d, httpOnly cookie)
> - Password reset via email link (expires in 1h, single-use)
> - No OAuth in v1. Adds complexity, unclear if users want it. Revisit after launch survey.
> - Rate limit: 5 failed login attempts per email per 15 minutes, then 15-minute lockout
> 
> Acceptance criteria:
> - [ ] User registers, receives confirmation email, can log in
> - [ ] Invalid password shows inline error, doesn't reveal if email exists
> - [ ] JWT expiry triggers silent refresh, user doesn't see a login screen mid-session
> - [ ] Password reset email arrives within 30 seconds"

**Why the expert version works**: Specific values (8 chars, 1h, 30d, 5 attempts). Explains what's excluded and why. Acceptance criteria are testable with a stopwatch and a browser.

### Self-Check Protocol

Before finalizing any document section, run through this checklist:

1. Read each sentence aloud. Does it sound like a person talking, or a generated document?
2. Count adjectives. More than 2 per sentence is a smell. Cut "comprehensive", "robust", "seamless", "intuitive", "cutting-edge", "state-of-the-art", "next-generation" unless they're backed by a specific meaning.
3. Check for opinion. An expert document has clear "we chose X over Y because Z." If the document only describes without deciding, it's slop.
4. Check for numbers. Requirements without numbers are wishes. "Fast" -> "under 200ms." "Secure" -> "bcrypt cost 12." "Scalable" -> "handles 5000 concurrent."
5. Read the document to someone unfamiliar with the project. If they say "sounds professional" but can't tell you what the product does after reading, it's slop. If they say "oh, so it's basically X for Y people" - it's clear.

### 8. Training-Data Lock-In

The most insidious anti-pattern for AI agents: recommending technologies, patterns, and services based solely on training data without verifying current state.

**Symptom**: Recommendations that were correct 12-18 months ago but are now outdated, deprecated, or superseded. The AI sounds confident because it "learned" these as facts, but facts have an expiration date.

**Examples:**

| What the AI recommends | What actually happened |
|------------------------|----------------------|
| `create-react-app` for new React projects | Deprecated. React docs now recommend Next.js, Remix, or Vite |
| `getServerSideProps` in Next.js | Legacy Pages Router pattern. App Router uses `async` server components |
| Heroku for free hosting | Free tier eliminated in 2022 |
| `moment.js` for dates | Deprecated by its own maintainers. Use `date-fns` or `dayjs` |
| `passport.js` for auth | Still works but modern alternatives (Lucia, Auth.js v5) are simpler |
| Firebase free tier limits | Changed multiple times, specific numbers in training are unreliable |

**The deeper problem**: This isn't just about wrong recommendations. When AI builds architecture on stale knowledge, it creates cascading errors:
1. Deprecated tool gets recommended → user builds on it → hits dead-end 3 weeks in
2. Outdated API shape gets specified → developer implements it → integration fails at runtime
3. Wrong pricing assumption → budget plan is wrong → project goes over cost

**Fix protocol**:
1. Before any technology recommendation, search for current status
2. Before any API specification, read current official docs
3. Before any pricing reference, check the actual pricing page
4. Before stating a "best practice," verify it's still recommended by the framework/community
5. When uncertain, say "I recommend X based on my training, but verify current status at [link]" — transparency over false confidence

**Self-check**: After completing any Technical Architecture or API Specification document, re-read every technology and service mentioned. For each one, ask: "Am I certain this is still current, or am I relying on training data?" If the answer is training data, research it.

### Banned Words & Phrases

These words are not forbidden entirely, but when they appear, they are almost always padding. Challenge every usage:

| Word/Phrase | Why it's suspect | Alternative |
|-------------|-----------------|-------------|
| "Leverage" | Corporate jargon for "use" | "Use" |
| "Robust" | Means nothing specific | State what makes it robust |
| "Seamless" | Everything claims to be seamless | Describe the actual UX |
| "Comprehensive" | Usually means "we listed a lot of things" | Be specific about what's covered |
| "Scalable" | Without numbers, it's a wish | "Handles N users/requests" |
| "Intuitive" | Subjective, untestable | "User completes X without documentation" |
| "Best practices" | Which ones? Name them. | Name the specific practice |
| "Industry standard" | Which standard? Cite it. | Name the standard (OAuth 2.0, OWASP Top 10) |
| "Streamline" | Vague improvement claim | "Reduces from 7 steps to 3" |
| "Empower" | Marketing speak | "Lets users do X" |
| "Ecosystem" | Often meaningless | "The tools that connect to this" or be specific |
| "End-to-end" | Overused, vague | Specify start and end points |
| "World-class" | Unmeasurable | Remove, or define what world-class means here |
| "Cutting-edge" | Means "new", adds nothing | Name the technology |
| "Deep dive" | Filler transition | Just go deep. Don't announce it. |
