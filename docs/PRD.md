# Product Requirements Document (PRD)

**Project:** VibMedia — AI Influencer Marketing Agency  
**Version:** 1.0  
**Date:** 2026-03-19  
**Author:** VibMedia Team  
**Status:** Draft → Approved  
**Scope:** Phase 1-3 P0 Features  

---

## 1. Executive Summary

VibMedia is an AI-first influencer marketing agency where two core AI agents automate outreach, onboarding, and campaign execution under human supervision. Starting with Delhi NCR restaurants and food creators, the agency will scale to multi-industry/multi-location before productizing into a SaaS platform.

**Key Differentiators:**
- AI agents handle 80% of operations (outreach, onboarding, matching, content QC)
- WhatsApp-first brand outreach + IG DM influencer outreach
- Conversational AI onboarding (no forms)
- Free-deal hook + influencer gamification tiers for growth
- Integrated upsells (website + ads) beyond just influencer matching

---

## 2. Target Users

| User | Description | Pain Point Solved |
|---|---|---|
| **Restaurant owners** (Delhi NCR) | SMBs wanting more footfall, brand awareness | Don't know how to find or manage influencers |
| **Food influencers** (1K-200K+ followers) | Content creators seeking brand deals | Inconsistent deal flow, no centralized platform |
| **VibMedia operations team** | Internal — managing campaigns | Manual matching, outreach, tracking is unsustainable |

---

## 3. Business Model

### 3.1 Revenue Streams

| Stream | Source | Amount |
|---|---|---|
| Campaign fee | Brand pays per influencer | ₹2K-₹1L+ (varies by tier) |
| Commission | 20% of influencer deal value | Deducted from influencer payout |
| Website upsell | Single-page WordPress site | ₹5,000 one-time |
| Ads upsell | Meta Ads management | ₹5,000 + ad spend (₹5K-₹10K) |

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

## 4. Core System — Two AI Agents

### 4.1 Agent 1: Influencer Onboarding Agent

**Purpose:** Find influencers → reach out via IG DM → conversationally collect data → enrich profile → store in database.

**Capabilities:**
- Scrape IG/YT bios for email, links, manager contact
- Send DM sequences via Socionator (Day 1: 5, Day 2: 6, Day 3: 8 DMs)
- Classify reply sentiment (positive / negative / spam)
- Run conversational onboarding via chat (NOT forms)
- Auto-enrich: follower count, engagement rate, niche, content quality
- Assign follower tier (Nano/Micro/Mid/Macro) + gamification tier (Bronze→Platinum)
- Monthly re-enrichment loop (detect tier changes, flag inactive creators)
- Fallback: Google Form link if influencer requests it

**Data Collected (via conversation):**

| Field | Method | Required |
|---|---|---|
| Full Name | Auto-scraped from IG | ✅ |
| Location / Area | "What area are you based in?" | ✅ |
| Phone (WhatsApp) | "Phone number for deals?" | ✅ |
| Manager Contact | "Do you have a manager?" | ❌ |
| Deal Preference | "Barter, paid, or both?" | ✅ |
| Rate per Reel (₹) | "What's your usual rate?" | ✅ |
| Industry/Niche | Auto-classified by AI | ✅ |
| Content Formats | "Reels, stories, or YT shorts?" | ❌ |
| UPI ID | "UPI for payments?" | ❌ |
| Address | "Address for product shipments?" | ❌ |

**Human Escalation:**
- If influencer raises rate post-onboarding → human negotiates
- If DM flagged as spam or angry → stop outreach immediately

### 4.2 Agent 2: Deal-to-Booking Agent (Brand Agent)

**Purpose:** Scrape brand leads → WhatsApp drip sequence → qualify → route to human for close → execute campaign.

**Capabilities:**
- Scrape GMB + Zomato for restaurant data (name, phone, address, cuisine, menu, rating)
- Generate custom hook messages referencing brand's specific menu/specialty
- Send WhatsApp drip via Evolution API (Day 1 hook → Day 2 social proof → Day 3 free deal)
- NLP intent classification on replies (positive / neutral / negative / price_objection)
- Route positive leads to Chatwoot unified inbox for human close
- Blacklist large brands who refuse fair pricing (with reason tracked)
- Offer "free deal" hook using Silver+ tier influencers with free-deal obligations

**Drip Sequence:**

| Day | Template | Hook |
|---|---|---|
| 1 | "We noticed your {dish} on Zomato... interested in a food reel?" | Custom, reference specific menu item |
| 2 | "{nearby_restaurant} got 45K views from our campaign..." | Social proof from local competitor |
| 3 | "FREE influencer visit — creator comes, eats, shoots. No cost." | Free-deal hook (barter) |

**Human Gates:**
- All deal closings go through human (Chatwoot)
- Deals > ₹50K require explicit human approval

---

## 5. CRM Pipelines

### 5.1 Brand Pipeline (11 stages)

```
Raw Lead → Contacted → Follow-up 1 → Follow-up 2 → Interested
→ Qualifying → Free Trial → Onboarded → Active → Dormant → Blacklisted
```

### 5.2 Influencer Pipeline (11 stages)

```
Discovered → DM Sent → Follow-up → Replied → Onboarding Chat
→ Onboarded → Available → On Campaign → Completed → Idle → Cold
```

**Phase 1:** Tracked in Google Sheets (zero cost).  
**Phase 2+:** Migrated to PostgreSQL + custom dashboard.

---

## 6. Campaign Workflow (Temporal + LangGraph)

Temporal orchestrates the campaign lifecycle (days/weeks). LangGraph handles intelligent agent decisions at each step.

### 6.1 Workflow Steps

| Step | Engine | Human Gate | Timeout |
|---|---|---|---|
| 1. Match influencers | LangGraph (Discovery) | Brand approves shortlist | 24hr |
| 2. Outreach to influencers | LangGraph (Outreach) | Auto (after calibration) | 72hr/influencer |
| 3. Coordinate visit | System (WhatsApp) | Confirm date | 72hr |
| 4. Content QC review | LangGraph (QC Agent) | Brand reviews after AI pass | 72hr |
| 5. Posting confirmation | System | None | Campaign duration |
| 6. Payment settlement | System (UPI/Razorpay) | Approve payout | 48hr |
| 7. Generate report | LangGraph (Reporting) | None | 1hr |

### 6.2 Deal Type Handling

| Step | Barter | Paid | Paid+Barter | Performance |
|---|---|---|---|---|
| Product/Meal | Keeps | Returns | Keeps | Varies |
| Payment | None | After draft approved | After draft approved | Reduced/none |
| Ad Spend | None | None | None | Guaranteed views |
| Commission (20%) | On product value | On payment | On payment | On ad spend |

---

## 7. Influencer Gamification System

| Tier | Criteria | Perks | Free Deal Obligation |
|---|---|---|---|
| 🥉 Bronze | Newly onboarded | Basic deal access | None |
| 🥈 Silver | 3+ deals | Priority matching | 1 free barter/quarter |
| 🥇 Gold | 10+ deals, 4.5+ rating | VIP brand access | 1 free barter/month |
| 💎 Platinum | 25+ deals, top performer | Premium brands, negotiation autonomy | 2 free barter/month |

**Free Deal Economy:** Silver+ influencers fulfill "free first campaign" offers to brands. Brand gets proof of concept → converts to paying client. Influencer gets free meal + content.

---

## 8. Outreach Channels & Tooling

| Channel | Target | Tool | Rate Limit |
|---|---|---|---|
| **WhatsApp** | Brands | Evolution API → Chatwoot | 5-10 msgs/number/day × 4-5 numbers |
| **IG DMs** | Influencers | Socionator + 5 proxies (₹500) | 5→6→8 DMs/day ramping |
| **Email** | Influencer fallback | Postal SMTP / Gmail | When IG DMs fail |

**Virtual Personas:** Each IG outreach account styled as recruiter (e.g., "Selena - VibMedia Recruiter").

**Why Evolution API over WhatsApp Business API?** Free messaging vs ₹0.80/msg. 50 msgs/day × 30 days = ₹1,200 saved/month.

---

## 9. Lead Generation / Scraping

| Target | Source | Data Extracted |
|---|---|---|
| **Brands** | Google My Business | Name, phone, address, rating, Google Maps link |
| **Brands** | Zomato | Name, phone, cuisine, price range, popular dishes, rating |
| **Influencers** | IG Bio | Email, Linktree, YouTube, manager contact |
| **Influencers** | IG Profile | Follower count, engagement rate, recent posts |
| **Influencers** | YouTube | Subscribers, video views, channel description |

Output: Merged master spreadsheet → deduplicated → fed into CRM pipeline.

---

## 10. Upsell Services

### 10.1 WordPress Single-Page Website (₹5,000)

Built via existing WordPress MCP. Includes:
- Hero section (name + cover photo)
- Menu highlights (3-5 items)
- Location map (Google Maps embed)
- Contact info (phone, WhatsApp, reservation link)
- Instagram feed embed
- Custom colors + logo

### 10.2 Meta Ads Campaign (₹5,000 + ad spend)

- Boost influencer reel/video as FB/IG paid ad
- Local targeting (5-15km radius around restaurant)
- Interest targeting (food lovers, dining out)
- 7/14/30 day duration options
- Weekly performance report

---

## 11. Database Schema

### 11.1 Core Tables

| Table | Purpose | Key Fields |
|---|---|---|
| `influencers` | Creator profiles + gamification | handle, tier, gamification_tier, base_rate, quality_score |
| `brands` | Restaurant/business profiles | brand_type (local/ecommerce/service), industry, pipeline_status |
| `campaigns` | Active campaigns | brand_id, deal_type, status, temporal_workflow_id |
| `campaign_influencers` | Influencer-campaign assignments | deal_amount, commission, visit_date, qc_score, posted_url |
| `outreach_messages` | All outreach (WhatsApp + IG DM + email) | channel, drip_day, socionator_profile, status |
| `agent_logs` | AI agent activity tracking | agent_name, llm_model, tokens_used, cost_usd |
| `invoices` | Brand billing | amount, service_type, razorpay_payment_id |
| `influencer_payouts` | Creator payments | gross_amount, commission_deducted, net_amount, payment_method |

Full SQL schema in [implementation_plan.md](../../../.gemini/antigravity/brain/4e390b6b-676d-493e-9de9-99f820ba1224/implementation_plan.md).

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
| WhatsApp | Evolution API + Chatwoot | Free multi-number messaging + unified inbox |
| IG DMs | Socionator + proxies | Automated DM sequences |
| Scraping | Firecrawl / custom | GMB, Zomato, IG bios |
| WordPress | Existing MCP | Upsell websites |
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
| Proxies | 5 data center | ~$6/mo |
| **Total** | | **~$131/mo (lasts ~2.3 months)** |

After credits: Migrate to Hetzner (€12-25/mo).

---

## 14. Phased Delivery

### Phase 1: Foundation (Month 1-2)
- [x] Influencer database (manual seed 200-300 Delhi NCR food creators)
- [ ] WhatsApp outreach via Evolution API (4-5 numbers + Chatwoot)
- [ ] Google Sheets CRM (brand + influencer pipelines)
- [ ] Brand cold outreach (GMB/Zomato scraping + WhatsApp drip)
- [ ] IG DM outreach (Socionator + proxies + recruiter personas)
- **Revenue Target:** ₹50K-₹1.5L/mo from 3-5 restaurant deals

### Phase 2: AI-Assisted Operations (Month 3-5)
- [ ] LangGraph Influencer Onboarding Agent
- [ ] LangGraph Deal-to-Booking Agent
- [ ] Internal Dashboard v1 (Next.js — campaigns, DB, agent logs, approvals)
- [ ] Conversational AI onboarding (replace manual data collection)
- [ ] Continuous enrichment loop (monthly auto-update)
- [ ] Influencer gamification tiers
- **Revenue Target:** ₹3-5L/mo from 10-15 clients

### Phase 3: Productized Service (Month 6-9)
- [ ] Client Dashboard (brands see campaigns, review content, get reports)
- [ ] Upsell Services Module (WordPress website + Meta Ads)
- [ ] Contract generation (template-based, e-signature)
- [ ] Payment tracking (Razorpay integration)
- [ ] AI Reporting Agent (weekly auto-reports)
- [ ] Expand to 2-3 industries beyond food
- **Revenue Target:** ₹8-15L/mo from 25-40 clients

### Phase 4: SaaS Platform (Month 10-18) — Questions to answer during Phase 1-3
- Multi-tenant architecture?
- API-first or UI-first?
- Agent customization for clients?
- Self-serve campaign builder?
- Mobile app for influencers?
- ML recommender vs rule-based?

---

## 15. Success Metrics

| Metric | Phase 1 Target | Phase 2 Target | Phase 3 Target |
|---|---|---|---|
| Active Brands | 3-5 | 10-15 | 25-40 |
| Active Influencers | 100-200 | 300-500 | 1,000+ |
| Monthly Revenue | ₹50K-₹1.5L | ₹3-5L | ₹8-15L |
| Outreach Response Rate | 5-10% | 10-15% (AI optimized) | 15-20% |
| Campaign Completion Rate | 70% | 85% | 95% |
| Agent Autonomy | 20% | 60% | 80% |
| Infrastructure Cost | ₹0 | ₹6K-10K/mo | ₹15-20K/mo |

---

## 16. Risks & Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| WhatsApp bans (Evolution API) | Lose outreach channel | Rate limit 5-10/day, warm up numbers gradually |
| IG account bans (Socionator) | Lose influencer outreach | Use proxies, ramp slowly, maintain backup accounts |
| Influencers ghost after onboarding | Empty database | Re-engagement drip, gamification perks |
| Brands don't convert after free deal | Revenue loss | Limit free deals to 1 per brand, track conversion rate |
| LLM hallucinations in outreach | Bad brand reputation | Human review first 10 msgs (calibration), daily audit |
| GCP credits exhaust | Infrastructure down | Plan Hetzner migration at $200 remaining |
| Influencer raises rate post-deal | Margin squeeze | Human negotiation, AI flags anomalies |

---

## Appendix A: Local Brands vs E-Commerce

| Aspect | Local Brands (Phase 1-3) | E-Commerce (Phase 4+) |
|---|---|---|
| Example | Restaurants, clinics, gyms | Amazon sellers, D2C brands |
| Logistics | Influencer walks in, shoots | Product shipping + returns |
| Complexity | Low — no product handling | High — shipping, GST, addressing |
| Volume | 3-15 influencers/campaign | 100-10,000 influencers/campaign |
| Rate | Mid-range | Cheap (₹1K nano bulk) |
| **Status** | **Starting here** | **Later phase** |

## Appendix B: Key Operational Decisions

| Decision | Choice | Rationale |
|---|---|---|
| Forms vs Chat onboarding | **Chat** (forms = backup) | Influencers hate forms, conversational has higher completion |
| WhatsApp Business API vs Evolution API | **Evolution API** | Free vs ₹0.80/msg |
| Pricing model | **Charge brand the influencer's rate, take 20% from influencer** | Standard agency model, clear to both parties |
| Payment method (Phase 1) | **UPI** | Zero cost, universal in India |
| Payment method (Phase 3+) | **Razorpay** | Invoicing, tracking, GST compliance |
| Hosting (Phase 1-2) | **GCP ($300 credit)** | Free tier, good specs |
| Hosting (Phase 3+) | **Hetzner** | 10x cheaper than GCP |
