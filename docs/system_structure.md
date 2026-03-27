# 🏗️ System Structure — Phase 1 Launch

> Architecture overview for Phase 1. This document describes WHAT we build at launch — not Phase 2-3 aspirations.

---

## 1. High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        OPERATIONS TEAM                          │
│  (Manual: data import, WhatsApp outreach, deal closing, QC)     │
└─────────┬──────────────────────────┬────────────────────────────┘
          │                          │
          ▼                          ▼
┌─────────────────┐      ┌────────────────────────┐
│  Google Sheets   │      │   AI Agent System       │
│  CRM             │      │   (FastAPI + LangGraph)  │
│  ┌─────────────┐ │      │                          │
│  │ Brand Tab   │ │◄────►│  ┌────────────────────┐  │
│  │ Influencer  │ │      │  │ Outreach Agent     │  │
│  │ Job Board   │ │      │  │ (instagrapi DMs)   │  │
│  │ Dashboard   │ │      │  ├────────────────────┤  │
│  └─────────────┘ │      │  │ Onboarding Agent   │  │
└─────────────────┘      │  │ (conversational)   │  │
                          │  ├────────────────────┤  │
                          │  │ Enrichment Agent   │  │
                          │  │ (profile data)     │  │
                          │  ├────────────────────┤  │
                          │  │ Campaign Agent     │  │
                          │  │ (matching + QC)    │  │
                          │  └────────────────────┘  │
                          └──────────┬───────────────┘
                                     │
                          ┌──────────▼───────────────┐
                          │  Infrastructure Layer     │
                          │  ┌──────┐ ┌───────────┐  │
                          │  │Proxy │ │IG Accounts│  │
                          │  │Groups│ │ + Sessions │  │
                          │  └──────┘ └───────────┘  │
                          │  ┌──────┐ ┌───────────┐  │
                          │  │Gemini│ │ PostgreSQL │  │
                          │  │ API  │ │(Phase 2)  │  │
                          │  └──────┘ └───────────┘  │
                          └──────────────────────────┘
```

---

## 2. Component Breakdown

### 2.1 Data Layer (Phase 1: Google Sheets)

| Sheet Tab | Purpose | Key Columns |
|-----------|---------|-------------|
| **Influencer Pipeline** | Track all influencers from import to campaign | Handle, Name, Followers, Tier, Points, Reputation Tier, Engagement Rate, Quality Score, Niche, Location, Phone, Pipeline Status |
| **Brand Pipeline** | Track brand leads from import to active client | Name, Phone, Area, Cuisine, Rating, Pipeline Status, Deal Count, Revenue |
| **Job Board** | Track job postings and applications | Job ID, Brand, Job Type, Deal Type, Points, Status (Open/Filled/Closed), Applicants, Accepted Influencer |
| **Campaign Tracker** | Active campaign lifecycle | Campaign ID, Brand, Influencer, Deal Type, Amount, Visit Date, Content Status, Payment Status, Points Awarded |
| **Account Manager** | IG outreach accounts | Username, Persona, Proxy Group, Status, DMs Today, Warmup Stage, Last Activity |
| **Dashboard** | Summary metrics + daily action items | Auto-calculated from other tabs |

### 2.2 AI Agent System (FastAPI + LangGraph)

```
                    ┌─────────────────────┐
                    │    FastAPI Gateway   │
                    │    (API + Scheduler) │
                    └─────────┬───────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │  Outreach   │ │ Onboarding  │ │ Enrichment  │
    │  Agent      │ │ Agent       │ │ Agent       │
    │             │ │             │ │             │
    │ • Batch DMs │ │ • Chat flow │ │ • Fetch IG  │
    │ • Drip seq  │ │ • Data      │ │   data      │
    │ • Reply     │ │   collection│ │ • Score     │
    │   classify  │ │ • Validation│ │   calc      │
    │ • Rate limit│ │ • Fallback  │ │ • Niche     │
    └─────┬───────┘ │   form      │ │   classify  │
          │         └──────┬──────┘ └──────┬──────┘
          │                │               │
          └────────────────┼───────────────┘
                           ▼
                 ┌───────────────────┐
                 │   instagrapi      │
                 │   (IG API Client) │
                 │                   │
                 │ • direct_send()   │
                 │ • user_info()     │
                 │ • user_medias()   │
                 │ • direct_threads()│
                 └────────┬──────────┘
                          │
                 ┌────────▼──────────┐
                 │  1 proxy → 2 outreach accounts│
                 └───────────────────┘
```

### 2.3 Outreach Flow (IG DMs)

```
[Imported CSV] → [Enrichment Agent] → [Quality Filter] → [Outreach Agent]
                                                              │
                                              ┌───────────────┼──────────┐
                                              ▼               ▼          ▼
                                          Day 1: Intro    Day 3: F/U  Day 5: Final
                                              │               │          │
                                              └───────┬───────┘──────────┘
                                                      ▼
                                              [Reply Classifier]
                                              ┌───┬───┬───┬────┐
                                              │ + │ - │ ? │ 📝 │
                                              └─┬─┴─┬─┴─┬─┴──┬─┘
                                                │   │   │    │
                                                ▼   ▼   ▼    ▼
                                           Onboard Stop Reply Collect
                                           Agent        AI    data
```

### 2.4 Job Board Flow

```
[Brand/Ops] ──post──► [Job Board] ◄──browse── [Influencers]
                           │
                      [Applications]
                           │
                    ┌──────▼──────┐
                    │ Ops Reviews  │
                    │ Accept/Reject│
                    └──────┬──────┘
                           │
                    [Accepted] ──► [Campaign Execution]
                                        │
                              [Job Complete] ──► [Points Awarded]
                                                      │
                                              [Tier Check & Upgrade]
```

---

## 3. Infrastructure (Phase 1)

### 3.1 GCP Deployment

```
VM 1 — App Server (e2-standard-2)
├── FastAPI backend
├── LangGraph agent runtime
├── Celery workers
└── Redis (task queue)

VM 2 — Services (e2-standard-2)
├── PostgreSQL 16 (initially for agent logs/state only)
├── Evolution API (WhatsApp — manual outreach)
└── Chatwoot (unified inbox)
```

### 3.2 Proxy Architecture

```
Proxy Group 1 ─── Proxy IP A
├── IG Account: @selena.vibmedia (Selena persona)
└── IG Account: @priya.vibmedia (Priya persona)

Proxy Group 2 ─── Proxy IP B
├── IG Account: @riya.vibmedia (Riya persona)
└── IG Account: @neha.vibmedia (Neha persona)

Proxy Group 3 ─── Proxy IP C (backup)
└── IG Account: @vibmedia.recruit (rotating backup)
```

### 3.3 Session Management

```
instagrapi Session Lifecycle:

1. Login ──► dump_settings(account_settings.json)
2. Next run ──► load_settings(account_settings.json)
3. Challenge detected ──► challenge_resolver()
4. Challenge failed ──► pause account, alert ops
5. Daily ──► health check all accounts
```

---

## 4. Data Flow Summary

```
        MANUAL INPUT                    AI PROCESSING                 OUTPUT
    ┌───────────────┐           ┌───────────────────────┐     ┌──────────────┐
    │ Influencer CSV│──import──►│ Enrichment Agent      │────►│ Enriched DB  │
    │ (IG handles)  │           │ (instagrapi + NLP)    │     │ (Sheets/PG)  │
    └───────────────┘           └───────────────────────┘     └──────────────┘
                                          │
    ┌───────────────┐                     │
    │ Brand CSV     │──import──► [Validation + Normalization] ──► [Brand Tab]
    │ (name, phone) │
    └───────────────┘
                                          │
    [Brand Tab] ──► [Ops posts Job] ──► [Job Board Tab]
                                          │
    [Enriched DB] ──► [Outreach Agent DMs] ──► [Reply Classify] ──► [Pipeline Update]
                                                     │
                                               [Positive Reply]
                                                     │
                                          [Onboarding Agent Chat] ──► [Profile Complete]
                                                     │
                                               [Points Awarded]
```

---

## 5. External Service Dependencies

| Service | Purpose | Phase 1 Status | Failure Impact |
|---------|---------|----------------|----------------|
| **instagrapi** | IG DM sending + profile enrichment | Primary tool | ⚠️ Critical — outreach stops |
| **Gemini API** | LLM for reply classification, onboarding chat | Primary LLM | ⚠️ High — agents degrade |
| **Evolution API** | WhatsApp message sending | Manual use | 🟡 Medium — switch to personal WA |
| **Chatwoot** | Unified inbox for brand replies | Manual use | 🟡 Medium — use WA groups instead |
| **Google Sheets API** | CRM data read/write | Data layer | ⚠️ High — use offline sheets |
| **Proxy provider** | IP addresses for IG accounts | Infrastructure | ⚠️ High — accounts at risk |

---

## 6. What Phase 1 System Does NOT Include

| Component | When | Why Later |
|-----------|------|-----------|
| Next.js Dashboard | Phase 2 | Google Sheets sufficient for <10 campaigns |
| PostgreSQL as primary DB | Phase 2 | Sheets + agent state DB enough at launch |
| Temporal workflow engine | Phase 2 | Manual tracking works for low volume |
| Qdrant vector search | Phase 2 | Spreadsheet filters work for <500 influencers |
| Automated WhatsApp outreach | Never (manual) | Relationship-driven, needs human judgment |
| Any automated scraping | Never | Ops team provides curated data |
