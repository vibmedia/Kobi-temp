# 🛡️ Guardrails — Do's and Don'ts

> Project-level and process-level rules for the AI Influencer Agency. Every team member (human or AI agent) must follow these.

---

## 🚦 Project-Level Guardrails

### ✅ DO

| # | Rule | Why |
|---|------|-----|
| 1 | **Build for Phase 1 launch ONLY** | No speculative features. If it's not in the Phase 1 scope, don't build it. |
| 2 | **Use Google Sheets as CRM first** | Zero cost, zero setup, full team visibility. Migrate to PostgreSQL in Phase 2. |
| 3 | **Import data manually** | Ops team provides curated CSVs. No scraping infrastructure needed. |
| 4 | **Use instagrapi for all IG automation** | Free, Python-native, full API control. Single tool for DMs + enrichment. |
| 5 | **WhatsApp: AI first touch → human handover** | AI sends personalized initial outreach (3-day drip). Human takes over via Chatwoot for all complex replies, negotiation, and deal closing. |
| 6 | **1 proxy per 2 outreach accounts** | Cost-effective while maintaining safety. Don't over-spend on proxies. |
| 7 | **Warm up new IG accounts for 2+ weeks** | Week 1: follows/likes. Week 2: comments. Week 3: DMs. No shortcuts. |
| 8 | **Focus on Delhi NCR restaurants only** | Don't expand geography or industry until Phase 1 revenue targets hit. |
| 9 | **Use the Job Board model** | Brands post jobs → influencers apply. Reduces manual matching overhead. |
| 10 | **Points-based incentives only** | No free-deal obligations. Influencers earn points voluntarily. |
| 11 | **Job Board via IG DM in Phase 1** | AI sends matching jobs to influencers via DM. They reply "interested" to apply. No web page yet. |
| 12 | **Points expire after 12 months** | Points decay after 12 months of inactivity to keep the system fresh. |
| 13 | **WhatsApp ban → shift or pause** | If a WhatsApp number gets banned, shift to next available number. If none, pause outreach until recovered. |
| 14 | **No-show policy: warning → ban → demotion** | 1st no-show = warning. 2nd = 7-day ban from jobs. 3rd = permanent tier demotion. |

### ❌ DON'T

| # | Rule | Why |
|---|------|-----|
| 1 | **Don't build a dashboard in Phase 1** | Google Sheets is the dashboard. A Next.js app is Phase 2. |
| 2 | **Don't scrape anything** | All data is manually sourced. No GMB/Zomato/IG scrapers. |
| 3 | **Don't use Socionator for IG DMs** | Socionator is manual cold-email fallback only. instagrapi is the DM tool. |
| 4 | **Don't let AI handle WhatsApp negotiation or deal-closing** | AI sends personalized first touch + answers FAQs only. All interested/complex replies route to Chatwoot for human sales. |
| 5 | **Don't build upsell features** | WordPress sites and Meta Ads are delivered manually as add-on services. |
| 6 | **Don't over-engineer the tech stack** | No Temporal, no Qdrant, no complex infra for Phase 1. Keep it simple. |
| 7 | **Don't assign 1 proxy per 1 outreach account** | 1 proxy : 2 outreach accounts is the model. Don't waste budget. |
| 8 | **Don't force free deals on influencers** | Use the points system instead. Free deals create resentment and pressure. |
| 9 | **Don't use Odoo or any external CRM** | Google Sheets → PostgreSQL migration path. No third-party CRM tools. |\r
| 10 | **Don't expand beyond food/restaurants** | Phase 1 = food niche only. Skin deep in other industries = failure. |\r
| 11 | **Don't send DMs from unwarmup'd accounts** | Minimum 2 weeks warmup. Skipping = ban risk. |

---

## ⚙️ Process-Level Guardrails

### Outreach

| DO | DON'T |
|----|-------|
| ✅ Start IG DMs at 5/day/account and ramp up slowly | ❌ Send 15+ DMs/day on a new account |
| ✅ Pause account immediately if challenge detected | ❌ Keep sending after a challenge/warning |
| ✅ Rotate between template variations (5+ per step) | ❌ Send the same template to everyone |
| ✅ Stop drip sequence when influencer replies | ❌ Send follow-ups after a reply |
| ✅ Personalize DMs with enriched data (name, niche) | ❌ Send generic "Hi, want brand deals?" messages |
| ✅ Track every DM sent in the CRM | ❌ Send DMs without logging them |
| ✅ Check account health daily | ❌ Ignore suspended/challenged accounts |

### Onboarding

| DO | DON'T |
|----|-------|
| ✅ Use conversational chat to collect data | ❌ Send a form link as the first interaction |
| ✅ Validate phone numbers and rates in real-time | ❌ Accept invalid data and fix later |
| ✅ Offer Google Form as a fallback if asked | ❌ Force chat if influencer prefers forms |
| ✅ Assign 0 points + Bronze tier on onboard | ❌ Start new influencers at higher tiers |
| ✅ Explain points system in welcome message | ❌ Leave influencers confused about how points work |

### Job Board

| DO | DON'T |
|----|-------|
| ✅ Define clear deliverables per job type | ❌ Post vague jobs with no criteria |
| ✅ Show points awarded on every job posting | ❌ Hide the points value |
| ✅ Filter jobs by influencer eligibility (tier, niche) | ❌ Show all jobs to all influencers |
| ✅ Set expiry dates on job postings | ❌ Leave stale jobs open indefinitely |
| ✅ Notify influencers of new matching jobs | ❌ Rely on influencers checking the board manually |
| ✅ Award points ONLY after ops-confirmed completion | ❌ Auto-award points on acceptance |

### Brand Outreach (Manual)

| DO | DON'T |
|----|-------|
| ✅ Use provided templates with {variables} filled | ❌ Wing it with improvised messages |
| ✅ Log every WhatsApp message in CRM | ❌ Send without tracking |
| ✅ Follow the 3-day drip structure | ❌ Send all 3 messages on day 1 |
| ✅ Classify replies when logging (positive/negative/etc.) | ❌ Leave reply status untracked |
| ✅ Blacklist rude refusals with reason | ❌ Re-contact blacklisted brands |
| ✅ Wait 30 days before re-engagement | ❌ Re-contact a non-responder within 2 weeks |

### Points & Reputation

| DO | DON'T |
|----|-------|
| ✅ Points are earned, never purchased | ❌ Allow points to be bought or transferred |
| ✅ Points scale with job complexity | ❌ Give equal points for a story review and a full campaign |
| ✅ Require mutual rating after job completion | ❌ Skip ratings |
| ✅ Check tier upgrades after every point award | ❌ Batch tier checks monthly |
| ✅ Notify influencer of tier upgrades immediately | ❌ Let tier changes go unannounced |

### Account & Proxy Management

| DO | DON'T |
|----|-------|
| ✅ Persist sessions via `dump_settings()` after every login | ❌ Login from scratch every time |
| ✅ Monitor proxy health daily | ❌ Ignore proxy downtime |
| ✅ Use residential proxies for IG | ❌ Use datacenter proxies (higher ban risk) |
| ✅ Rotate to next account when daily DM limit hit | ❌ Push past the rate limit |
| ✅ Give each account a unique recruiter persona | ❌ Use brand's own IG account for outreach |

### Data & Privacy

| DO | DON'T |
|----|-------|
| ✅ Validate all imported data before enrichment | ❌ Enrich invalid/duplicate records |
| ✅ Deduplicate by IG handle on every import | ❌ Allow duplicate profiles |
| ✅ Respect data deletion requests (DPDPA) | ❌ Refuse to delete influencer data |
| ✅ Store session files securely (not in git) | ❌ Commit IG session files to version control |
| ✅ Audit AI-generated messages periodically | ❌ Let AI DMs run unmonitored |

### LLM & AI Safety

| DO | DON'T |
|----|-------|
| ✅ Human-review first 10 AI-generated DMs per template | ❌ Deploy new templates without calibration |
| ✅ Set confidence thresholds for reply classification | ❌ Trust low-confidence classifications |
| ✅ Escalate uncertain classifications to human | ❌ Auto-act on ambiguous replies |
| ✅ Log all LLM calls with tokens/cost | ❌ Run LLM calls without cost tracking |
| ✅ Use Gemini Flash for repetitive tasks, Pro for complex | ❌ Use Pro for everything (burns budget) |

---

## 📋 Pre-Launch Checklist

Before going live with Phase 1:

- [ ] **IG Accounts:** 3-5 accounts created, warmed up for 2+ weeks
- [ ] **Proxy Groups:** 2-3 proxies configured, 1 per 2 accounts
- [ ] **Sessions:** All instagrapi sessions persisted and tested
- [ ] **CRM Sheets:** All tabs created with data validation rules
- [ ] **Templates:** 5+ DM template variations per drip step
- [ ] **Enrichment:** Test batch of 50 profiles enriched successfully
- [ ] **Job Board:** At least 3 seed job postings from manual brands
- [ ] **Points System:** Scoring table configured, tier thresholds set
- [ ] **WhatsApp Numbers:** 4-5 numbers warmed up for brand outreach
- [ ] **Chatwoot:** Unified inbox configured, team members added
- [ ] **LLM Calibration:** First 10 AI DMs reviewed and approved
- [ ] **Monitoring:** Daily report template ready
- [ ] **Backup:** Proxy failover tested, backup IG account ready
