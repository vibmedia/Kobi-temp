# Development Roadmap

> Launch-focused roadmap. Only Phase 1 has detailed sprints. Phases 2-4 are directional only.

## Phase 1: Launch (Month 1-2)

### Sprint 1 (Week 1-2): Setup + Data Import + Account Management
- [ ] Set up GCP VMs (2× e2-standard-2)
- [ ] Deploy Evolution API + Chatwoot on VM 2
- [ ] Connect 4-5 WhatsApp numbers to Evolution API (manual outreach)
- [ ] Purchase 2-3 residential proxies (1 proxy : 2 accounts)
- [ ] Create 3-5 recruiter IG profiles with personas
- [ ] Start warmup protocol (follows + likes — no DMs yet)
- [ ] Set up instagrapi sessions, bind to proxy groups
- [ ] Create Google Sheets CRM (Brand + Influencer + Job Board + Dashboard tabs)
- [ ] Import first batch of influencer CSV (200-300 Delhi NCR food handles)
- [ ] Import first batch of restaurant CSV (manually sourced)

### Sprint 2 (Week 3-4): Enrichment + Outreach System
- [ ] Run enrichment agent on imported influencer profiles (instagrapi)
- [ ] Calculate engagement rates, quality scores, assign tiers
- [ ] Classify niches from enriched bio/caption data
- [ ] Import + validate brand data (phone numbers, normalize cuisines)
- [ ] Set up DM template pool (5+ variations per drip step)
- [ ] Begin IG DM outreach (instagrapi, 5 DMs/day/account, ramping)
- [ ] Set up reply classification pipeline (Gemini Flash)
- [ ] Begin manual WhatsApp brand outreach (templates + CRM logging)

### Sprint 3 (Week 5-6): Onboarding + Job Board + First Deals
- [ ] Deploy conversational onboarding agent (IG DM chat flow)
- [ ] Onboard first batch of positive-reply influencers
- [ ] Set up Job Board (Google Sheet tab or simple web form)
- [ ] Configure points system (scoring table, tier thresholds)
- [ ] Post first 3-5 seed jobs from early brand conversations
- [ ] Close first 3-5 restaurant deals (manual WhatsApp closing)
- [ ] Execute first campaigns (manual coordination)

### Sprint 4 (Week 7-8): Campaign Execution + Optimize
- [ ] Execute 3-5 full campaigns through lifecycle
- [ ] Award first points, check tier upgrade logic
- [ ] A/B test outreach templates (track reply rates per variation)
- [ ] Content QC on first campaign deliverables
- [ ] Generate first campaign reports for brands
- [ ] Mutual ratings: brands rate influencers, influencers rate brands
- [ ] Gather learnings, optimize templates and processes
- [ ] Daily/weekly reporting operational

**Milestone:** 3-5 paying brands, 100+ onboarded influencers, Job Board active, Points system live, ₹50K-₹1.5L MRR

---

## Phase 2: AI-Assisted Operations (Month 3-5) — Directional

- Migrate from Google Sheets to PostgreSQL + custom Next.js dashboard
- Full LangGraph agent deployment (Outreach, Onboarding, Campaign, Enrichment)
- Temporal workflow engine for campaign lifecycle
- Continuous enrichment loop (monthly auto-update profiles)
- Expand to 300-500 influencers, 10-15 brand clients
- **Target:** ₹3-5L MRR

## Phase 3: Productized Service (Month 6-9) — Directional

- Client-facing dashboard (brands login to manage campaigns)
- Qdrant vector search for semantic influencer matching
- Razorpay payment integration
- Contract generation (template-based, e-signature)
- AI Reporting Agent (auto weekly reports)
- Expand to 2-3 industries beyond food
- **Target:** ₹8-15L MRR

## Phase 4: SaaS Platform (Month 10-18) — Future

> Not planned — decisions driven by Phase 1-3 learnings.
