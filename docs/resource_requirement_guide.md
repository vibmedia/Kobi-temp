# 📚 Resource Requirement Guide — AI Influencer Agency

> A structured research guide for each knowledge domain. Use **Gemini Flash** + **NotebookLM MCP** to fill each gap with sourced, verified knowledge.
>
> **How to use:** For each domain below, feed the "Research Prompt" into NotebookLM with the suggested sources. The "Output Template" shows what the completed research should look like.

---

## How This Guide Works

```
For each Knowledge Domain:
1. Create a NotebookLM notebook (or add sources to existing one)
2. Add the listed Source URLs/Docs into the notebook
3. Use the Research Prompt to query the notebook
4. Save the structured output into the project docs folder
5. Mark the domain as ✅ when complete
```

---

## Domain 1: Instagram Private API & Automation

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- https://subzeroid.github.io/instagrapi/ (full docs)
- https://github.com/subzeroid/instagrapi/tree/master/docs (raw docs)
- https://github.com/subzeroid/instagrapi/blob/master/README.md
- https://subzeroid.github.io/instagrapi/usage-guide/direct.html (Direct Messages)
- https://subzeroid.github.io/instagrapi/usage-guide/user.html (User API)
- https://subzeroid.github.io/instagrapi/usage-guide/challenge_resolver.html
- Any blog posts or tutorials on "instagrapi DM automation 2025/2026"

### Research Prompt
```
I'm building an AI influencer marketing agency that needs to:
1. Send DMs to influencers at scale (5-8 per account per day, ramping up)
2. Scrape influencer profiles for follower count, engagement rate, bio data
3. Manage multiple IG accounts simultaneously with proxy binding
4. Handle 2FA challenges automatically
5. Track DM conversation threads for onboarding state

Please provide:
- Complete API reference for Direct Message operations (send, thread list, thread messages)
- Complete API reference for User operations (user_info, user_medias, user_followers)
- Session management best practices (login, save/load settings, cookie rotation)
- Rate limiting safe numbers for 2026 (DMs per day, API calls per hour)
- Challenge resolver setup and fallback strategies
- Proxy integration patterns
- Known risks, ban triggers, and mitigation strategies
- Error codes and their meanings
```

### Output Template
```markdown
# Instagram API Knowledge Base

## 1. Direct Message API
- Methods: [list all DM methods with parameters]
- Rate limits: [safe numbers per day/hour]
- Thread tracking: [how to maintain conversation state]

## 2. User/Profile API
- Methods: [list all user methods]
- Data fields available: [what data we can extract]
- Engagement rate formula: [how to calculate accurately]

## 3. Session Management
- Login flow: [step by step]
- Session persistence: [save/load patterns]
- Multi-account: [how to manage N accounts]

## 4. Proxy Integration
- How to bind proxy per account
- Proxy rotation patterns
- IP health monitoring

## 5. Safety & Anti-Ban
- Known ban triggers
- Safe operation parameters
- Recovery procedures
```

---

## Domain 2: WhatsApp Automation (Evolution API)

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- https://doc.evolution-api.com/ (official docs)
- https://github.com/EvolutionAPI/evolution-api
- https://doc.evolution-api.com/v2/en/get-started/introduction
- Search: "Evolution API WhatsApp drip sequence automation"
- Search: "Evolution API multi-number setup tutorial"

### Research Prompt
```
I'm building a WhatsApp outreach system for restaurant brands using Evolution API (self-hosted). I need:
1. Drip sequence automation — Day 1: hook, Day 2: social proof, Day 3: free deal offer
2. Multi-number management — 4-5 WhatsApp numbers for outreach
3. Message templates with dynamic variables ({restaurant_name}, {dish_name}, {competitor_stats})
4. Reply detection and intent classification routing
5. Integration with Chatwoot unified inbox for human handoff

Please provide:
- Setup guide for self-hosted Evolution API
- Message sending API reference (text, media, links)
- Webhook configuration for incoming messages
- Multi-device/multi-number architecture
- Rate limits and anti-ban strategies for WhatsApp
- Chatwoot integration setup
- Template message patterns for our 3-day drip
```

---

## Domain 3: Data Processing & Enrichment

**Status:** `[ ]` Incomplete

> ⚠️ All raw data is **provided manually** (CSV/Sheets). This domain covers enrichment and processing only — no scraping.

### Sources to Add to NotebookLM
- https://subzeroid.github.io/instagrapi/usage-guide/user.html (User API for enrichment)
- Search: "Instagram bio parsing and NLP extraction"
- Search: "data enrichment pipeline design Python"
- Search: "CSV data validation and normalization patterns"

### Research Prompt
```
I receive raw influencer and brand data via CSV/Google Sheets. I need to:

1. Influencer enrichment (via instagrapi):
   - Given an IG handle, fetch: follower count, post count, bio, average likes, average comments
   - Extract emails, YouTube links, Linktree links from bio text
   - Calculate engagement rate from last 12 posts
   - Classify niche from bio text and post captions
   - Detect content types (Reels, Stories, Carousels, Posts)

2. Brand/restaurant enrichment (from provided data):
   - Normalize cuisine types, price ranges
   - Validate phone numbers (10-digit Indian mobile)
   - Merge data from multiple sources (GMB + Zomato exports)
   - Deduplicate by name + area

3. Processing pipeline:
   - Batch enrichment architecture (process 100-1000 profiles)
   - Rate limiting for instagrapi calls during enrichment
   - Error handling (invalid handles, private accounts)
   - Delta updates (only re-enrich changed fields)

Provide:
- Python data pipeline design patterns
- CSV validation and normalization best practices
- Bio text NLP extraction patterns (emails, URLs, phone numbers)
- Deduplication strategies
```

---

## Domain 4: LangGraph Agent Architecture

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- https://langchain-ai.github.io/langgraph/ (full docs)
- https://langchain-ai.github.io/langgraph/concepts/ (concepts guide)
- https://langchain-ai.github.io/langgraph/how-tos/ (how-to guides)
- https://langchain-ai.github.io/langgraph/tutorials/ (tutorials)
- Our PRD.md and Blueprint as context files

### Research Prompt
```
I'm building 4 AI agents for an influencer marketing agency using LangGraph:

1. Influencer Outreach Agent — enrich imported data → send DM (instagrapi) → classify replies → onboard via conversation
2. Campaign Orchestrator — manage job board matching → coordinate visit → QC content → award points → payment → report
3. Data Enrichment Agent — periodic re-enrichment → update metrics → detect tier changes

Note: Brand outreach (WhatsApp) is manual, not part of AI agents.

For each agent, I need:
- State graph design (nodes, edges, conditional routing)
- Human-in-the-loop interrupt gate patterns
- State persistence strategy (where to store graph state)
- Error handling and retry patterns
- Multi-agent coordination (how agents communicate)
- LangSmith observability setup
- Cost optimization (when to use Gemini Flash vs Pro vs local Ollama)
```

---

## Domain 5: NLP & Sentiment Classification

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- Search: "LLM-based intent classification for chat messages"
- Search: "Gemini Flash text classification examples"
- Search: "WhatsApp reply sentiment analysis"

### Research Prompt
```
I need to classify incoming replies from two channels:

1. Instagram DMs from influencers — classify as:
   - positive (interested in brand deals)
   - negative (not interested)
   - spam (irrelevant message)
   - question (needs more info)
   - onboarding_data (providing requested information like phone/rate)

2. WhatsApp replies from restaurant brands — classify as:
   - positive (interested in influencer campaign)
   - neutral (asking for more details)
   - negative (not interested)
   - price_objection (too expensive)
   - already_doing (already using influencers)

Provide:
- Prompt templates for Gemini Flash classification
- Few-shot examples for each intent category
- Confidence threshold strategy (when to escalate to human)
- Hindi + English bilingual handling (code-switching is common in Delhi)
- Batch vs real-time classification tradeoffs
```

---

## Domain 6: Campaign Workflow Design (Temporal)

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- https://docs.temporal.io/ (Temporal docs)
- https://docs.temporal.io/concepts/what-is-a-workflow
- Search: "Temporal Python SDK tutorial"
- Our PRD campaign workflow section as context

### Research Prompt
```
I need to design long-running campaign workflows that span days to weeks:

Campaign lifecycle:
1. Brand creates campaign brief (24hr timeout for approval)
2. AI matches influencers to brief (human approves shortlist)
3. Outreach to selected influencers (72hr per influencer timeout)
4. Coordinate restaurant visit (72hr timeout)
5. Content QC review (AI pass → human review, 72hr timeout)
6. Posting confirmation (campaign duration timeout)
7. Payment settlement (48hr timeout)
8. Generate report (1hr)

Must handle 4 deal types differently: Barter, Paid, Paid+Barter, Performance

Provide:
- Workflow definition patterns in Temporal Python SDK
- Activity design for each step
- Timer and timeout handling
- Human approval gate implementation
- Retry and compensation logic
- Multi-workflow coordination (parallel influencer outreach)
- State visibility (dashboard querying workflow state)
```

---

## Domain 7: CRM Pipeline Design

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- Our PRD.md (Section 5 — CRM Pipelines)
- Search: "CRM pipeline design for agency"
- Search: "Google Sheets CRM template influencer marketing"

### Research Prompt
```
Design two CRM pipelines for our agency:

Brand Pipeline (11 stages):
Raw Lead → Contacted → Follow-up 1 → Follow-up 2 → Interested → Qualifying → Free Trial → Onboarded → Active → Dormant → Blacklisted

Influencer Pipeline (11 stages):
Discovered → DM Sent → Follow-up → Replied → Onboarding Chat → Onboarded → Available → On Campaign → Completed → Idle → Cold

For Phase 1 (Google Sheets) provide:
- Sheet structure and column layout for each pipeline
- Automation formulas (status tracking, age calculation)
- Data validation rules
- Dashboard views (daily action items, pipeline health)

For Phase 2+ (PostgreSQL) provide:
- Table schema design
- Status transition rules (which transitions are valid)
- Automated triggers (e.g., 7 days no response → move to Dormant)
- API endpoints needed for dashboard CRUD
```

---

## Domain 8: Marketing Psychology & Outreach Copy

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- Our Blueprint Section 5 (Marketing Psychology Playbook)
- Search: "persuasion psychology for cold outreach"
- Search: "WhatsApp cold outreach templates for restaurants India"
- Search: "Instagram DM templates for influencer recruitment"

### Research Prompt
```
I need battle-tested outreach copy for two channels:

1. WhatsApp to restaurants (brand outreach):
   - Day 1 hook: Reference their specific popular dish from Zomato
   - Day 2 social proof: Mention competitor's campaign results
   - Day 3 free deal: Offer free influencer visit
   - Follow-up after positive reply
   - Handling price objections
   - 5 variations of each template for A/B testing

2. Instagram DMs to food influencers:
   - Initial approach: Recruiter-style "we bring paid brand deals"
   - Value proposition for nano (barter) vs micro (paid) influencers
   - Onboarding conversation flow (collecting data naturally)
   - Re-engagement for cold leads
   - 5 variations of each template

Rules:
- Hindi + English mix (Delhi NCR style, Hinglish is natural)
- Apply psychology: reciprocity, social proof, scarcity, foot-in-the-door
- Keep messages under WhatsApp/IG character limits
- Include personalization variables: {brand_name}, {dish_name}, {influencer_name}, {follower_count}
```

---

## Domain 9: Influencer Scoring & Tier System

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- Search: "influencer engagement rate calculation formula 2025"
- Search: "fake follower detection methods"
- Search: "influencer quality scoring model"
- Our PRD Section 7 (Gamification System)

### Research Prompt
```
Design a comprehensive influencer scoring system:

1. Engagement Rate calculation:
   - Formula: (likes + comments) / followers × 100? Or more sophisticated?
   - How to handle Reels vs Posts vs Stories differently
   - Minimum post sample size for accuracy
   - Industry benchmarks for food influencers in India

2. Fake follower detection signals:
   - Follower-to-following ratio anomalies
   - Engagement rate vs follower count correlation
   - Comment quality analysis (generic vs genuine)
   - Follower growth pattern analysis

3. Quality Score model (0-100):
   - Engagement rate weight
   - Content consistency weight
   - Follower authenticity weight
   - Niche relevance weight
   - Response rate weight (to our DMs)

4. Tier assignment rules:
   - Nano: 1K-10K followers
   - Micro: 10K-50K
   - Mid: 50K-200K
   - Macro: 200K+

5. Points-based tier rules (replaces free-deal obligations):
   - Bronze: 0-49 points (newly onboarded)
   - Silver: 50-199 points (active contributor)
   - Gold: 200-499 points (trusted creator)
   - Platinum: 500+ points (top performer)
   - Points earned per job: Quick Review = 10, Standard Collab = 25, Full Campaign = 50, Long-term = 100
```

---

## Domain 10: Database Schema Design

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- Our PRD Section 11 (Database Schema)
- Search: "PostgreSQL schema design for marketplace"
- Search: "Drizzle ORM schema design patterns"

### Research Prompt
```
Design the complete PostgreSQL schema for our influencer marketing agency:

Core tables: influencers, brands, campaigns, campaign_influencers, outreach_messages, agent_logs, invoices, influencer_payouts, job_postings, job_applications, points_ledger, ig_accounts, proxy_groups

For each table provide:
- Complete column definitions with types
- Foreign key relationships
- Indexes for common queries
- JSONB fields for flexible data
- Enum types for status fields
- Timestamps and audit fields
- Migration strategy from Google Sheets (Phase 1) to PostgreSQL (Phase 2)
```

---

## Domain 11: Infrastructure & DevOps

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- https://coolify.io/docs/ (Coolify docs)
- https://docs.hetzner.com/ (Hetzner docs)
- Our Blueprint Section 4 (Tech Stack)
- Search: "Coolify self-hosted deployment guide"

### Research Prompt
```
Design the infrastructure for a multi-service application:

Services to host:
- FastAPI backend (Python)
- Next.js frontend
- PostgreSQL database
- Redis cache
- Qdrant vector DB
- Evolution API (WhatsApp)
- Chatwoot (unified inbox)
- Temporal (workflow engine)
- MinIO (file storage)

Phase 1: GCP ($300 free credits)
Phase 2: Hetzner VPS (€12-25/mo)

Provide:
- VM sizing and service distribution
- Docker Compose configuration
- Coolify deployment setup
- Domain and SSL configuration
- Backup strategy
- Monitoring setup (Grafana + Loki)
- Migration plan from GCP to Hetzner
```

---

## Domain 12: Legal & Compliance (India)

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- Search: "India DPDPA 2023 compliance for marketing"
- Search: "Instagram automation legal India 2025"
- Search: "WhatsApp bulk messaging legal India"
- Search: "influencer marketing regulations India ASCI"

### Research Prompt
```
Provide a legal compliance guide for an influencer marketing agency in India:

1. DPDPA (Digital Personal Data Protection Act) 2023:
   - What influencer data can we collect and store?
   - Consent requirements for DM outreach
   - Data retention policies
   - Data deletion on request

2. ASCI (Advertising Standards Council of India):
   - Sponsored content disclosure rules
   - Influencer marketing guidelines
   - Penalties for non-compliance

3. WhatsApp/Instagram Terms of Service:
   - Automated messaging risks
   - Account ban policies
   - Business vs personal account rules

4. Business Registration:
   - GST requirements
   - Invoice format for services
   - TDS on influencer payments
```

---

## Domain 13: Job Board System

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- Search: "job board marketplace design patterns"
- Search: "two-sided marketplace matching algorithm"
- Search: "influencer marketplace platform design"
- Our user_stories_phase1.md (Epic 6) as context

### Research Prompt
```
Design a job board system for an influencer marketing agency:

1. Brand/ops posts jobs with criteria:
   - Niche, tier range, deal type, location, deliverables, budget
   - Job types: Quick Review (story), Standard Collab (reel), Full Campaign (multi-post), Long-term
   - Points awarded per job type

2. Influencers browse and apply:
   - Jobs filtered by profile match (niche, tier, location)
   - Application includes profile summary and points total
   - Accept/reject flow for brands

3. Design questions:
   - Google Sheet tab vs web form vs dashboard (Phase 1 MVP)?
   - Application matching algorithm
   - Auto-close/expire logic
   - Notification flow for new jobs and application status
   - How to prevent no-shows after acceptance
```

---

## Domain 14: Points & Reputation System

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- Search: "gamification points system design marketplace"
- Search: "reputation system two-sided marketplace"
- Search: "mutual rating system design patterns"
- Our user_stories_phase1.md (Epic 7) as context

### Research Prompt
```
Design a points-based reputation system replacing free-deal obligations:

1. Points earning:
   - Quick Review = 10 points
   - Standard Collab = 25 points
   - Full Campaign = 50 points
   - Long-term Partnership = 100 points
   - Bonus multipliers for high-rated brands?

2. Tier thresholds:
   - Bronze: 0-49 (basic access)
   - Silver: 50-199 (priority matching, profile badge)
   - Gold: 200-499 (VIP brand access, featured profile)
   - Platinum: 500+ (premium brands, negotiation autonomy)

3. Mutual rating system:
   - Brand rates influencer (1-5 stars) after job
   - Influencer rates brand/restaurant (1-5 stars) after job
   - Average rating displayed on profiles
   - Low rating triggers and consequences

4. Incentive design:
   - Why does the points system motivate influencers better than free-deal obligations?
   - How to prevent gaming/point inflation?
   - Leaderboard design (monthly + all-time)
   - Points decay? (should old points expire?)
```

---

## Domain 15: Account & Proxy Management

**Status:** `[ ]` Incomplete

### Sources to Add to NotebookLM
- Search: "Instagram multi-account management proxy setup"
- Search: "residential proxy rotation for social media automation"
- Search: "instagrapi multi-account session management"
- Our user_stories_phase1.md (Epic 8) as context

### Research Prompt
```
Design an account and proxy management system for IG outreach:

1. Proxy groups:
   - 1 proxy serves 2 IG accounts
   - Residential vs datacenter proxies for IG safety
   - Health monitoring (up/down/slow)
   - Auto-failover if proxy goes down

2. Account management:
   - Dashboard showing all accounts with status
   - Per-account settings: DM rate limit, warmup stage, auto-pause
   - Session persistence (instagrapi dump_settings/load_settings)
   - Challenge detection and resolution tracking

3. Warmup protocol:
   - Week 1: follows + likes only
   - Week 2: comments
   - Week 3: DMs start
   - Warmup progress tracking per account

4. Account rotation:
   - Auto-rotate when account hits daily DM limit
   - Respect proxy group boundaries
   - Exhaustion alerts

5. Logging:
   - Per-account daily activity logs
   - Challenge/error history
   - DM send success/failure rates
```

---

## Quick Reference — Complete Domain Checklist

| # | Domain | NotebookLM Notebook | Status |
|---|--------|-------------------|--------|
| 1 | Instagram Private API | `ig-api-kb` | `[ ]` |
| 2 | WhatsApp Automation (Manual) | `wa-evolution-kb` | `[ ]` |
| 3 | Data Processing & Enrichment | `enrichment-kb` | `[ ]` |
| 4 | LangGraph Agents | `langgraph-kb` | `[ ]` |
| 5 | NLP Classification | `nlp-kb` | `[ ]` |
| 6 | Campaign Workflows | `temporal-kb` | `[ ]` |
| 7 | CRM Pipeline | `crm-kb` | `[ ]` |
| 8 | Marketing Copy | `marketing-kb` | `[ ]` |
| 9 | Influencer Scoring & Points | `scoring-kb` | `[ ]` |
| 10 | Database Schema | `database-kb` | `[ ]` |
| 11 | Infrastructure | `devops-kb` | `[ ]` |
| 12 | Legal Compliance | `legal-kb` | `[ ]` |
| 13 | Job Board System | `job-board-kb` | `[ ]` |
| 14 | Points & Reputation | `points-kb` | `[ ]` |
| 15 | Account & Proxy Management | `account-mgmt-kb` | `[ ]` |

> **Estimated time:** 3-5 hours to fill all 15 notebooks using Gemini Flash with the prompts above.
