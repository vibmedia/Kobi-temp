# VibMedia — AI Influencer Agency

> AI-first influencer marketing agency. Agents handle outreach, onboarding, matching, and campaign execution. Humans supervise and close deals.

## Quick Links

| Document | Path | Description |
|---|---|---|
| PRD | [`docs/PRD.md`](docs/PRD.md) | Full product requirements |
| Roadmap | [`docs/ROADMAP.md`](docs/ROADMAP.md) | Phase-by-phase delivery plan |

## Project Structure

```
ai influencer management/
├── .agent/                     # AI agent framework (knowledge only)
│   └── brands/saas/ai-influencer-agency/  # Brand context
├── docs/                       # Documentation
│   ├── PRD.md                  # Product Requirements Document
│   └── ROADMAP.md              # Development roadmap
├── data/                       # Raw data (CSVs, scraped data)
│   ├── influencers/            # Influencer lists
│   └── brands/                 # Brand/restaurant lists
├── agents/                     # LangGraph agent code (Phase 2+)
│   ├── influencer_onboarding/  # Agent 1
│   └── deal_to_booking/        # Agent 2
├── backend/                    # FastAPI backend (Phase 2+)
├── dashboard/                  # Next.js dashboard (Phase 2+)
├── scripts/                    # Utility scripts (scraping, enrichment)
└── templates/                  # Outreach message templates
```

## Getting Started

1. Read [`docs/PRD.md`](docs/PRD.md) for full requirements
2. Seed influencer data in `data/influencers/`
3. Seed brand data in `data/brands/`
4. Set up Evolution API + Chatwoot on GCP
5. Start WhatsApp outreach (Phase 1)
