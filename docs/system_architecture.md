# 🏗️ System Architecture — Kobi AI Influencer Agency

> Complete architecture design for Phase 1 with all subsystem diagrams. Comprehensive mermaid-diagrammed version covering all agents, pipelines, infrastructure, and data flows.

> **WhatsApp Model:** Hybrid — AI sends personalized first touch, human handles complex replies via Chatwoot.

---

## 1. High-Level System Architecture

```mermaid
graph TB
    subgraph HUMAN["👤 Human Layer"]
        OPS["Operations Team"]
        SALES["Sales Team"]
    end

    subgraph DATA_IN["📥 Data Input"]
        CSV_INF["Influencer CSV<br/>(IG handles)"]
        CSV_BRAND["Brand CSV<br/>(restaurants)"]
    end

    subgraph AI_SYSTEM["🤖 AI Agent System (FastAPI + LangGraph)"]
        ENRICH["Enrichment Agent<br/>instagrapi + NLP"]
        OUTREACH["Outreach Agent<br/>IG DM via instagrapi"]
        ONBOARD["Onboarding Agent<br/>Conversational Chat"]
        CAMPAIGN["Campaign Agent<br/>Matching + QC"]
        WA_BOT["WhatsApp Bot<br/>Personalized 1st Touch"]
        CLASSIFY["Reply Classifier<br/>Gemini Flash"]
    end

    subgraph INFRA["⚙️ Infrastructure"]
        PROXY["Proxy Groups<br/>1:2 ratio"]
        IG_ACCT["IG Accounts<br/>+ Sessions"]
        GEMINI["Gemini API<br/>Flash + Pro"]
        SHEETS["Google Sheets<br/>CRM (Phase 1)"]
        PG["PostgreSQL<br/>(Agent state only)"]
    end

    subgraph CHANNELS["📡 Outreach Channels"]
        IG_DM["Instagram DMs<br/>(instagrapi)"]
        WA["WhatsApp<br/>(Evolution API)"]
        CHATWOOT["Chatwoot<br/>Unified Inbox"]
    end

    CSV_INF --> ENRICH
    CSV_BRAND --> ENRICH
    ENRICH --> SHEETS
    ENRICH --> OUTREACH
    OUTREACH --> IG_DM
    IG_DM --> CLASSIFY
    CLASSIFY -->|positive| ONBOARD
    CLASSIFY -->|negative| OUTREACH
    ONBOARD --> SHEETS
    CAMPAIGN --> SHEETS
    
    CSV_BRAND --> WA_BOT
    WA_BOT -->|"personalized 1st msg"| WA
    WA -->|"reply received"| CLASSIFY
    CLASSIFY -->|"complex/interested"| CHATWOOT
    CHATWOOT --> SALES
    
    OUTREACH --> PROXY
    PROXY --> IG_ACCT
    CLASSIFY --> GEMINI
    ONBOARD --> GEMINI
    
    OPS --> CSV_INF
    OPS --> CSV_BRAND
    SALES --> CHATWOOT
```

---

## 2. Influencer Pipeline Flow

```mermaid
flowchart LR
    subgraph IMPORT["1️⃣ Import"]
        A[CSV/Sheets Import] --> B[Dedup by Handle]
        B --> C[Validation]
    end

    subgraph ENRICH["2️⃣ Enrichment"]
        C --> D[instagrapi user_info]
        D --> E[Engagement Rate Calc]
        E --> F[Niche Classification]
        F --> G[Quality Score 0-100]
        G --> H[Tier Assignment]
    end

    subgraph OUTREACH["3️⃣ Outreach"]
        H --> I[DM Day 1: Intro]
        I -->|no reply| J[DM Day 3: Follow-up]
        J -->|no reply| K[DM Day 5: Final]
    end

    subgraph CLASSIFY["4️⃣ Classification"]
        I -->|reply| L{Reply Classifier}
        J -->|reply| L
        K -->|reply| L
        L -->|positive| M[Onboarding Agent]
        L -->|negative| N[Stop + Mark Cold]
        L -->|question| O[AI Answers]
        L -->|spam| P[Ignore]
    end

    subgraph ONBOARD["5️⃣ Onboarding"]
        M --> Q[Collect: Name, Area]
        Q --> R[Collect: Phone, Rate]
        R --> S[Collect: Deal Pref]
        S --> T[Validate All Fields]
        T --> U[Profile Complete<br/>0 pts, Bronze]
    end

    style IMPORT fill:#1a1a2e,color:#fff
    style ENRICH fill:#16213e,color:#fff
    style OUTREACH fill:#0f3460,color:#fff
    style CLASSIFY fill:#533483,color:#fff
    style ONBOARD fill:#2a4858,color:#fff
```

---

## 3. Brand Pipeline Flow (Hybrid WhatsApp)

> **Key Change:** AI sends the personalized first touch. Human takes over after first meaningful reply.

```mermaid
flowchart TD
    subgraph BRAND_IMPORT["1️⃣ Brand Import"]
        BI1[CSV Import] --> BI2[Validate Phone]
        BI2 --> BI3[Normalize Cuisine]
        BI3 --> BI4[Score Lead Value]
    end

    subgraph AI_TOUCH["2️⃣ AI First Touch (WhatsApp)"]
        BI4 --> AT1["AI generates personalized msg<br/>using {dish}, {area}, {cuisine}"]
        AT1 --> AT2["Send via Evolution API"]
        AT2 --> AT3{Reply?}
        AT3 -->|no reply, Day 2| AT4["AI sends social proof msg"]
        AT4 --> AT5{Reply?}
        AT5 -->|no reply, Day 3| AT6["AI sends free deal offer"]
        AT6 --> AT7{Reply?}
        AT7 -->|no reply| AT8[Mark: No Response<br/>Re-engage in 30 days]
    end

    subgraph HANDOVER["3️⃣ Human Handover"]
        AT3 -->|reply| HO1{Classify Reply}
        AT5 -->|reply| HO1
        AT7 -->|reply| HO1
        HO1 -->|"simple positive<br/>(yes, interested)"| HO2[Route to Chatwoot<br/>Human sales picks up]
        HO1 -->|"question<br/>(price? how?)"| HO3[AI answers FAQ<br/>then handover]
        HO1 -->|"negative"| HO4[Mark Cold<br/>Log reason]
        HO1 -->|"price objection"| HO5[Route to Chatwoot<br/>Human negotiates]
    end

    subgraph CLOSING["4️⃣ Human Closes Deal"]
        HO2 --> CL1[Sales qualifies lead]
        HO3 --> CL1
        HO5 --> CL1
        CL1 --> CL2{Interested?}
        CL2 -->|yes| CL3[Free Trial / Job Post]
        CL2 -->|no| CL4[Dormant]
        CL3 --> CL5[Onboarded Brand]
    end

    style AI_TOUCH fill:#0d7377,color:#fff
    style HANDOVER fill:#14a085,color:#fff
    style CLOSING fill:#323232,color:#fff
```

---

## 4. Job Board & Points System

```mermaid
flowchart TD
    subgraph JOB_CREATE["📌 Job Creation"]
        JC1[Brand/Ops creates job] --> JC2["Define: niche, tier,<br/>deal type, deliverables,<br/>budget, points"]
        JC2 --> JC3[Job posted to board]
    end

    subgraph JOB_MATCH["🔍 Matching"]
        JC3 --> JM1[Filter by influencer profile]
        JM1 --> JM2[Eligible influencers see job]
        JM2 --> JM3[Influencer applies]
    end

    subgraph JOB_REVIEW["✅ Review"]
        JM3 --> JR1[Ops reviews applications]
        JR1 --> JR2{Accept?}
        JR2 -->|yes| JR3[Notify influencer via DM]
        JR2 -->|no| JR4[Notify rejection]
    end

    subgraph EXECUTION["🎬 Execution"]
        JR3 --> EX1[Coordinate visit date]
        EX1 --> EX2[Influencer visits & creates content]
        EX2 --> EX3[Content QC review]
        EX3 --> EX4{QC Pass?}
        EX4 -->|yes| EX5[Content posted]
        EX4 -->|no| EX6[Revision requested]
        EX6 --> EX2
    end

    subgraph COMPLETE["⭐ Completion"]
        EX5 --> CP1[Job marked complete]
        CP1 --> CP2[Points awarded]
        CP2 --> CP3{Tier upgrade?}
        CP3 -->|yes| CP4[Upgrade tier + notify]
        CP3 -->|no| CP5[Update profile]
        CP1 --> CP6[Mutual ratings]
        CP1 --> CP7[Payment processed]
    end

    style JOB_CREATE fill:#1b2838,color:#fff
    style JOB_MATCH fill:#2d4059,color:#fff
    style JOB_REVIEW fill:#3b6978,color:#fff
    style EXECUTION fill:#204051,color:#fff
    style COMPLETE fill:#84a9ac,color:#000
```

---

## 5. Points & Reputation Tiers

```mermaid
graph LR
    subgraph EARNING["Points Earning"]
        QR["Quick Review<br/>10 pts"]
        SC["Standard Collab<br/>25 pts"]
        FC["Full Campaign<br/>50 pts"]
        LT["Long-term<br/>100 pts"]
    end

    subgraph TIERS["Reputation Tiers"]
        B["🥉 Bronze<br/>0-49 pts<br/>Basic access"]
        S["🥈 Silver<br/>50-199 pts<br/>Priority matching"]
        G["🥇 Gold<br/>200-499 pts<br/>VIP brand access"]
        P["💎 Platinum<br/>500+ pts<br/>Premium + autonomy"]
    end

    QR --> B
    SC --> S
    FC --> G
    LT --> P

    B -->|"earn 50 pts"| S
    S -->|"earn 200 pts"| G
    G -->|"earn 500 pts"| P
```

---

## 6. AI Agent Architecture (LangGraph)

```mermaid
graph TD
    subgraph GATEWAY["FastAPI Gateway"]
        API["REST API<br/>+ Scheduler"]
        CELERY["Celery Workers<br/>+ Redis Queue"]
    end

    subgraph AGENTS["LangGraph Agents"]
        subgraph OUT_AGENT["Outreach Agent"]
            OA1[Batch DM Sender]
            OA2[Drip Sequence Manager]
            OA3[Rate Limiter]
            OA4[Account Rotator]
        end

        subgraph ONB_AGENT["Onboarding Agent"]
            OB1[Chat Flow Controller]
            OB2[Data Collection]
            OB3[Real-time Validation]
            OB4[Form Fallback]
        end

        subgraph ENR_AGENT["Enrichment Agent"]
            EN1[Profile Fetcher]
            EN2[Engagement Calculator]
            EN3[Niche Classifier]
            EN4[Quality Scorer]
        end

        subgraph CAM_AGENT["Campaign Agent"]
            CA1[Influencer Matcher]
            CA2[Content QC]
            CA3[Report Generator]
        end

        subgraph WA_AGENT["WhatsApp Outreach Agent"]
            WA1[Template Personalizer]
            WA2[Drip Scheduler]
            WA3[Reply Router]
        end
    end

    subgraph LLM["LLM Layer"]
        FLASH["Gemini Flash<br/>Classification, simple tasks"]
        PRO["Gemini Pro<br/>Complex onboarding, QC"]
        OLLAMA["Ollama Llama 3<br/>Fallback"]
    end

    API --> CELERY
    CELERY --> OUT_AGENT
    CELERY --> ONB_AGENT
    CELERY --> ENR_AGENT
    CELERY --> CAM_AGENT
    CELERY --> WA_AGENT

    OUT_AGENT --> FLASH
    ONB_AGENT --> PRO
    ENR_AGENT --> FLASH
    CAM_AGENT --> PRO
    WA_AGENT --> FLASH

    style GATEWAY fill:#1a1a2e,color:#fff
    style AGENTS fill:#0f3460,color:#fff
    style LLM fill:#533483,color:#fff
```

---

## 7. Infrastructure Deployment (Phase 1 — GCP)

```mermaid
graph TD
    subgraph VM1["VM 1 — App Server<br/>e2-standard-2 (2 vCPU, 8GB)"]
        FASTAPI["FastAPI Backend"]
        LANGGRAPH["LangGraph Runtime"]
        CELERY_W["Celery Workers"]
        REDIS["Redis (Task Queue)"]
    end

    subgraph VM2["VM 2 — Services<br/>e2-standard-2 (2 vCPU, 8GB)"]
        POSTGRES["PostgreSQL 16<br/>(agent state + logs)"]
        EVOLUTION["Evolution API<br/>(WhatsApp)"]
        CHATWOOT_S["Chatwoot<br/>(Unified Inbox)"]
    end

    subgraph EXTERNAL["External Services"]
        GEMINI_E["Gemini API"]
        PROXIES["Residential Proxies<br/>2-3 IPs"]
        GSHEETS["Google Sheets API"]
    end

    FASTAPI --> LANGGRAPH
    LANGGRAPH --> CELERY_W
    CELERY_W --> REDIS

    FASTAPI --> POSTGRES
    FASTAPI --> EVOLUTION
    EVOLUTION --> CHATWOOT_S

    CELERY_W --> GEMINI_E
    CELERY_W --> PROXIES
    CELERY_W --> GSHEETS

    style VM1 fill:#1b4332,color:#fff
    style VM2 fill:#2d6a4f,color:#fff
    style EXTERNAL fill:#40916c,color:#fff
```

---

## 8. Proxy & Account Architecture

```mermaid
graph TD
    subgraph PG1["Proxy Group 1 — IP A"]
        P1_IP["Residential Proxy A"]
        P1_A1["@selena.vibmedia<br/>Selena Persona"]
        P1_A2["@priya.vibmedia<br/>Priya Persona"]
        P1_IP --> P1_A1
        P1_IP --> P1_A2
    end

    subgraph PG2["Proxy Group 2 — IP B"]
        P2_IP["Residential Proxy B"]
        P2_A1["@riya.vibmedia<br/>Riya Persona"]
        P2_A2["@neha.vibmedia<br/>Neha Persona"]
        P2_IP --> P2_A1
        P2_IP --> P2_A2
    end

    subgraph PG3["Proxy Group 3 — IP C (Backup)"]
        P3_IP["Residential Proxy C"]
        P3_A1["@vibmedia.recruit<br/>Rotating Backup"]
        P3_IP --> P3_A1
    end

    subgraph SESSION["Session Management"]
        LOGIN["Login via instagrapi"]
        DUMP["dump_settings()"]
        LOAD["load_settings()"]
        CHALLENGE["challenge_resolver()"]
        HEALTH["Daily Health Check"]

        LOGIN --> DUMP
        DUMP --> LOAD
        LOAD -->|"challenge"| CHALLENGE
        CHALLENGE -->|"failed"| HEALTH
    end

    P1_A1 --> SESSION
    P1_A2 --> SESSION
    P2_A1 --> SESSION
    P2_A2 --> SESSION
    P3_A1 --> SESSION

    style PG1 fill:#2b2d42,color:#fff
    style PG2 fill:#3d405b,color:#fff
    style PG3 fill:#5c5d70,color:#fff
    style SESSION fill:#8d99ae,color:#000
```

---

## 9. Data Layer (Phase 1 → Phase 2 Migration)

```mermaid
graph LR
    subgraph PHASE1["Phase 1: Google Sheets"]
        S1["Influencer Pipeline Tab"]
        S2["Brand Pipeline Tab"]
        S3["Job Board Tab"]
        S4["Campaign Tracker Tab"]
        S5["Account Manager Tab"]
        S6["Dashboard Tab"]
    end

    subgraph PHASE2["Phase 2: PostgreSQL"]
        T1["influencers"]
        T2["brands"]
        T3["job_postings"]
        T4["job_applications"]
        T5["campaigns"]
        T6["campaign_influencers"]
        T7["points_ledger"]
        T8["outreach_messages"]
        T9["ig_accounts"]
        T10["proxy_groups"]
        T11["ratings"]
        T12["agent_logs"]
        T13["invoices"]
        T14["influencer_payouts"]
    end

    S1 -->|"migrate"| T1
    S2 -->|"migrate"| T2
    S3 -->|"migrate"| T3
    S3 -->|"migrate"| T4
    S4 -->|"migrate"| T5
    S4 -->|"migrate"| T6
    S5 -->|"migrate"| T9
    S5 -->|"migrate"| T10

    style PHASE1 fill:#003049,color:#fff
    style PHASE2 fill:#d62828,color:#fff
```

---

## 10. WhatsApp Hybrid Outreach Detail

```mermaid
sequenceDiagram
    participant OPS as Ops Team
    participant AI as WhatsApp Bot
    participant EVOL as Evolution API
    participant WA as Restaurant WhatsApp
    participant CW as Chatwoot
    participant SALES as Sales Human

    OPS->>AI: Import brand CSV with enriched data
    AI->>AI: Generate personalized msg<br/>"Hi {name}! We loved your {dish}..."
    AI->>EVOL: Send Day 1 message
    EVOL->>WA: Deliver via WhatsApp
    
    alt No Reply (Day 2)
        AI->>EVOL: Send social proof msg<br/>"{competitor} got 45K views..."
        EVOL->>WA: Deliver
    end

    alt No Reply (Day 3)
        AI->>EVOL: Send free deal offer
        EVOL->>WA: Deliver
    end

    alt Brand Replies
        WA->>EVOL: Reply received
        EVOL->>AI: Webhook notification
        AI->>AI: Classify reply intent
        
        alt Simple Question (FAQ)
            AI->>EVOL: Auto-answer FAQ
            AI->>CW: Log conversation
            Note over AI,CW: AI handles simple Q&A
        end
        
        alt Interested / Complex / Negotiation
            AI->>CW: Route to Chatwoot inbox
            CW->>SALES: Notification: hot lead!
            SALES->>CW: Human takes over conversation
            Note over SALES,CW: Human closes the deal
        end
        
        alt Negative
            AI->>AI: Mark Cold, log reason
            AI->>CW: Log in CRM
        end
    end
```

---

## 11. Reply Classification System

```mermaid
flowchart TD
    subgraph INPUT["Incoming Messages"]
        IG_MSG["IG DM Reply"]
        WA_MSG["WhatsApp Reply"]
    end

    subgraph CLASSIFIER["Gemini Flash Classifier"]
        PROMPT["Classification Prompt<br/>+ Few-shot Examples"]
        CONF["Confidence Check"]
    end

    subgraph IG_CLASSES["IG DM Classes"]
        IG_POS["✅ Positive<br/>'interested in deals'"]
        IG_NEG["❌ Negative<br/>'not interested'"]
        IG_SPAM["🚫 Spam<br/>'irrelevant'"]
        IG_Q["❓ Question<br/>'tell me more'"]
        IG_DATA["📝 Onboarding Data<br/>'my rate is ₹5K'"]
    end

    subgraph WA_CLASSES["WhatsApp Classes"]
        WA_POS["✅ Positive<br/>'interested'"]
        WA_NEU["🟡 Neutral<br/>'send details'"]
        WA_NEG["❌ Negative<br/>'not interested'"]
        WA_PRICE["💰 Price Objection<br/>'too expensive'"]
        WA_DOING["🔄 Already Doing<br/>'already use influencers'"]
    end

    IG_MSG --> PROMPT
    WA_MSG --> PROMPT
    PROMPT --> CONF

    CONF -->|"high confidence"| IG_CLASSES
    CONF -->|"high confidence"| WA_CLASSES
    CONF -->|"low confidence"| HUMAN["🧑 Escalate to Human"]

    IG_POS --> ONBOARD_FLOW["Start Onboarding"]
    IG_NEG --> STOP["Stop Sequence"]
    IG_Q --> AI_REPLY["AI Answers"]
    IG_DATA --> COLLECT["Store Data"]

    WA_POS --> CHATWOOT_R["→ Chatwoot (Human)"]
    WA_NEU --> AI_FAQ["AI Answers FAQ"]
    WA_NEG --> COLD["Mark Cold"]
    WA_PRICE --> CHATWOOT_R
    WA_DOING --> CHATWOOT_R

    style CLASSIFIER fill:#6c3483,color:#fff
    style IG_CLASSES fill:#1a5276,color:#fff
    style WA_CLASSES fill:#0e6655,color:#fff
```

---

## 12. Campaign Lifecycle

```mermaid
stateDiagram-v2
    [*] --> JobPosted: Brand/Ops posts job
    JobPosted --> ApplicationsOpen: Job goes live
    ApplicationsOpen --> UnderReview: Influencer applies
    UnderReview --> Accepted: Ops approves
    UnderReview --> Rejected: Ops rejects
    Rejected --> [*]
    
    Accepted --> VisitScheduled: Date coordinated
    VisitScheduled --> VisitComplete: Influencer visits
    VisitComplete --> ContentDraft: Content created
    ContentDraft --> ContentQC: AI reviews
    
    ContentQC --> RevisionNeeded: QC fails
    RevisionNeeded --> ContentDraft: Resubmit
    ContentQC --> ContentApproved: QC passes
    
    ContentApproved --> Posted: Content goes live
    Posted --> PointsAwarded: Points added
    PointsAwarded --> RatingCollected: Mutual ratings
    RatingCollected --> PaymentProcessed: Commission calc'd
    PaymentProcessed --> Complete: All settled
    Complete --> [*]

    note right of ContentQC
        AI first pass
        Human reviews flagged items
    end note

    note right of PaymentProcessed
        Brand pays → 20% commission
        → Net payout to influencer via UPI
    end note
```

---

## 13. CRM Pipeline State Machines

### Influencer Pipeline
```mermaid
stateDiagram-v2
    [*] --> Imported: CSV import
    Imported --> DMSent: Outreach agent sends DM
    DMSent --> FollowUp: No reply (Day 3)
    FollowUp --> FinalDM: No reply (Day 5)
    FinalDM --> Cold: No reply after Day 5
    
    DMSent --> Replied: Got reply
    FollowUp --> Replied: Got reply
    FinalDM --> Replied: Got reply
    
    Replied --> OnboardingChat: Positive classification
    Replied --> Cold: Negative classification
    
    OnboardingChat --> Onboarded: Data collected
    Onboarded --> Available: Ready for jobs
    Available --> OnCampaign: Accepted a job
    OnCampaign --> Completed: Job done + points
    Completed --> Available: Ready for next
    Available --> Idle: 30 days inactive
    Idle --> Cold: 60 days inactive
    Idle --> Available: Re-engaged
```

### Brand Pipeline
```mermaid
stateDiagram-v2
    [*] --> RawLead: CSV import
    RawLead --> Contacted: AI sends Day 1 msg
    Contacted --> FollowUp1: AI sends Day 2
    FollowUp1 --> FollowUp2: AI sends Day 3
    FollowUp2 --> NoResponse: No reply after Day 3
    NoResponse --> RawLead: Re-engage (30 days)
    
    Contacted --> Interested: Positive reply
    FollowUp1 --> Interested: Positive reply
    FollowUp2 --> Interested: Positive reply
    
    Interested --> Qualifying: Human evaluates fit
    Qualifying --> FreeTrial: Offer free influencer visit
    FreeTrial --> Onboarded: Trial successful
    Onboarded --> Active: Paying client
    Active --> Dormant: 30 days no campaign
    Dormant --> Active: New campaign
    
    Contacted --> Blacklisted: Hostile reply
    Interested --> Blacklisted: Hostile reply
```

---

## 14. Reporting & Notification Flow

```mermaid
flowchart TD
    subgraph TRIGGERS["Trigger Events"]
        T1["Daily 10PM IST"]
        T2["Weekly Sunday 8PM"]
        T3["Real-time Events"]
    end

    subgraph REPORTS["Report Types"]
        R1["📊 Daily Ops Summary<br/>DMs sent, replies,<br/>brands contacted,<br/>campaigns, revenue"]
        R2["📈 Weekly Business Report<br/>New leads, conversions,<br/>job board stats, MRR"]
        R3["🔔 Real-time Alerts<br/>Positive reply, account issue,<br/>payment received"]
    end

    subgraph DELIVERY["Delivery Channels"]
        D1["WhatsApp Group<br/>(Ops Team)"]
        D2["Chatwoot<br/>(Individual)"]
        D3["Google Sheets<br/>(Dashboard Tab)"]
    end

    T1 --> R1
    T2 --> R2
    T3 --> R3

    R1 --> D1
    R1 --> D3
    R2 --> D1
    R2 --> D3
    R3 --> D1
    R3 --> D2

    style TRIGGERS fill:#1a1a2e,color:#fff
    style REPORTS fill:#16213e,color:#fff
    style DELIVERY fill:#0f3460,color:#fff
```

---

## External Service Dependencies

| Service | Purpose | Phase 1 Status | Failure Impact |
|---------|---------|----------------|----------------|
| **instagrapi** | IG DM sending + profile enrichment | Primary tool | ⚠️ Critical — outreach stops |
| **Gemini API** | LLM for reply classification, onboarding chat | Primary LLM | ⚠️ High — agents degrade |
| **Evolution API** | WhatsApp message sending (AI + manual) | Primary tool | 🟡 Medium — switch to personal WA |
| **Chatwoot** | Unified inbox for brand replies + human handover | Required | ⚠️ High — lose handover flow |
| **Google Sheets API** | CRM data read/write | Data layer | ⚠️ High — use offline sheets |
| **Proxy provider** | IP addresses for IG accounts | Infrastructure | ⚠️ High — accounts at risk |
