# 🤖 Kobi — AI Influencer Agency System

> **VibMedia's AI-first influencer marketing agency**, built to connect Delhi NCR food influencers with restaurant brands through automated outreach, conversational onboarding, and a gamified job board.

---

## 🧠 What is Kobi?

Kobi is a **multi-agent AI system** that operates the full influencer agency pipeline:

```
Influencer Discovery → AI Outreach → Conversational Onboarding → Job Board → Campaign QC → Reports
        ↑                                                                          ↓
    Brand Leads → AI WhatsApp First Touch → Human Handover → Deal Closing → Campaign Launch
```

**Phase 1 Focus:** Delhi NCR restaurants × food influencers. Zero-cost infrastructure using GCP credits. Google Sheets as CRM.

---

## 🏗️ System Architecture

> See [`docs/system_architecture.md`](docs/system_architecture.md) for all 14 Mermaid diagrams.

### Core Components

| Component | Purpose | Technology |
|-----------|---------|------------|
| **Enrichment Agent** | Fetches IG profiles, calculates engagement, classifies niche, scores quality | `instagrapi` + Gemini Flash |
| **Outreach Agent** | Sends 3-day IG DM drip sequences with account rotation + rate limiting | `instagrapi` + LangGraph |
| **Onboarding Agent** | Conversational chat to collect influencer data (name, area, rate, phone) | LangGraph + Gemini Pro |
| **WhatsApp Bot** | AI-personalized first touch to brands, FAQ handling, human handover | Evolution API + Gemini Flash |
| **Reply Classifier** | Classifies incoming IG/WA replies into intent categories | Gemini Flash (few-shot) |
| **Campaign Agent** | Matches influencers to jobs, QC review, report generation | Gemini Pro |
| **Job Board** | Brands post jobs → influencers apply → ops reviews | Google Sheets (Phase 1) |
| **Points System** | Gamified reputation: Bronze→Silver→Gold→Platinum | Google Sheets tracking |

### Data Flow

```
┌─────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  CSV Import  │────▶│  Enrichment  │────▶│   Outreach   │────▶│  Onboarding  │
│ (Manual)     │     │  (instagrapi)│     │   (IG DMs)   │     │  (LangGraph) │
└─────────────┘     └──────┬───────┘     └──────┬───────┘     └──────┬───────┘
                           │                    │                    │
                    ┌──────▼───────┐     ┌──────▼───────┐     ┌──────▼───────┐
                    │ Google Sheets│     │ Reply        │     │ Onboarded    │
                    │ CRM          │◀────│ Classifier   │     │ Influencer   │
                    └──────────────┘     └──────────────┘     └──────────────┘
```

---

## 📁 Project Structure

```
kobi/
├── docs/                          # 📄 All documentation
│   ├── PRD.md                     # Product Requirements Document
│   ├── system_architecture.md     # System design (14 Mermaid diagrams)
│   ├── guardrails.md              # DO and DON'T rules
│   ├── ROADMAP.md                 # Phase-by-phase roadmap
│   ├── user_stories_phase1.md     # User stories (P0/P1/P2)
│   ├── system_structure.md        # Original system structure (legacy)
│   ├── audit_findings.md          # Cross-reference audit results
│   ├── resource_requirement_guide.md  # Knowledge domain research guide
│   ├── knowledge_requirements.md  # Knowledge research prompts
│   └── designs/                   # UI/UX mockup images
│       ├── 01_ops_dashboard.png
│       ├── 02_account_manager.png
│       ├── 03_job_board.png
│       ├── 04_influencer_profile.png
│       ├── 05_campaign_tracker.png
│       ├── 06_whatsapp_outreach.png
│       ├── 07_campaign_report.png
│       └── 08_onboarding_chat.png
│
├── data/                          # 📊 Data files
│   ├── influencers/               # Influencer CSVs (manually sourced)
│   ├── brands/                    # Restaurant CSVs (manually sourced)
│   └── Blueprint/                 # Business blueprint docs
│       ├── ai_influencer_agency_blueprint.md
│       ├── brainstorm_ai_influencer_agency.md
│       └── implementation_plan.md
│
├── templates/                     # 📝 Message templates
│   └── outreach_templates.md      # WhatsApp + IG DM templates
│
├── agents/                        # 🤖 LangGraph agent definitions (TO BUILD)
│   ├── enrichment/                # Profile enrichment agent
│   ├── outreach/                  # IG DM outreach agent
│   ├── onboarding/                # Conversational onboarding agent
│   ├── whatsapp/                  # WhatsApp hybrid outreach agent
│   ├── classifier/                # Reply classification agent
│   └── campaign/                  # Campaign matching + QC agent
│
├── backend/                       # ⚙️ FastAPI backend (TO BUILD)
│   ├── api/                       # REST endpoints
│   ├── services/                  # Business logic
│   ├── models/                    # Data models
│   └── config/                    # Configuration
│
├── scripts/                       # 🔧 Operational scripts (TO BUILD)
│   ├── import_csv.py              # CSV → Google Sheets importer
│   ├── session_manager.py         # IG session management
│   └── health_check.py            # Account health monitoring
│
├── dashboard/                     # 📊 Dashboard (Phase 2 — NOT Phase 1)
│
├── .env.example                   # Environment variables template
├── requirements.txt               # Python dependencies
├── docker-compose.yml             # Docker setup (Phase 1)
└── README.md                      # ← You are here
```

---

## 🔑 Key Design Decisions

### 1. WhatsApp: Hybrid AI + Human Model
```
AI sends personalized first touch (3-day drip)
         ↓
Brand replies → Gemini classifies intent
         ↓
┌─────────────────┬─────────────────┬────────────────┐
│   FAQ Question  │ Positive/Complex │   Negative     │
│   AI auto-answer│ → Chatwoot       │ Mark Cold, log │
│   then handover │ Human negotiates │                │
└─────────────────┴─────────────────┴────────────────┘
```

**Why hybrid?** AI can personalize at scale (using {dish}, {area}, {cuisine}), but deal negotiation requires human trust. AI handles the top-of-funnel, human closes.

### 2. Google Sheets as Phase 1 CRM
- **Why not PostgreSQL?** Zero cost, zero setup, full team visibility, easy pivot
- **Migration plan:** All columns map to PostgreSQL tables (documented in system_architecture §9)
- **Sheets tabs:** Influencer Pipeline, Brand Pipeline, Job Board, Campaign Tracker, Account Manager, Dashboard

### 3. Points System Instead of Free-Deal Obligations
| Old Model (Rejected) | New Model (Active) |
|---|---|
| "Do 1 free deal to get started" | Earn points voluntarily |
| Creates pressure and resentment | Gamified, intrinsic motivation |
| No progression | Bronze→Silver→Gold→Platinum tiers |

### 4. 1:2 Proxy-to-Account Ratio
Each residential proxy IP serves maximum 2 IG outreach accounts. More accounts per IP = ban risk. Fewer = wasted money.

### 5. Account Warmup Protocol (Mandatory)
```
Week 1: Follows + Likes only (organic behavior)
Week 2: Comments on food posts
Week 3: Start DMs (5/day → 6/day → 8/day ramp)
```
> ⚠️ NO SHORTCUTS. Cold accounts sending DMs = instant action block.

---

## 🤖 Agent Architecture

### LangGraph State Machine
Each agent is a **LangGraph state graph** with human-in-the-loop interrupt gates.

```
                    ┌─────────────────────────────┐
                    │     FastAPI Gateway          │
                    │     + Celery Workers         │
                    └─────────┬───────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
  ┌─────▼─────┐        ┌─────▼─────┐        ┌─────▼─────┐
  │ Outreach  │        │ Onboarding│        │ WhatsApp  │
  │ Agent     │        │ Agent     │        │ Agent     │
  │           │        │           │        │           │
  │ • Batch   │        │ • Chat    │        │ • Template│
  │ • Drip    │        │ • Collect │        │ • Drip    │
  │ • Rotate  │        │ • Validate│        │ • Route   │
  │ • Limit   │        │ • Fallback│        │ • Classify│
  └─────┬─────┘        └─────┬─────┘        └─────┬─────┘
        │                    │                     │
        ▼                    ▼                     ▼
  Gemini Flash          Gemini Pro            Gemini Flash
```

### LLM Usage Map

| Task | Model | Why |
|------|-------|-----|
| Reply classification | Gemini Flash | Fast, cheap, high-volume |
| DM personalization | Gemini Flash | Template filling, simple NLP |
| Onboarding conversation | Gemini Pro | Complex multi-turn dialogue |
| Content QC | Gemini Pro | Nuanced quality assessment |
| WhatsApp FAQ answers | Gemini Flash | Simple question → answer |
| Niche classification | Gemini Flash | Category assignment |
| Engagement analysis | Gemini Flash | Number crunching + grade |
| **Fallback (all)** | **Llama 3 (Ollama)** | **Self-hosted, zero cost** |

---

## 📊 Pipelines

### Influencer Pipeline (11 Stages)
```
Imported → DM Sent → Follow-up → Replied → Onboarding Chat 
    → Onboarded → Available → On Campaign → Completed → Idle → Cold
```

### Brand Pipeline (10 Stages)
```
Raw Lead → Contacted (Day 1) → Follow-up 1 (Day 2) → Follow-up 2 (Day 3) 
    → No Response / Interested → Qualifying → Free Trial → Onboarded 
    → Active → Dormant
```

### Campaign Lifecycle (12 States)
```
Job Posted → Applications Open → Under Review → Accepted/Rejected 
    → Visit Scheduled → Visit Complete → Content Draft → Content QC 
    → Approved/Revision → Posted → Points Awarded → Rating → Payment → Complete
```

---

## ⭐ Points & Reputation System

| Tier | Points | Benefits |
|------|--------|----------|
| 🥉 Bronze | 0-49 | Basic job board access |
| 🥈 Silver | 50-199 | Priority matching, Silver badge |
| 🥇 Gold | 200-499 | VIP brand access, Gold badge |
| 💎 Platinum | 500+ | Premium deals, autonomy, Platinum badge |

| Job Type | Points Earned |
|----------|--------------|
| Quick Review (1 story) | 10 pts |
| Standard Collab (reel + stories) | 25 pts |
| Full Campaign (multi-content) | 50 pts |
| Long-term Partnership | 100 pts |

---

## 💰 Revenue Model

| Source | Amount | Details |
|--------|--------|---------|
| Commission on paid deals | 20% | Brand pays ₹10K → VibMedia keeps ₹2K |
| Campaign packages | ₹10K-₹50K | Starter / Growth / Premium tiers |
| Upsell: WordPress site | ₹5,000 | Single-page restaurant site |
| Upsell: Meta Ads | ₹5K + ad spend | IG/FB ad campaigns |

---

## 🛡️ Guardrails (Critical Rules)

### ✅ DO
1. Launch minimal → iterate based on revenue
2. Use Google Sheets as CRM first
3. Import data manually (no scraping)
4. Use `instagrapi` for all IG automation
5. WhatsApp: AI first touch → human handover
6. 1 proxy per 2 outreach accounts
7. Warm up new IG accounts 2+ weeks
8. Focus on Delhi NCR restaurants only

### ❌ DON'T
1. Don't build a dashboard in Phase 1
2. Don't scrape anything
3. Don't use Socionator for IG DMs
4. Don't let AI handle WhatsApp negotiation
5. Don't build upsell features yet
6. Don't over-engineer the tech stack

> Full guardrails: [`docs/guardrails.md`](docs/guardrails.md)

---

## 🏗️ Infrastructure (Phase 1)

### GCP Setup (Using $300 Free Credits)

| Resource | Spec | Purpose | Monthly Cost |
|----------|------|---------|-------------|
| VM 1 — App | e2-standard-2 (2 vCPU, 8GB) | FastAPI + LangGraph + Celery + Redis | ~$50 |
| VM 2 — Services | e2-standard-2 (2 vCPU, 8GB) | PostgreSQL + Evolution API + Chatwoot | ~$50 |
| Gemini API | Flash + Pro | LLM inference | ~$10-30 |
| Residential Proxies | 2-3 IPs | IG account safety | ~$15-30 |
| **Total** | | **Covered by GCP credits for ~3 months** | **~$135/mo** |

### External Services

| Service | Self-Hosted? | Purpose |
|---------|-------------|---------|
| Evolution API | ✅ Yes (Docker) | WhatsApp message sending |
| Chatwoot | ✅ Yes (Docker) | Unified inbox + human handover |
| PostgreSQL | ✅ Yes (Docker) | Agent state + logs only (not CRM) |
| Redis | ✅ Yes (Docker) | Celery task queue |
| Google Sheets API | ☁️ Cloud | CRM data layer |
| Gemini API | ☁️ Cloud | LLM inference |

---

## 🚀 Getting Started — Build Guide

### Prerequisites
- Python 3.11+
- Docker + Docker Compose
- GCP account with $300 credits
- 2-3 residential proxy IPs
- 4-5 warmed-up IG accounts
- 1+ WhatsApp Business number
- Google Sheets API credentials

### Step 1: Clone & Setup
```bash
git clone https://github.com/vibmedia/Kobi-temp.git
cd Kobi-temp

# Create virtual environment
python -m venv venv
source venv/bin/activate  # or .\venv\Scripts\activate on Windows

# Install dependencies
pip install -r requirements.txt

# Copy environment template
cp .env.example .env
# Edit .env with your credentials
```

### Step 2: Configure Environment Variables
```env
# Google
GOOGLE_SHEETS_CREDENTIALS_JSON=path/to/credentials.json
SPREADSHEET_ID=your_google_sheet_id

# Gemini
GEMINI_API_KEY=your_gemini_api_key

# Instagram (per account)
IG_USERNAME_1=selena.vibmedia
IG_PASSWORD_1=your_password
IG_PROXY_1=http://user:pass@proxy_ip:port

# WhatsApp
EVOLUTION_API_URL=http://your-vm:8080
EVOLUTION_API_KEY=your_evolution_key

# Chatwoot
CHATWOOT_URL=http://your-vm:3000
CHATWOOT_API_KEY=your_chatwoot_key

# Infrastructure
REDIS_URL=redis://localhost:6379/0
DATABASE_URL=postgresql://user:pass@localhost:5432/kobi
```

### Step 3: Deploy Infrastructure (Docker)
```bash
docker-compose up -d

# This starts:
# - PostgreSQL (agent state)
# - Redis (task queue)
# - Evolution API (WhatsApp)
# - Chatwoot (unified inbox)
```

### Step 4: Prepare Data
1. Create Google Sheet with tabs: `Influencer Pipeline`, `Brand Pipeline`, `Job Board`, `Campaign Tracker`, `Account Manager`, `Dashboard`
2. Import influencer CSV to `Influencer Pipeline` tab
3. Import brand CSV to `Brand Pipeline` tab

### Step 5: Warm Up IG Accounts
```
Week 1: Follows + Likes only
Week 2: Comments on food posts
Week 3: Start DMs (5/day)
```
> ⚠️ NEVER skip warmup. Cold accounts = immediate action block.

### Step 6: Run First Outreach
```bash
# Start the backend
uvicorn backend.main:app --reload --port 8000

# Start Celery workers
celery -A backend.worker worker --loglevel=info

# Trigger first batch
curl -X POST http://localhost:8000/api/outreach/batch \
  -H "Content-Type: application/json" \
  -d '{"batch_size": 5, "account": "selena.vibmedia"}'
```

---

## 📋 Phase 1 Sprint Plan

> See [`docs/ROADMAP.md`](docs/ROADMAP.md) for full details.

| Sprint | Duration | Focus |
|--------|----------|-------|
| Sprint 1 | Week 1-2 | Infrastructure + 500 influencer import + enrichment |
| Sprint 2 | Week 3-4 | IG outreach agent + reply classification |
| Sprint 3 | Week 5-6 | Onboarding agent + WhatsApp hybrid bot |
| Sprint 4 | Week 7-8 | Job board + campaign tracker + first paid deal |

---

## 🔍 Known Issues & Open Questions

> Full audit: [`docs/audit_findings.md`](docs/audit_findings.md)

### 🔴 Decisions Needed (Before Build)
1. **Job Board delivery for influencers:** How do influencers see jobs in Phase 1?
   - Option A: IG DM notifications (AI sends matching jobs)
   - Option B: Simple public web page
   - Option C: WhatsApp community group
2. **Points decay policy:** Do points expire after 6 months? 12 months? Never?
3. **No-show penalties:** What happens if an influencer accepts a job but doesn't show up?

### 🟡 Planned Additions
4. WhatsApp number ban recovery plan
5. DPDPA opt-out flow (Indian data protection compliance)
6. Conflict resolution process for brand-influencer disputes

---

## 📐 UI/UX Designs

### Image Mockups (in `docs/designs/`)
| Screen | File | Description |
|--------|------|-------------|
| Ops Dashboard | `01_ops_dashboard.png` | Main KPI dashboard with pipeline health |
| Account Manager | `02_account_manager.png` | Proxy groups + IG account health |
| Job Board (Mobile) | `03_job_board.png` | Influencer-facing job browser |
| Influencer Profile | `04_influencer_profile.png` | Internal profile card with stats |
| Campaign Tracker | `05_campaign_tracker.png` | Kanban-style campaign board |
| WhatsApp Outreach | `06_whatsapp_outreach.png` | AI outreach + conversation thread |
| Campaign Report | `07_campaign_report.png` | Brand-facing PDF report |
| Onboarding Chat | `08_onboarding_chat.png` | IG DM onboarding conversation flow |

### Interactive Prototypes (Stitch)
Project: **VibMedia Kobi — AI Influencer Agency**
6 interactive screens designed in Google Stitch covering the same views above.

---

## 📚 Documentation Map

| Doc | Purpose | When to Read |
|-----|---------|-------------|
| [`PRD.md`](docs/PRD.md) | Full product requirements | Before starting any feature |
| [`system_architecture.md`](docs/system_architecture.md) | 14 Mermaid diagrams | To understand system design |
| [`guardrails.md`](docs/guardrails.md) | DO/DON'T rules | Before making ANY decision |
| [`ROADMAP.md`](docs/ROADMAP.md) | Sprint plan | To plan your week |
| [`user_stories_phase1.md`](docs/user_stories_phase1.md) | Detailed user stories | Before implementing a feature |
| [`audit_findings.md`](docs/audit_findings.md) | Loopholes & clashes | Before assuming anything |
| [`resource_requirement_guide.md`](docs/resource_requirement_guide.md) | Knowledge research prompts | When exploring a new domain |
| [`outreach_templates.md`](templates/outreach_templates.md) | DM/WhatsApp message templates | When building outreach agent |
| `data/Blueprint/` | Business strategy & competitive audit | For business context |

---

## 🤝 Contributing

1. Always check [`guardrails.md`](docs/guardrails.md) before adding features
2. All code follows Python best practices (type hints, docstrings, tests)
3. No new infrastructure without Phase 1 completion
4. Test with 5 DMs before running any batch > 10

---

## 📄 License

Private — VibMedia. All rights reserved.
