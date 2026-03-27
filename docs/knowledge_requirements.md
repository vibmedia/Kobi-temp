# 🧠 Knowledge Requirements — AI Influencer Agency (Kobi)

> Complete inventory of knowledge domains, agent/skill/workflow dependencies, and the trimmed `.agent` structure needed to build and manage the VibMedia AI Influencer project.
>
> **Key Assumptions:**
> - All raw data (influencer lists, brand lists) is **provided manually** — no automated scraping
> - AI outreach (DMs) uses **instagrapi only** — Socionator is manual/cold-email only
> - WhatsApp brand outreach is **manual** (not AI agent team)
> - Upsell services (WordPress, Meta Ads) are **manual** — not part of AI system
> - Proxy allocation: **1 proxy per 2 IG accounts**

---

## 1. Knowledge Domains Overview

Every column below represents a **distinct knowledge area** that the project team (human + AI agents) must master. Items marked 🔴 are Phase 1 critical.

| # | Domain | Why Needed | Phase |
|---|--------|-----------|-------|
| 1 | **Instagram Private API (instagrapi)** | DM outreach to influencers, profile scraping, engagement data, story/reel interaction | 🔴 1 |
| 2 | **WhatsApp Automation (Evolution API)** | Brand outreach drip sequences, multi-number messaging | 🔴 1 |
| 3 | **Data Processing & Enrichment** | Enrich provided influencer/brand data — bios, engagement, niche classification | 🔴 1 |
| 4 | **LangGraph Agent Architecture** | Stateful AI agent graphs, human-in-the-loop gates, interrupt patterns | 🟡 2 |
| 5 | **NLP / Sentiment Classification** | Classify reply intent (positive/negative/spam/price_objection) | 🟡 2 |
| 6 | **Conversational AI / Chat Onboarding** | Collect influencer data via natural conversation instead of forms | 🟡 2 |
| 7 | **FastAPI Backend** | Python API gateway for all agents and dashboard | 🟡 2 |
| 8 | **Next.js 15 (TypeScript)** | Internal dashboard → client portal → SaaS | 🟡 2 |
| 9 | **PostgreSQL + Drizzle ORM** | Relational DB for campaigns, influencers, brands, invoices | 🟡 2 |
| 10 | **Qdrant Vector DB** | Semantic search for influencer matching ("find food bloggers who do casual reviews") | 🟡 2 |
| 11 | **Temporal Workflow Engine** | Long-running campaign lifecycle orchestration (days/weeks) | 🟡 2 |
| 12 | **Proxy Management** | IG account safety — 1 proxy per 2 accounts, proxy groups, health monitoring | 🔴 1 |
| 13 | **CRM Pipeline Design** | Brand (11-stage) + Influencer (11-stage) pipeline management | 🔴 1 |
| 14 | **Marketing Psychology** | Reciprocity, anchoring, social proof, scarcity — for outreach copy | 🔴 1 |
| 15 | **Content QC / Brand Safety** | AI-check video drafts vs brief (hashtags, mentions, duration, tone) | 🟡 2 |
| 16 | **Payment Integration (Razorpay/UPI)** | Brand billing, influencer commission tracking | 🟢 3 |
| 17 | **WordPress Site Generation** | ⚠️ MANUAL — Upsell single-page restaurant websites | 🟢 3 |
| 18 | **Meta Ads Management** | ⚠️ MANUAL — Boost influencer reels as paid IG/FB ads | 🟢 3 |
| 19 | **DevOps / Self-Hosting (Coolify/Hetzner)** | Deploy all services on budget VPS infrastructure | 🟡 2 |
| 20 | **Job Board System** | Autonomous brand job postings, influencer self-selection, application management | 🔴 1 |
| 21 | **Points & Reputation System** | Points-based incentive replacing free-deal obligations, tier upgrades via points | 🔴 1 |
| 22 | **Account Management System** | IG account dashboard, proxy groups, session management, warmup tracking | 🔴 1 |
| 23 | **Reporting & Analytics** | Auto-generated campaign reports — reach, engagement, ROI | 🟡 2 |
| 24 | **Chatwoot (Unified Inbox)** | Human agent inbox for deal closings, WhatsApp routing | 🟡 2 |
| 25 | **LLM Prompt Engineering** | Outreach messages, negotiation flows, content review prompts | 🔴 1 |
| 26 | **Instagram Account Management** | Multiple recruiter personas, warm-up strategies, ban avoidance | 🔴 1 |
| 27 | **Contract & E-Signature** | Template-based deal contracts with digital signatures | 🟢 3 |

---

## 2. Instagrapi Knowledge Map

> Source: [subzeroid/instagrapi](https://github.com/subzeroid/instagrapi) — Unofficial Instagram API for Python

### 2.1 Capabilities We Need

| Capability | Instagrapi Module | Use Case |
|-----------|-------------------|----------|
| **Direct Messages** | `direct_send()`, `direct_send_link()`, `direct_threads()` | Influencer outreach DM sequences |
| **User Profile Data** | `user_info()`, `user_medias()`, `user_followers()` | Scrape follower count, bio, engagement |
| **Media Analysis** | `media_info()`, `media_comments()`, `media_likers()` | Engagement rate calculation, content audit |
| **Story Interaction** | `user_stories()` | Check influencer activity/content style |
| **Login / Session** | `login()`, `load_settings()`, `dump_settings()` | Multi-account session persistence |
| **Challenge Resolver** | Email/SMS 2FA handlers | Handle IG verification challenges |
| **Hashtag Search** | `hashtag_medias_top()` | Discover food influencers in Delhi NCR |
| **Location Search** | `location_medias_top()` | Find creators posting from Delhi restaurants |

### 2.2 Knowledge Questions for Instagrapi

1. **Rate Limits:** What are the safe DM sending limits per account per day? (PRD says 5→6→8 ramping)
2. **Session Management:** How to persist sessions across server restarts and rotate between accounts?
3. **Proxy Integration:** How to bind each IG account to a specific proxy via instagrapi?
4. **Challenge Handling:** How to auto-resolve email/SMS challenges without human intervention?
5. **DM Thread Management:** How to track conversation state (onboarding progress) across DM threads?
6. **Error Handling:** What errors indicate account compromise vs temporary blocks?
7. **Engagement Calculation:** How to compute accurate engagement rate from `user_medias()` data?
8. **Content Type Detection:** How to classify posts as Reels/Stories/Carousels for influencer profiling?

---

## 3. Agent Framework Mapping

### 3.1 Full vibmedia-agent-skills Inventory

**Agents (25 total):**

| Agent | Relevant? | Role in This Project |
|-------|-----------|---------------------|
| `orchestrator` | ✅ **CORE** | Multi-agent task coordination |
| `project-planner` | ✅ **CORE** | 4-phase planning methodology |
| `backend-specialist` | ✅ **KEEP** | FastAPI + Celery backend |
| `frontend-specialist` | ✅ **KEEP** | Next.js dashboard |
| `database-architect` | ✅ **KEEP** | PostgreSQL schema design |
| `debugger` | ✅ **KEEP** | Bug fixing across stack |
| `devops-engineer` | ✅ **KEEP** | Coolify/Hetzner deployment |
| `code-reviewer` | ✅ **KEEP** | PR review quality |
| `documentation-writer` | ✅ **KEEP** | API docs, runbooks |
| `product-manager` | ✅ **KEEP** | PRD refinement, feature scoping |
| `product-owner` | ✅ **KEEP** | Backlog prioritization |
| `security-auditor` | ✅ **KEEP** | API key safety, IG credential security |
| `test-engineer` | ✅ **KEEP** | Test coverage |
| `explorer-agent` | ✅ **KEEP** | Codebase exploration |
| `performance-optimizer` | ⚠️ Phase 3+ | Optimization at scale |
| `seo-specialist` | ⚠️ Phase 3+ | Landing page SEO |
| `local-seo-manager` | ⚠️ Phase 3+ | Restaurant client local SEO upsell |
| `wordpress-site-builder` | ⚠️ Phase 3+ | Website upsell |
| `listing-automation` | ⚠️ Phase 3+ | GMB listing management |
| `client-coordinator` | ⚠️ Phase 3+ | Client communication |
| `mobile-developer` | ❌ REMOVE | No mobile app in Phase 1-3 |
| `game-developer` | ❌ REMOVE | Not relevant |
| `crocoblock-specialist` | ❌ REMOVE | WordPress-specific |
| `penetration-tester` | ❌ REMOVE | Overkill for bootstrap |
| `pos-operations-manager` | ❌ REMOVE | POS-specific |
| `qa-automation-engineer` | ❌ REMOVE | Merged into test-engineer |
| `code-archaeologist` | ❌ REMOVE | Not needed for new project |

### 3.2 Trimmed Influencer Branch — Agent Team (14 agents)

**Core Team (Phase 1-2):**
1. `orchestrator` — Central coordination
2. `project-planner` — Planning methodology
3. `backend-specialist` — FastAPI + LangGraph agents
4. `frontend-specialist` — Next.js dashboard
5. `database-architect` — PostgreSQL + Qdrant schema
6. `debugger` — Cross-stack debugging
7. `devops-engineer` — Infrastructure
8. `code-reviewer` — Quality gates
9. `documentation-writer` — Docs
10. `product-manager` — Feature scoping
11. `security-auditor` — Credentials & API safety
12. `test-engineer` — Test coverage
13. `explorer-agent` — Codebase navigation
14. `product-owner` — Backlog management

**NEW Agents to Create:**

| New Agent | Purpose | Skills |
|-----------|---------|--------|
| `influencer-outreach-agent` | Manages IG DM outreach logic, persona management, rate limiting, instagrapi integration | `instagram-dm-outreach`, `proxy-management` |
| `brand-outreach-agent` | WhatsApp drip sequences, Evolution API integration, lead qualification, Chatwoot routing | `whatsapp-automation`, `cold-email`, `marketing-psychology` |
| `data-scraper-agent` | GMB/Zomato/IG scraping, data normalization, deduplication | `web-scraping`, `data-analysis` |
| `campaign-orchestrator-agent` | End-to-end campaign lifecycle — matching, outreach, QC, payment, reporting | `langgraph-fundamentals`, `langgraph-human-in-the-loop` |

---

## 4. Skills Mapping

### 4.1 Existing Skills to KEEP

| Skill | Why |
|-------|-----|
| `clean-code` | Global mandatory |
| `brainstorming` | Socratic gate |
| `intelligent-routing` | Agent selection |
| `api-patterns` | FastAPI design |
| `database-design` | PostgreSQL schema |
| `architecture` | System design |
| `architecture-explorer` | Codebase understanding |
| `data-analysis` | Influencer analytics |
| `cold-email` | Outreach templates |
| `content-strategy` | Campaign content planning |
| `copywriting` | Outreach message writing |
| `marketing-psychology` | Persuasion tactics |
| `marketing-ideas` | Growth strategies |
| `deployment-procedures` | CI/CD |
| `cloud-infrastructure` | GCP/Hetzner setup |
| `lint-and-validate` | Code quality |
| `code-review-checklist` | PR standards |
| `github-mcp` | GitHub workflow |
| `executing-plans` | Task execution |
| `plan-writing` | Task documents |
| `behavioral-modes` | Agent behavior |
| `documentation-templates` | Doc standards |
| `nextjs-react-expert` | Dashboard development |
| `performance-profiling` | Optimization (Phase 3) |
| `langgraph-fundamentals` | Agent architecture |
| `langgraph-human-in-the-loop` | Approval gates |
| `langgraph-persistence` | Agent state management |
| `deep-agents-core` | Multi-agent patterns |
| `deep-agents-orchestration` | Agent coordination |
| `parallel-agents` | Concurrent agent execution |
| `ml-engineer` | Influencer scoring models |

### 4.2 Skills to REMOVE (Not relevant)

| Skill | Reason |
|-------|--------|
| `game-development` | Not applicable |
| `mobile-design` | No mobile app |
| `petpooja-management` | POS-specific |
| `envato-template-selection` | Template marketplace |
| `business-listing` | GMB listings (keep local-seo instead) |
| `geo-fundamentals` | Geographic mapping |
| `form-cro` | Form optimization |
| `page-cro` | Landing page CRO |
| `onboarding-cro` | SaaS onboarding |
| `paywall-upgrade-cro` | Paywall optimization |
| `churn-prevention` | SaaS churn (Phase 4+) |
| `ab-test-setup` | A/B testing (Phase 4+) |
| `wordpress-*` skills | Phase 3+ only |
| `i18n-localization` | Hindi/English later |

### 4.3 NEW Skills to Create

| New Skill | Description | Used By |
|-----------|-------------|---------|
| `instagram-dm-outreach` | Instagrapi wrapper patterns, DM sequencing, rate limiting, session rotation, challenge handling | `influencer-outreach-agent` |
| `proxy-management` | Proxy rotation strategy, IP binding per IG account, health monitoring | `influencer-outreach-agent` |
| `whatsapp-automation` | Evolution API integration patterns, drip sequences, multi-number management, Chatwoot routing | `brand-outreach-agent` |
| `influencer-scoring` | Engagement rate calculation, fake follower detection, niche classification, tier assignment | `campaign-orchestrator-agent` |
| `campaign-lifecycle` | Temporal workflow patterns for multi-day campaigns, deal type handling (barter/paid/hybrid) | `campaign-orchestrator-agent` |
| `crm-pipeline` | Pipeline stage management, lead routing, status transitions for brands + influencers | `brand-outreach-agent`, `influencer-outreach-agent` |
| `sentiment-classification` | NLP intent detection for DM/WhatsApp replies — positive, negative, spam, price_objection | `brand-outreach-agent`, `influencer-outreach-agent` |
| `web-scraping` | Firecrawl + custom scraping patterns for GMB, Zomato, IG bio extraction | `data-scraper-agent` |

---

## 5. Workflows Mapping

### 5.1 Existing Workflows to KEEP

| Workflow | Use Case |
|----------|----------|
| `orchestrate.md` | Multi-agent task orchestration |
| `plan.md` | Project planning methodology |
| `create.md` | Feature creation flow |
| `create-prd.md` | PRD writing workflow |
| `debug.md` | Bug investigation |
| `deploy.md` | Deployment procedures |
| `code-review.md` | PR review process |
| `code-review-fix.md` | Post-review fix flow |
| `brainstorm.md` | Ideation sessions |
| `enhance.md` | Feature enhancement |
| `implement-fix.md` | Bug fix implementation |
| `test.md` | Test execution |
| `validate.md` | Validation checks |
| `update.md` | Update procedures |
| `status.md` | Project status reporting |
| `system-check.md` | System health checks |
| `piv-plan.md` | PIV planning |
| `piv-execute.md` | PIV execution |
| `piv-prime.md` | PIV priming |
| `execution-report.md` | Post-execution reporting |
| `preview.md` | Feature preview |
| `visualize-arch.md` | Architecture visualization |

### 5.2 Workflows to REMOVE

| Workflow | Reason |
|----------|--------|
| `build-website.md` | WordPress-specific (Phase 3+) |
| `local-seo-setup.md` | Upsell service (Phase 3+) |
| `menu-sync.md` | POS-specific |
| `setup-listing.md` | GMB listing (Phase 3+) |
| `client-verify.md` | Client onboarding (Phase 3+) |
| `audit-goals.md` | Business audit (Phase 3+) |
| `system-review.md` | System-wide review (Phase 3+) |
| `rca.md` | Root cause analysis (Phase 3+) |
| `ui-ux-pro-max.md` | Deep UI/UX (Phase 3+) |

### 5.3 NEW Workflows to Create

| New Workflow | Steps | Trigger |
|-------------|-------|---------|
| `influencer-discover.md` | 1. Scrape IG by hashtag/location → 2. Extract profile data → 3. Calculate engagement → 4. Classify niche → 5. Assign tier → 6. Store in DB | `/discover-influencers` |
| `influencer-outreach.md` | 1. Select target batch → 2. Generate personalized DM → 3. Send via instagrapi (rate-limited) → 4. Track delivery → 5. Monitor replies → 6. Classify sentiment → 7. Route positives to onboarding | `/outreach-influencers` |
| `influencer-onboard.md` | 1. Initiate chat → 2. Collect data conversationally (name, location, rate, niche) → 3. Enrich profile → 4. Assign gamification tier → 5. Store in CRM | `/onboard-influencer` |
| `brand-outreach.md` | 1. Scrape GMB/Zomato leads → 2. Generate hook messages → 3. Send WhatsApp drip (Day 1/2/3) → 4. Classify replies → 5. Route positives to Chatwoot | `/outreach-brands` |
| `campaign-execute.md` | 1. Match influencers to brand → 2. Send outreach → 3. Coordinate visit → 4. Content QC review → 5. Posting confirmation → 6. Payment settlement → 7. Generate report | `/execute-campaign` |
| `enrichment-loop.md` | 1. Select stale profiles → 2. Re-scrape IG data → 3. Update engagement/follower metrics → 4. Detect tier changes → 5. Flag inactive creators | `/enrich-profiles` |

---

## 6. Scripts Mapping

### 6.1 Existing Scripts to KEEP

| Script | Purpose |
|--------|---------|
| `checklist.py` | Project audit checklist |
| `verify_all.py` | Validation runner |
| `verify_boundary.py` | Boundary verification |
| `session_manager.py` | Session management |
| `email_system.py` | Email sending |

### 6.2 Scripts to REMOVE

| Script | Reason |
|--------|--------|
| `auto_preview.py` | WordPress preview |
| `process_brand.py` | POS brand processing |
| `process_intake.py` | POS intake |
| `setup_folders.py` | POS folder setup |
| `wordpress_checklist.py` | WordPress-specific |
| `zomato_playwright.py` | Keep reference but rewrite for scraping |

### 6.3 NEW Scripts to Create

| New Script | Purpose |
|-----------|---------|
| `ig_session_manager.py` | Multi-account instagrapi session persistence, proxy binding, health checks |
| `ig_dm_sender.py` | Rate-limited DM sending with retry logic, challenge handling |
| `ig_profile_scraper.py` | Bulk profile scraping — followers, engagement, bio, recent posts |
| `whatsapp_drip.py` | Evolution API drip sequence runner with template variables |
| `lead_scraper.py` | GMB + Zomato restaurant scraping with dedup |
| `engagement_calculator.py` | Compute engagement rates, fake follower detection |
| `sentiment_classifier.py` | NLP reply classification (positive/negative/spam/objection) |
| `crm_pipeline_sync.py` | Sync pipeline stages between Google Sheets ↔ PostgreSQL |

---

## 7. Influencer Branch Plan for `vibmedia-agent-skills`

### 7.1 Branch Structure

```
Branch: influencer
Base: main

vibmedia-agent-skills/
├── .shared/                    # ✅ Keep (shared utilities)
├── ARCHITECTURE.md             # ✏️ Modify (add influencer project context)
├── GUIDE.md                    # ✅ Keep
├── INSTALL.md                  # ✏️ Modify (add instagrapi, evolution-api deps)
├── README.md                   # ✏️ Modify (rebrand for influencer project)
├── ROADMAP.md                  # ✏️ Modify (align with PRD phases)
├── agents/                     # 🔻 Trim from 25 → 14 + 4 new = 18
│   ├── orchestrator.md         # ✅ Keep
│   ├── project-planner.md      # ✅ Keep
│   ├── backend-specialist.md   # ✅ Keep
│   ├── frontend-specialist.md  # ✅ Keep
│   ├── database-architect.md   # ✅ Keep
│   ├── debugger.md             # ✅ Keep
│   ├── devops-engineer.md      # ✅ Keep
│   ├── code-reviewer.md        # ✅ Keep
│   ├── documentation-writer.md # ✅ Keep
│   ├── product-manager.md      # ✅ Keep
│   ├── product-owner.md        # ✅ Keep
│   ├── security-auditor.md     # ✅ Keep
│   ├── test-engineer.md        # ✅ Keep
│   ├── explorer-agent.md       # ✅ Keep
│   ├── influencer-outreach-agent.md  # 🆕 NEW
│   ├── brand-outreach-agent.md       # 🆕 NEW
│   ├── data-scraper-agent.md         # 🆕 NEW
│   └── campaign-orchestrator-agent.md # 🆕 NEW
├── skills/                     # 🔻 Trim from 60+ → ~35 + 8 new = ~43
│   ├── [kept skills listed in §4.1]
│   ├── instagram-dm-outreach/  # 🆕 NEW
│   ├── proxy-management/       # 🆕 NEW
│   ├── whatsapp-automation/    # 🆕 NEW
│   ├── influencer-scoring/     # 🆕 NEW
│   ├── campaign-lifecycle/     # 🆕 NEW
│   ├── crm-pipeline/           # 🆕 NEW
│   ├── sentiment-classification/ # 🆕 NEW
│   └── web-scraping/           # 🆕 NEW
├── workflows/                  # 🔻 Trim from 25 → 15 + 6 new = 21
│   ├── [kept workflows listed in §5.1]
│   ├── influencer-discover.md  # 🆕 NEW
│   ├── influencer-outreach.md  # 🆕 NEW
│   ├── influencer-onboard.md   # 🆕 NEW
│   ├── brand-outreach.md       # 🆕 NEW
│   ├── campaign-execute.md     # 🆕 NEW
│   └── enrichment-loop.md      # 🆕 NEW
├── scripts/                    # 🔻 Trim from 11 → 5 + 8 new = 13
│   ├── [kept scripts listed in §6.1]
│   ├── ig_session_manager.py   # 🆕 NEW
│   ├── ig_dm_sender.py         # 🆕 NEW
│   ├── ig_profile_scraper.py   # 🆕 NEW
│   ├── whatsapp_drip.py        # 🆕 NEW
│   ├── lead_scraper.py         # 🆕 NEW
│   ├── engagement_calculator.py # 🆕 NEW
│   ├── sentiment_classifier.py # 🆕 NEW
│   └── crm_pipeline_sync.py   # 🆕 NEW
├── profiles/                   # ✅ Keep
├── brands/                     # ✅ Keep (repurpose for restaurant brands)
├── rules/                      # ✅ Keep
├── docs/                       # ✅ Keep
├── mcp_config.json             # ✏️ Modify (add instagrapi MCP if applicable)
├── telos.md                    # ✏️ Modify (influencer project mission)
└── vib.md                      # ✅ Keep
```

### 7.2 Summary of Changes

| Category | Original (main) | Influencer Branch | Delta |
|----------|-----------------|-------------------|-------|
| **Agents** | 25 | 18 (14 kept + 4 new) | -7 removed, +4 created |
| **Skills** | 60+ | ~43 (35 kept + 8 new) | -25 removed, +8 created |
| **Workflows** | 25 | 21 (15 kept + 6 new) | -10 removed, +6 created |
| **Scripts** | 11 | 13 (5 kept + 8 new) | -6 removed, +8 created |

---

## 8. Knowledge Questions to Resolve Before Building

### 8.1 Instagram / Instagrapi

1. What is the **current ban risk** with instagrapi DMs in 2026? Is the library still maintained?
2. How many **proxy IPs** are needed? (Model: 1 proxy per 2 IG accounts)
3. Should we use **instagrapi REST API wrapper** (Docker) or the Python library directly?
4. What **fallback** do we use if instagrapi accounts get banned? (Socionator is manual/cold-email only)

### 8.2 Architecture

5. Do we start with **Google Sheets CRM** (Phase 1) or jump directly to PostgreSQL?
6. Where does the **LangGraph agent state** persist — SQLite, Redis, or PostgreSQL?
7. Should the **Temporal workflow engine** be introduced in Phase 2 or deferred to Phase 3?
8. Do we need a **message queue** (Redis/RabbitMQ) between agents, or direct function calls?

### 8.3 Operations

9. How many **WhatsApp numbers** to start with for manual outreach?
10. What is the **Google Sheets schema** for Phase 1 CRM? (Separate tabs: brands, influencers, job board)
11. How do we handle **influencer data privacy** (DPDPA compliance for India)?
12. What **LLM** for Phase 1 outreach messages? (Gemini 2.0 Flash with $300 GCP credit?)

### 8.4 Job Board & Points System

13. What is the **minimum viable Job Board** — Google Sheet tab, simple web form, or full dashboard?
14. How are **points values** calibrated? (Quick Review = 10pts, Full Collab = 50pts — is this balanced?)
15. Should **brands rate influencers** AND vice versa (mutual rating), or only one-way?

---

## 9. Next Steps

1. **Create `influencer` branch** in `vibmedia/vibmedia-agent-skills` repo
2. **Remove** irrelevant agents, skills, workflows, scripts
3. **Create stubs** for the 4 new agents, 8 new skills, 6 new workflows, 8 new scripts
4. **Install** the trimmed `.agent` into `d:\nara\projects\ai influencer\.agent\`
5. **Resolve** knowledge questions in §8 before implementation begins
6. **Start Phase 1** with: influencer discovery → IG DM outreach → brand scraping → WhatsApp drip
