# Product Requirements Document (PRD)

**Project:** VibMedia — AI Influencer Marketing Agency (Kobi)
**Version:** 2.0
**Date:** 2026-03-27
**Author:** VibMedia Team
**Status:** Draft → In Review
**Scope:** Phase 1 Launch (Month 1-2)

> **Key Assumptions:**
> - All raw data (influencer + brand lists) is **provided manually** — no automated scraping
> - AI outreach (IG DMs) uses **instagrapi** only — Socionator is manual/cold-email fallback only
> - WhatsApp brand outreach is **manual** — templates + CRM tracking
> - Proxy allocation: **1 proxy per 2 IG accounts**
> - Upsell services (WordPress, Meta Ads) are **manual** — not part of AI system

---

## 1. Executive Summary

VibMedia is an AI-assisted influencer marketing agency where AI agents automate IG DM outreach, onboarding, and profile enrichment under human supervision. Brand outreach (WhatsApp) remains manual. Starting with Delhi NCR restaurants and food creators, the agency uses a Job Board model where brands post campaigns and influencers self-select based on a points-based reputation system.

**Key Differentiators:**
- AI agents handle IG outreach, onboarding conversations, and data enrichment
- Job Board: brands post jobs → influencers apply → ops approves → campaign executes
- Points-based reputation system (replaces free-deal obligations)
- WhatsApp-first manual brand outreach + IG DM AI influencer outreach
- Conversational AI onboarding via IG DMs (instagrapi)

---

## 2. Target Users

| User | Description | Pain Point Solved |
|---|---|---|
| **Restaurant owners** (Delhi NCR) | SMBs wanting more footfall, brand awareness | Don't know how to find or manage influencers |
| **Food influencers** (1K-200K+ followers) | Content creators seeking brand deals | Inconsistent deal flow, no centralized marketplace |
| **VibMedia operations team** | Internal — managing campaigns | Manual matching, outreach, tracking is unsustainable |

---

## 3. Business Model

### 3.1 Revenue Streams

| Stream | Source | Amount |
|---|---|---|
| Campaign fee | Brand pays per influencer | ₹2K-₹1L+ (varies by tier) |
| Commission | 20% of influencer deal value | Deducted from influencer payout |
| Website upsell | Single-page WordPress site (⚠️ manual) | ₹5,000 one-time |
| Ads upsell | Meta Ads management (⚠️ manual) | ₹5,000 + ad spend |

### 3.2 Collaboration Types

| Type | Product Handling | Payment | When Used |
|---|---|---|---|
| **Barter** | Influencer keeps (meal/product) | None | Nano/Micro, brand intro |
| **Paid** | Influencer returns product | After draft approved | Costly products |
| **Paid + Barter** | Influencer keeps | After draft approved | Mid-tier+ influencers |
| **Performance** | Varies | Reduced/none | Top-tier, guaranteed ad spend |

### 3.3 Influencer Tiers & Pricing

| Tier | Followers | Typical Rate | Deal Preference |
|---|---|---|---|
| Nano | 1K – 10K | Barter only | Barter |
| Micro | 10K – 50K | ₹2K – ₹8K | Barter or Paid |
| Mid | 50K – 200K | ₹8K – ₹25K | Paid or Paid+Barter |
| Macro | 200K+ | ₹25K – ₹1L+ | Paid, Paid+Barter, Performance |

---

## 4. Core AI System

### 4.1 AI Agent: Influencer Outreach & Onboarding

**Purpose:** Send DMs to imported influencers via instagrapi → classify replies → conversationally collect data → enrich profile → store in database.

**Capabilities:**
- Send DM sequences via **instagrapi** (Day 1: 5, Day 2: 6, Day 3: 8 DMs per account)
- Classify reply sentiment (positive / negative / spam / question / onboarding_data)
- Run conversational onboarding via IG DM chat (NOT forms)
- Auto-enrich imported profiles: follower count, engagement rate, niche, content quality
- Extract bio data: email, Linktree, YouTube links
- Assign follower tier (Nano/Micro/Mid/Macro) + points-based reputation tier
- Periodic re-enrichment (detect tier changes, flag inactive creators)
- Fallback: Google Form link if influencer requests it

> ⚠️ **NOT agent scope:** Raw data sourcing (all raw data will be provided), WhatsApp outreach (manual), scraping (none)

**Data Collected (via conversation):**

| Field | Method | Required |
|---|---|---|
| Full Name | Enriched from IG profile | ✅ |
| Location / Area | "What area are you based in?" | ✅ |
| Phone (WhatsApp) | "Phone number for deals?" | ✅ |
| Manager Contact | "Do you have a manager?" | ❌ |
| Deal Preference | "Barter, paid, or both?" | ✅ |
| Rate per Reel (₹) | "What's your usual rate?" | ✅ |
| Industry/Niche | Auto-classified by AI from bio + posts | ✅ |
| Content Formats | "Reels, stories, or YT shorts?" | ❌ |
| UPI ID | "UPI for payments?" | ❌ |
| Address | "Address for product shipments?" | ❌ |

**Human Escalation:**
- If influencer raises rate post-onboarding → human negotiates
- If DM flagged as spam or angry → stop outreach immediately

### 4.2 AI Agent: Campaign Orchestrator

**Purpose:** Match influencers to jobs → coordinate visit → QC content → award points → handle payment → generate report.

**Capabilities:**
- Match influencers to brand job postings (niche, tier, location, points, deal type)
- Generate influencer shortlists for brand approval
- Content QC review (hashtags, brand mention, duration, tone)
- Award points after job completion
- Trigger tier upgrades when thresholds crossed
- Generate campaign completion reports

**Human Gates:**
- All deal closings go through human
- Deals > ₹50K require explicit human approval
- Content QC: AI first pass → human reviews flagged items

### 4.3 What Is NOT Part of AI Agents

| Activity | Method | Why Not AI |
|---|---|---|
| Raw data sourcing | Manual import (CSV/Sheets) | Data is provided by ops team |
| Upsell services (WordPress, Meta Ads) | Manual delivery | Low volume, custom work |
| Deal negotiation & closing | Human via Chatwoot | Trust-building, complex pricing |

### 4.4 AI Agent: WhatsApp Hybrid Outreach (NEW)

**Purpose:** Send personalized initial WhatsApp messages to restaurant brands using enriched data, then hand over to human sales for complex conversations and deal closing.

**Capabilities:**
- Generate personalized Day 1 message using {restaurant_name}, {popular_dish}, {area}, {cuisine}
- Send 3-day drip sequence via Evolution API (Day 1: custom hook, Day 2: social proof, Day 3: free deal offer)
- Classify incoming replies (positive, neutral, negative, price_objection, already_doing)
- Auto-answer simple FAQ questions ("how does it work?", "what is the cost?")
- Route all interested/complex/negotiation replies to Chatwoot for human handover
- Log all conversations in CRM

**Human Gates:**
- All deal-related conversations → human via Chatwoot
- Price negotiations → human always
- Brand expresses interest → human qualifies and closes

> ⚠️ **NOT agent scope:** Deal negotiation, pricing discussions, closing, contract terms — all handled by human sales team via Chatwoot

---

## 5. Job Board System

> 🆕 Core feature — brands post jobs, influencers self-select.

### 5.1 How It Works

```
Brand/Ops creates job posting → Eligible influencers see job → 
Influencer applies → Ops reviews applications → Accepts/Rejects →
Accepted influencer executes campaign → Points awarded on completion
```

### 5.2 Job Types

| Job Type | Deliverables | Points Awarded | Typical Budget |
|---|---|---|---|
| Quick Review | 1 story or short visit | 10 pts | Barter only |
| Standard Collab | 1 reel + stories | 25 pts | ₹2K-₹8K |
| Full Campaign | Reel + stories + post | 50 pts | ₹8K-₹25K |
| Long-term Partnership | Multi-month, multiple posts | 100 pts | ₹25K+ |

### 5.3 Job Posting Fields

- Brand name, location, cuisine type
- Job type, deal type (Barter/Paid/Paid+Barter/Performance)
- Deliverables checklist
- Budget/barter details
- Required niche, tier range, minimum points
- Expiry date (7/14/30 days)
- Points awarded

---

## 6. Points & Reputation System

> 🆕 Replaces the free-deal obligation model. Influencers earn points from jobs, building visible reputation.

### 6.1 Points Earning

| Job Type | Points |
|---|---|
| Quick Review | 10 |
| Standard Collab | 25 |
| Full Campaign | 50 |
| Long-term Partnership | 100 |

### 6.2 Reputation Tiers (Points-Based)

| Tier | Points Required | Perks |
|---|---|---|
| 🥉 Bronze | 0-49 (newly onboarded) | Basic job board access |
| 🥈 Silver | 50-199 | Priority matching, profile badge |
| 🥇 Gold | 200-499 | VIP brand access, featured in recommendations |
| 💎 Platinum | 500+ | Premium brands, negotiation autonomy, top listing |

### 6.3 Mutual Rating System

- Brand rates influencer (1-5 stars) after job completion
- Influencer rates brand/restaurant (1-5 stars) after job completion
- Average rating displayed on profiles
- Rating < 3 stars triggers review

### 6.4 Why Points > Free-Deal Obligations

| Free Deal Model (Old) | Points Model (New) |
|---|---|
| Forced obligation → resentment | Voluntary jobs → earned reputation |
| Puts pressure on us to find deals for influencers | Influencers self-select jobs they want |
| Binary (did/didn't fulfill) | Granular (10-100pt range from small to big jobs) |
| Only Silver+ does free work | Everyone earns from day 1 |

---

## 7. CRM Pipelines

### 7.1 Brand Pipeline (11 stages)

```
Raw Lead → Contacted → Follow-up 1 → Follow-up 2 → Interested
→ Qualifying → Free Trial → Onboarded → Active → Dormant → Blacklisted
```

### 7.2 Influencer Pipeline (11 stages)

```
Imported → DM Sent → Follow-up → Replied → Onboarding Chat
→ Onboarded → Available → On Campaign → Completed → Idle → Cold
```

**Phase 1:** Google Sheets (separate tabs: Brands, Influencers, Job Board, Dashboard).
**Phase 2+:** PostgreSQL + custom dashboard.

---

## 8. Account & Proxy Management

### 8.1 IG Account Setup

| Item | Configuration |
|---|---|
| Outreach accounts | 3-5 IG accounts with recruiter personas |
| Proxy allocation | **1 proxy per 2 outreach accounts** |
| Session management | instagrapi `dump_settings()`/`load_settings()` |
| Warmup protocol | Week 1: follows/likes → Week 2: comments → Week 3: DMs start |

### 8.2 Account Management Dashboard

- All IG accounts listed with: username, persona, proxy group, status, DMs sent today
- Per-account settings: DM rate limit, warmup stage, auto-pause triggers
- Session status: logged in / expired / needs re-auth
- Proxy health: up / down / slow

### 8.3 Recruiter Personas

Each IG outreach account styled as a recruiter (e.g., "Selena - VibMedia Recruiter"):
- Named persona with unique profile photo, bio, display name
- Persona context maintained across DM conversations
- Warmup as real account before DM outreach begins

---

## 9. Outreach Channels & Tooling

| Channel | Target | Tool | Method | Rate Limit |
|---|---|---|---|---|
| **IG DMs** | Influencers | **instagrapi** | AI-automated | 5→6→8 DMs/day/account, ramping |
| **WhatsApp** | Brands | Evolution API + Chatwoot | **Manual** | 5-10 msgs/number/day × 4-5 numbers |
| **Cold Email** | Influencer fallback | Socionator (**manual**) | **Manual** | When IG DMs fail, email available |

---

## 10. Data Import & Enrichment

> ⚠️ **No automated scraping.** All raw data is provided manually by the ops team.

### 10.1 Data Sources (Manual Import)

| Data | Source | Format |
|---|---|---|
| **Influencer profiles** | Manually sourced IG handles | CSV / Google Sheets |
| **Brand/restaurant data** | Manually sourced from GMB/Zomato | CSV / Google Sheets |

### 10.2 AI Enrichment (Automated)

| Data Point | Method | Tool |
|---|---|---|
| Follower count, post count | instagrapi `user_info()` | Automated |
| Average likes, comments | instagrapi `user_medias()` (last 12 posts) | Automated |
| Bio email, links | NLP extraction from bio text | Automated |
| Engagement rate | (avg likes + comments) / followers × 100 | Automated |
| Niche classification | AI classification from bio + captions | Automated |
| Quality score (0-100) | Composite of engagement, consistency, authenticity | Automated |
| Phone validation | 10-digit Indian mobile format check | Automated |

---

## 11. Database Schema

### 11.1 Core Tables

| Table | Purpose | Key Fields |
|---|---|---|
| `influencers` | Creator profiles + points/tier | handle, tier, points, reputation_tier, base_rate, quality_score |
| `brands` | Restaurant/business profiles | brand_type, industry, pipeline_status |
| `campaigns` | Active campaigns | brand_id, deal_type, status |
| `campaign_influencers` | Influencer-campaign assignments | deal_amount, commission, visit_date, qc_score, posted_url |
| `job_postings` | Job board listings | brand_id, job_type, deal_type, points_awarded, status, expiry_date |
| `job_applications` | Influencer applications to jobs | influencer_id, job_id, status (applied/accepted/rejected/completed) |
| `points_ledger` | Points history per influencer | influencer_id, job_id, points, action, timestamp |
| `ig_accounts` | IG outreach account management | username, persona_name, proxy_group_id, status, warmup_stage |
| `proxy_groups` | Proxy-to-account bindings | proxy_ip, account_1_id, account_2_id, health_status |
| `outreach_messages` | All DM outreach tracking | channel, drip_step, ig_account_id, status |
| `ratings` | Mutual ratings (brand↔influencer) | rater_type, ratee_id, stars, job_id |
| `agent_logs` | AI agent activity | agent_name, llm_model, tokens_used, cost_usd |
| `invoices` | Brand billing | amount, service_type |
| `influencer_payouts` | Creator payments | gross_amount, commission_deducted, net_amount, upi_id |

---

## 12. Tech Stack

| Layer | Technology | Rationale |
|---|---|---|
| AI Agents | LangGraph (Python) | Stateful graphs, human-in-the-loop gates |
| LLM | Google Gemini 2.0 Flash/Pro | $300 GCP free credit |
| LLM (backup) | Llama 3 70B (Ollama) | Free after credits exhaust |
| Backend | FastAPI + Celery | Python-native, async |
| Frontend | Next.js 15 (TypeScript) | Dashboard → portal → SaaS |
| Database | PostgreSQL 16 | Relational campaign data |
| Vector DB | Qdrant | Semantic influencer search |
| Workflow | Temporal | Campaign lifecycle (days/weeks) |
| WhatsApp | Evolution API + Chatwoot | ⚠️ Manual outreach — templates + tracking |
| IG DMs | **instagrapi** + proxies | AI-automated DM sequences |
| IG fallback | Socionator | ⚠️ Manual — cold email only |
| Data sourcing | Manual import (CSV/Sheets) | No scraping — ops team provides data |
| Enrichment | instagrapi + NLP | AI enriches imported profiles |
| WordPress | Existing MCP | ⚠️ Manual upsell only |
| Payments | UPI → Razorpay | Start free, formalize later |
| Hosting | GCP ($300) → Hetzner | Free start, cheap scale |

---

## 13. Infrastructure — GCP ($300 Credits)

| Resource | Spec | Monthly Cost |
|---|---|---|
| VM 1 (App Server) | e2-standard-2, 2 vCPU, 8GB | ~$50/mo |
| VM 2 (Services) | e2-standard-2, 2 vCPU, 8GB | ~$50/mo |
| PostgreSQL | Self-hosted on VM 2 | $0 |
| Gemini API | Flash + Pro | ~$15-30/mo |
| Proxies | 2-3 residential (1 per 2 accounts) | ~$10/mo |
| **Total** | | **~$135/mo (lasts ~2.2 months)** |

After credits: Migrate to Hetzner (€12-25/mo).

---

## 14. Phase 1 Launch Scope (Month 1-2)

> **Focus: Get first 3-5 paying restaurant clients using semi-automated pipeline.**

### What's IN Phase 1

- [x] Influencer database (manual import 200-300 Delhi NCR food creators)
- [ ] AI enrichment of imported profiles (instagrapi)
- [ ] IG DM outreach (instagrapi + 3-5 accounts + recruiter personas)
- [ ] Conversational AI onboarding via IG DM
- [ ] Job Board (Google Sheets or simple web form)
- [ ] Points system + tier tracking
- [ ] Account & proxy management dashboard
- [ ] Google Sheets CRM (brand + influencer + job board tabs)
- [ ] WhatsApp brand outreach (manual, with templates)
- [ ] Campaign execution tracking
- [ ] Payment & commission tracking
- [ ] Daily/weekly reporting
- **Revenue Target:** ₹50K-₹1.5L/mo from 3-5 restaurant deals

### What's NOT in Phase 1

- ❌ Automated scraping (any kind)
- ❌ AI-powered WhatsApp outreach
- ❌ WordPress website upsell (manual if sold)
- ❌ Meta Ads management upsell (manual if sold)
- ❌ Full Next.js dashboard (Google Sheets first)
- ❌ Temporal workflow engine (manual tracking first)
- ❌ Qdrant vector search (spreadsheet filters first)
- ❌ Contract generation / e-signatures
- ❌ Razorpay integration (UPI manual first)

### Phase 2-3 Preview (Not in Scope — For Reference Only)

| Phase | Focus | Revenue Target |
|---|---|---|
| Phase 2 (Month 3-5) | Full LangGraph agents, Next.js dashboard, Temporal workflows, auto-enrichment | ₹3-5L/mo |
| Phase 3 (Month 6-9) | Client portal, upsell modules, contract gen, Razorpay, multi-industry | ₹8-15L/mo |

---

## 15. Success Metrics (Phase 1 Only)

| Metric | Phase 1 Target |
|---|---|
| Active Brands | 3-5 |
| Active Influencers (onboarded) | 100-200 |
| Monthly Revenue | ₹50K-₹1.5L |
| IG DM Response Rate | 5-10% |
| Job Board Fill Rate | 50%+ |
| Points Earned (total across influencers) | 500+ |
| Campaign Completion Rate | 70% |
| Infrastructure Cost | ₹0 (GCP credits) |

---

## 16. Risks & Mitigations (Phase 1)

| Risk | Impact | Mitigation |
|---|---|---|
| IG account bans (instagrapi) | Lose influencer outreach | 1 proxy per 2 accounts, ramp slowly, warmup protocol, backup accounts |
| WhatsApp bans (Evolution API) | Lose brand outreach | Manual sending, rate limit 5-10/day, warm up numbers |
| Influencers ghost after onboarding | Empty database | Points incentive, re-engagement DMs, job board notifications |
| Brands don't convert from free trial | Revenue loss | Limit free trials to 1 per brand, track conversion rate |
| LLM hallucinations in DM outreach | Bad reputation | Human review first 10 msgs (calibration), daily audit |
| GCP credits exhaust | Infrastructure down | Plan Hetzner migration at $200 remaining |
| Job Board has no takers | Dead marketplace | Seed with barter-only Quick Review jobs, manually invite influencers |
| Points gaming | Inflated reputation | Points only awarded after ops-confirmed completion |

---

## Appendix A: Local Brands vs E-Commerce

| Aspect | Local Brands (Phase 1-3) | E-Commerce (Phase 4+) |
|---|---|---|
| Example | Restaurants, clinics, gyms | Amazon sellers, D2C brands |
| Logistics | Influencer walks in, shoots | Product shipping + returns |
| Complexity | Low — no product handling | High — shipping, GST, addressing |
| Volume | 3-15 influencers/campaign | 100-10,000 influencers/campaign |
| **Status** | **Starting here** | **Later phase** |

## Appendix B: Key Operational Decisions

| Decision | Choice | Rationale |
|---|---|---|
| Forms vs Chat onboarding | **Chat** (forms = backup) | Influencers hate forms, conversational has higher completion |
| WhatsApp Business API vs Evolution API | **Evolution API** | Free vs ₹0.80/msg |
| IG DM tool | **instagrapi** (Socionator = manual email only) | Free, full API control, Python-native |
| Incentive model | **Points system** (not free-deal obligations) | Voluntary, scalable, visible reputation |
| Proxy model | **1 proxy per 2 accounts** | Cost-effective while maintaining safety |
| Data sourcing | **Manual import** (no scraping) | Ops team provides curated lists |
| Brand outreach | **Manual WhatsApp** | Relationship-driven, needs human judgment |
| Pricing model | **Charge brand, take 20% from influencer** | Standard agency model |
| Payment (Phase 1) | **UPI** | Zero cost, universal in India |
| Hosting (Phase 1) | **GCP ($300 credit)** | Free tier, good specs |
