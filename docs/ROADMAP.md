# Development Roadmap

## Phase 1: Foundation (Month 1-2)

### Sprint 1 (Week 1-2): Setup & Seeding
- [ ] Set up GCP VMs (2× e2-standard-2)
- [ ] Deploy Evolution API on VM 2
- [ ] Deploy Chatwoot on VM 2
- [ ] Connect 4-5 WhatsApp numbers to Evolution API
- [ ] Set up Socionator + purchase 5 proxies
- [ ] Create 3-5 recruiter IG profiles
- [ ] Create Google Sheets CRM (Brand + Influencer + Deal Tracker)

### Sprint 2 (Week 3-4): Scraping & Database
- [ ] Scrape 500+ Delhi NCR restaurants from GMB + Zomato
- [ ] Deduplicate and merge into master brand list
- [ ] Seed 200-300 food influencer profiles (manual + scraping)
- [ ] Scrape IG bios for emails and manager contacts
- [ ] Import all data to Google Sheets CRM

### Sprint 3 (Week 5-6): Outreach Begins
- [ ] Start WhatsApp drip to restaurants (5-10/day per number)
- [ ] Start IG DM outreach to influencers via Socionator
- [ ] Monitor replies in Chatwoot
- [ ] Close first 3-5 restaurant deals (free barter + paid)
- [ ] Execute first campaigns manually
- [ ] Document learnings and template optimization

### Sprint 4 (Week 7-8): Optimize & Close
- [ ] A/B test outreach templates (track response rates)
- [ ] Onboard first batch of influencers via manual chat
- [ ] Execute 3-5 full campaigns
- [ ] Collect content, manage QC manually
- [ ] Generate first campaign reports
- [ ] Pitch upsells (website + ads) to happy clients

**Milestone:** 3-5 paying brands, 100+ onboarded influencers, ₹50K-₹1.5L MRR

---

## Phase 2: AI-Assisted Operations (Month 3-5)

### Sprint 5-6 (Week 9-12): Backend + Agents
- [ ] Set up PostgreSQL on VM 2 (migrate from Sheets)
- [ ] Build FastAPI backend (API for agents, dashboard, CRM)
- [ ] Build LangGraph Influencer Onboarding Agent
  - [ ] IG bio scraping
  - [ ] Sentiment classification
  - [ ] Conversational data collection
  - [ ] Profile enrichment + tier assignment
- [ ] Build LangGraph Deal-to-Booking Agent
  - [ ] Custom hook generation
  - [ ] WhatsApp drip via Evolution API
  - [ ] NLP intent detection
  - [ ] Lead routing to Chatwoot
- [ ] Integrate Gemini API for agent reasoning

### Sprint 7-8 (Week 13-16): Dashboard + Workflows
- [ ] Build Next.js internal dashboard (v1)
  - [ ] Campaign queue view
  - [ ] Influencer database (search, filter, sort)
  - [ ] Brand database + pipeline stages
  - [ ] Agent activity logs
  - [ ] Approval gates (human-in-the-loop)
- [ ] Set up Temporal for campaign workflows
- [ ] Connect Temporal activities to LangGraph agents
- [ ] Implement gamification tier system
- [ ] Build continuous enrichment loop (monthly auto-update)

**Milestone:** 10-15 clients, 300+ influencers, agents handling 60% of ops, ₹3-5L MRR

---

## Phase 3: Productized Service (Month 6-9)

### Sprint 9-10 (Week 17-20): Client-Facing
- [ ] Build client dashboard (brand login)
  - [ ] Campaign overview + progress bar
  - [ ] Influencer shortlist approval
  - [ ] Content review (approve/reject/revise)
  - [ ] Reports (weekly auto-generated)
  - [ ] Invoice history
- [ ] Multi-tenant middleware (admin vs client roles)

### Sprint 11-12 (Week 21-24): Upsells & Scale
- [ ] WordPress website upsell workflow (via MCP)
- [ ] Meta Ads campaign launcher (API integration)
- [ ] Razorpay payment integration
- [ ] Contract generation (template-based)
- [ ] AI Reporting Agent (weekly auto-reports)
- [ ] Expand to 2-3 industries beyond food

**Milestone:** 25-40 clients, 1,000+ influencers, 80% agent autonomy, ₹8-15L MRR

---

## Phase 4: SaaS Platform (Month 10-18) — Future

> Questions to answer with Phase 1-3 data before building:
> - Multi-tenant DB or row-level security?
> - API-first or white-label UI?
> - Self-serve campaign builder?
> - Influencer self-service portal?
> - Mobile app for influencers?
> - ML recommender vs rule-based?
> - International expansion strategy?
