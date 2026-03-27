# 📋 User Stories — Phase 1 Launch (AI Influencer Agency)

> Comprehensive user stories for Phase 1 (Month 1-2) launch. Organized by epic, prioritized by P0/P1/P2.
>
> **Format:** `As a [user], I want to [action], so that [value].`
> **Acceptance Criteria:** Testable conditions under each story.
>
> **Key Assumptions:**
> - All raw data (influencer lists, brand lists) is **provided manually** — no automated scraping
> - AI outreach (DMs) uses **instagrapi only** — Socionator is manual/cold-email only
> - WhatsApp brand outreach is **manual** (not AI agent team)
> - Upsell services (WordPress, Meta Ads) are **manual** — not part of AI system
> - Proxy allocation: **1 proxy per 2 IG accounts**

---

## Epic 1: Influencer Data Import & Enrichment 🔍

### P0 — Must Have

**US-1.1** | As an **operations manager**, I want to import influencer data from CSV/Google Sheets (handles, names, follower counts), so that I can seed the database with manually sourced creators.
- [ ] AC: CSV import with column mapping
- [ ] AC: Supports batch import of 100-1000 profiles at a time
- [ ] AC: Deduplication by IG handle on import
- [ ] AC: Validation errors reported (missing handle, invalid format)

**US-1.2** | As an **operations manager**, I want imported influencer profiles automatically enriched with live data (follower count, post count, average likes, average comments, bio email) via instagrapi, so that I can evaluate them without manual checking.
- [ ] AC: Enrichment runs on each imported profile
- [ ] AC: Engagement rate calculated as (avg likes + avg comments) / followers × 100
- [ ] AC: Last 12 posts analyzed for engagement metrics
- [ ] AC: Email/YouTube/Linktree links extracted from bio if present

**US-1.3** | As an **operations manager**, I want influencers automatically classified into tiers (Nano: 1K-10K, Micro: 10K-50K, Mid: 50K-200K, Macro: 200K+), so that I can filter them by size for campaigns.
- [ ] AC: Tier assigned based on enriched follower count
- [ ] AC: Tier displayed in database
- [ ] AC: Filter/sort by tier in the spreadsheet

**US-1.4** | As an **operations manager**, I want a quality score (0-100) calculated for each influencer based on engagement rate, content consistency, and follower authenticity, so that I can prioritize high-quality creators.
- [ ] AC: Quality score algorithm defined and documented
- [ ] AC: Score computed from enriched data
- [ ] AC: Influencers with score < 30 flagged as "low quality"

**US-1.5** | As an **operations manager**, I want the influencer database stored in a Google Sheet with standardized columns, so that the team can access and filter it easily.
- [ ] AC: Columns: Handle, Name, Followers, Tier, Engagement Rate, Quality Score, Niche, Location, Email, Phone, Bio, Profile URL, Points, Status, Date Added
- [ ] AC: Data validation on Tier and Status columns
- [ ] AC: Conditional formatting for quality score (red < 30, yellow 30-60, green > 60)

### P1 — Should Have

**US-1.6** | As an **operations manager**, I want niche/content type auto-classified from bio text and recent post captions (food reviews, cooking, lifestyle+food, restaurant visits), so that matching is easier.
- [ ] AC: Niche classified from enriched bio and caption data
- [ ] AC: Multiple niche tags supported per influencer
- [ ] AC: Manual override available

**US-1.7** | As an **operations manager**, I want potentially fake/bot accounts flagged by analyzing follower-to-engagement ratio from enriched data, so that I don't waste outreach on inauthentic profiles.
- [ ] AC: Flag accounts with engagement rate < 0.5% AND followers > 10K
- [ ] AC: Flag accounts with generic/spam comments > 30%
- [ ] AC: Flagged profiles marked as "Review Required"

### P2 — Nice to Have

**US-1.8** | As an **operations manager**, I want periodic re-enrichment of existing profiles (monthly), so that data stays fresh and tier changes are detected.
- [ ] AC: Re-enrichment triggered monthly for all profiles
- [ ] AC: Only changed fields updated (no data loss)
- [ ] AC: Tier changes flagged and logged

---

## Epic 2: Influencer Outreach (IG DMs via instagrapi) 📩

### P0 — Must Have

**US-2.1** | As an **outreach coordinator**, I want to send personalized DMs to influencers from recruiter-styled IG accounts using instagrapi, so that I can invite them to join our platform.
- [ ] AC: DM personalized with {influencer_name} and {follower_count}
- [ ] AC: Message follows approved template
- [ ] AC: Sent from designated recruiter account (e.g., "Selena - VibMedia Recruiter")
- [ ] AC: Sent via instagrapi `direct_send()` API

**US-2.2** | As an **outreach coordinator**, I want to manage multiple IG outreach accounts (3-5 accounts), with every 2 accounts sharing a proxy, so that I can distribute DM volume and reduce ban risk.
- [ ] AC: Every 2 IG accounts bound to 1 shared proxy IP
- [ ] AC: Session persistence across restarts via `dump_settings()`/`load_settings()`
- [ ] AC: Account health status visible (active/challenged/banned)

**US-2.3** | As an **outreach coordinator**, I want DM sending to follow a rate-limiting schedule (Day 1: 5 DMs, Day 2: 6, Day 3: 8, max 15/day after warmup), so that accounts don't get flagged.
- [ ] AC: Daily DM count enforced per account
- [ ] AC: Ramp-up schedule configurable
- [ ] AC: Automatic pause if challenge detected

**US-2.4** | As an **outreach coordinator**, I want to define a 3-step DM sequence (Day 1: intro, Day 3: follow-up, Day 5: final), so that non-responders get automatic follow-ups.
- [ ] AC: 3-step drip sequence with configurable delays
- [ ] AC: Sequence stops if influencer replies
- [ ] AC: Different templates for each step

**US-2.5** | As an **outreach coordinator**, I want the pipeline status of each influencer updated automatically (Discovered → DM Sent → Follow-up → Replied), so that I know where everyone stands.
- [ ] AC: Status updates in Google Sheet after each action
- [ ] AC: Timestamp recorded for each status change
- [ ] AC: Status transition rules enforced

**US-2.6** | As an **outreach coordinator**, I want to be notified immediately when an influencer replies to a DM, so that I can respond quickly.
- [ ] AC: Notification via WhatsApp/Telegram to operations team
- [ ] AC: Reply content included in notification
- [ ] AC: Influencer profile link included

### P1 — Should Have

**US-2.7** | As an **outreach coordinator**, I want replies automatically classified by sentiment (positive, negative, question, spam), so that I can prioritize positive leads.
- [ ] AC: Classification runs on each incoming DM
- [ ] AC: Positive replies highlighted/tagged
- [ ] AC: Negative replies auto-stop the sequence

**US-2.8** | As an **outreach coordinator**, I want template variations (5+ per step) that rotate randomly, so that DMs don't appear scripted.
- [ ] AC: Template pool with minimum 5 variations per step
- [ ] AC: Random selection from pool on each send
- [ ] AC: Track which template sent to which influencer

**US-2.9** | As an **outreach coordinator**, I want IG challenge/verification prompts handled automatically via instagrapi's challenge resolver, so that accounts don't stay locked.
- [ ] AC: Email verification challenges resolved automatically
- [ ] AC: SMS challenges flagged for human resolution
- [ ] AC: Account paused if challenge can't be resolved

**US-2.10** | As an **outreach coordinator**, I want a daily outreach report showing DMs sent, replies received, positive rate, and account health, so that I can track outreach performance.
- [ ] AC: Daily report generated at 11 PM IST
- [ ] AC: Metrics: total sent, total replies, positive %, negative %, account health
- [ ] AC: Report sent via WhatsApp/Telegram

### P2 — Nice to Have

**US-2.11** | As an **outreach coordinator**, I want outreach messages personalized with insights from the influencer's enriched bio data, so that DMs feel genuine.
- [ ] AC: Bio keywords and niche used to personalize message
- [ ] AC: One relevant detail inserted into DM template
- [ ] AC: Manual review option before sending

**US-2.12** | As an **outreach coordinator**, I want a fallback cold email outreach (via Socionator, manually) for influencers who don't respond to DMs, so that I maximize contact rate.
- [ ] AC: Socionator used manually for cold email sending
- [ ] AC: Email only sent if email address available from enrichment
- [ ] AC: Tracked in CRM but not part of automated AI pipeline

---

## Epic 3: Influencer Onboarding (Conversational via instagrapi) 💬

### P0 — Must Have

**US-3.1** | As an **onboarding agent**, I want to start a conversational onboarding flow via IG DM (instagrapi) when an influencer replies positively, so that I can collect their information naturally.
- [ ] AC: Onboarding triggered by positive reply classification
- [ ] AC: Conversation follows defined flow via instagrapi DM thread
- [ ] AC: Fallback to Google Form link if influencer requests it

**US-3.2** | As an **onboarding agent**, I want to collect the following data via conversation: full name, location/area, WhatsApp phone, deal preference (barter/paid/both), rate per reel (₹), and content formats, so that I can build their profile.
- [ ] AC: Each field collected with a natural question
- [ ] AC: Data validated in real-time (phone format, ₹ amount)
- [ ] AC: Conversation can handle out-of-order responses

**US-3.3** | As an **onboarding agent**, I want collected data automatically saved to the influencer database (Google Sheet), so that profiles are complete without manual entry.
- [ ] AC: Data mapped to correct columns
- [ ] AC: Status updated to "Onboarded"
- [ ] AC: Timestamp of onboarding recorded

**US-3.4** | As an **onboarding agent**, I want newly onboarded influencers to start with 0 points and Bronze status, so that they enter the points/reputation system at the baseline.
- [ ] AC: All new onboards start at 0 points, Bronze tier
- [ ] AC: Points system explained to influencer in welcome message
- [ ] AC: Tier and points displayed in database

### P1 — Should Have

**US-3.5** | As an **onboarding agent**, I want to detect when an influencer's stated rate is significantly above market average for their tier, so that I can flag it for human review.
- [ ] AC: Market rate ranges defined per tier
- [ ] AC: Flag if rate > 150% of tier average
- [ ] AC: Human escalation notification sent

**US-3.6** | As an **onboarding agent**, I want optional data fields (manager contact, UPI ID, shipping address) collected in a follow-up conversation, so that profiles are enriched over time.
- [ ] AC: Follow-up conversation triggered 3 days after onboarding
- [ ] AC: Only uncollected fields requested
- [ ] AC: Optional fields don't block onboarding completion

**US-3.7** | As an **onboarding agent**, I want to send a welcome message with next steps after onboarding completes, so that the influencer knows what to expect.
- [ ] AC: Welcome message sent via IG DM (instagrapi)
- [ ] AC: Includes: how the Job Board works, how points are earned, commission structure
- [ ] AC: WhatsApp group invite included if available

---

## Epic 4: Brand Data Import & Enrichment 🏪

### P0 — Must Have

**US-4.1** | As a **sales manager**, I want to import restaurant/brand data from CSV/Google Sheets (name, phone, address, cuisine, rating), so that I can build a brand lead database from manually sourced data.
- [ ] AC: CSV import with column mapping
- [ ] AC: Supports batch import of 100-1000 leads at a time
- [ ] AC: Deduplication by name + area
- [ ] AC: Validation errors reported

**US-4.2** | As a **sales manager**, I want imported brand data enriched with additional fields (popular dishes, menu items, Zomato rating) from provided source files, so that outreach messages can be personalized.
- [ ] AC: Enrichment merges data from multiple provided sources
- [ ] AC: Popular dishes field populated for message personalization
- [ ] AC: Cuisine type and price range standardized

**US-4.3** | As a **sales manager**, I want restaurant leads stored in a Google Sheet with standardized columns, so that the team can manage brand outreach.
- [ ] AC: Columns: Name, Phone, Address, Area, Cuisine, Rating, Popular Dishes, Pipeline Status, Date Added, Notes
- [ ] AC: Deduplicated by name + area
- [ ] AC: Status column with data validation

**US-4.4** | As a **sales manager**, I want phone numbers validated (10-digit Indian mobile format) and flagged whether likely WhatsApp-enabled, so that I know which leads can be contacted via WhatsApp.
- [ ] AC: Phone numbers validated on import
- [ ] AC: Landline numbers flagged (not WhatsApp-capable)
- [ ] AC: Mobile numbers marked as WhatsApp-probable

### P1 — Should Have

**US-4.5** | As a **sales manager**, I want restaurant leads scored by potential value (rating × price range × review count), so that I can prioritize high-value targets.
- [ ] AC: Lead score formula defined and documented
- [ ] AC: Score calculated from imported/enriched data
- [ ] AC: Top 20% highlighted as "Hot Leads"

---

## Epic 5: Brand Outreach (WhatsApp — Manual) 📱

> ⚠️ **Note:** WhatsApp outreach is a **manual process** managed by the sales team. It is NOT part of the AI agent pipeline. The system provides templates, tracking, and CRM integration only.

### P0 — Must Have

**US-5.1** | As a **sales manager**, I want pre-written WhatsApp outreach templates with dynamic variables ({restaurant_name}, {popular_dish}), so that I can personalize messages quickly when sending manually.
- [ ] AC: Template library with 5+ variations per drip step
- [ ] AC: Variables auto-filled from CRM data
- [ ] AC: Copy-to-clipboard functionality

**US-5.2** | As a **sales manager**, I want a 3-day drip guide (Day 1: custom hook, Day 2: social proof, Day 3: free deal offer), so that I follow a consistent outreach structure.
- [ ] AC: Day 1: "We noticed your {dish}... interested in a food reel?"
- [ ] AC: Day 2: "{nearby_restaurant} got 45K views from our campaign..."
- [ ] AC: Day 3: "FREE influencer visit — creator comes, eats, shoots. No cost."
- [ ] AC: Guide accessible from CRM

**US-5.3** | As a **sales manager**, I want to log each WhatsApp message I send in the CRM, so that pipeline status is tracked.
- [ ] AC: Status auto-updates when message logged (Raw Lead → Contacted → Follow-up 1 → Follow-up 2)
- [ ] AC: Timestamp for each status transition
- [ ] AC: History of all messages logged per lead

**US-5.4** | As a **sales manager**, I want to log replies and classify them (positive, neutral, negative, price_objection), so that I can prioritize hot leads.
- [ ] AC: Reply logged with classification tag
- [ ] AC: Positive leads highlighted in pipeline sheet
- [ ] AC: Pipeline status updated on reply

### P1 — Should Have

**US-5.5** | As a **sales manager**, I want templates in both English and Hinglish (Hindi-English mix), so that messages feel natural to Delhi restaurant owners.
- [ ] AC: Minimum 3 Hinglish variations per drip step
- [ ] AC: Accessible alongside English templates

**US-5.6** | As a **sales manager**, I want a blacklist system for restaurants that explicitly refuse or are rude, so that we don't contact them again.
- [ ] AC: "Blacklisted" status in pipeline
- [ ] AC: Reason for blacklisting recorded
- [ ] AC: Blacklisted leads excluded from daily action items

---

## Epic 6: Job Board System 📌

> 🆕 **NEW** — Brands post jobs autonomously, influencers accept based on posted criteria.

### P0 — Must Have

**US-6.1** | As a **brand/operations manager**, I want to post a job (campaign brief) on the Job Board with criteria (niche, tier range, deal type, location, deliverables, budget), so that matched influencers can self-select.
- [ ] AC: Job posting form with all criteria fields
- [ ] AC: Job visible to eligible influencers
- [ ] AC: Job has status: Open, Filled, Closed, Expired
- [ ] AC: Multiple jobs can be open simultaneously per brand

**US-6.2** | As an **influencer**, I want to browse available jobs filtered by my niche, tier, and location, so that I can find relevant opportunities.
- [ ] AC: Job listings filtered by influencer's profile match
- [ ] AC: Each job shows: brand name, deliverables, deal type, budget/barter details, points offered
- [ ] AC: Only jobs matching influencer tier visible

**US-6.3** | As an **influencer**, I want to accept/apply to a job posting, so that I can express interest in a campaign.
- [ ] AC: Apply button per job
- [ ] AC: Application includes influencer's profile data
- [ ] AC: Application status tracked: Applied, Accepted, Rejected, Completed

**US-6.4** | As a **brand/operations manager**, I want to review applications from influencers and accept/reject them, so that I can select the best creators for my campaign.
- [ ] AC: Applicant list with profile summary, quality score, points, engagement rate
- [ ] AC: Accept/Reject action per applicant
- [ ] AC: Accepted influencers notified via IG DM (instagrapi)
- [ ] AC: Rejected influencers notified politely

**US-6.5** | As an **operations manager**, I want job postings to display the influencer's total points/reputation alongside their profile, so that brands can make informed decisions.
- [ ] AC: Points total visible on applicant card
- [ ] AC: Job completion history visible (number of past jobs)
- [ ] AC: Average rating visible if available

**US-6.6** | As an **operations manager**, I want jobs to support all deal types (Barter, Paid, Paid+Barter, Performance), so that the board covers all campaign models.
- [ ] AC: Deal type clearly displayed on job card
- [ ] AC: Payment terms/barter details shown
- [ ] AC: Commission structure visible to influencer

### P1 — Should Have

**US-6.7** | As an **influencer**, I want to see job recommendations based on my profile and past performance, so that I find the best matches faster.
- [ ] AC: Recommended section at top of job board
- [ ] AC: Matching based on niche, tier, location, past job completion
- [ ] AC: Points/tier weighting in recommendations

**US-6.8** | As an **operations manager**, I want jobs to auto-expire after a configurable period (7/14/30 days), so that stale postings don't clutter the board.
- [ ] AC: Expiry date set on creation
- [ ] AC: Expired jobs moved to archive
- [ ] AC: Notification to brand before expiry

**US-6.9** | As a **brand/operations manager**, I want to post different job sizes — from small review jobs (visit + quick story) to full collaboration (reel + stories + post), so that I can use the board for all engagement levels.
- [ ] AC: Job type field: Quick Review, Standard Collab, Full Campaign, Long-term Partnership
- [ ] AC: Points awarded scale with job size
- [ ] AC: Deliverables checklist per job type

---

## Epic 7: Points & Reputation System ⭐

> 🆕 **NEW** — Replaces the "Free Deal Obligation" model. Influencers earn points from jobs, building a visible reputation.

### P0 — Must Have

**US-7.1** | As an **operations manager**, I want a points system where influencers earn points for completing jobs (small review = fewer points, full collab = more points), so that there is a clear incentive to do more work through our platform.
- [ ] AC: Points table: Quick Review = 10pts, Standard Collab = 25pts, Full Campaign = 50pts, Long-term = 100pts
- [ ] AC: Points added after job completion confirmed
- [ ] AC: Points visible in influencer profile

**US-7.2** | As an **influencer**, I want my total points displayed on my profile and in job applications, so that brands can see my track record.
- [ ] AC: Points total displayed on all profile views
- [ ] AC: Points breakdown (by job type) accessible
- [ ] AC: Points history with dates and job names

**US-7.3** | As an **operations manager**, I want tier upgrades to be based on points rather than just deal count, so that the system rewards quality engagement.
- [ ] AC: Bronze: 0-49 points (newly onboarded)
- [ ] AC: Silver: 50-199 points (active contributor)
- [ ] AC: Gold: 200-499 points (trusted creator)
- [ ] AC: Platinum: 500+ points (top performer)

**US-7.4** | As an **influencer**, I want tier upgrades to unlock better perks (priority in job board, higher visibility, negotiation autonomy), so that I have incentive to earn more points.
- [ ] AC: Bronze: Basic job access
- [ ] AC: Silver: Priority job matching, profile badge
- [ ] AC: Gold: VIP brand access, profile featured in recommendations
- [ ] AC: Platinum: Premium brands, negotiation autonomy, top listing

**US-7.5** | As an **operations manager**, I want points displayed on job postings (e.g., "This job awards 25 points"), so that influencers know the value before accepting.
- [ ] AC: Points clearly shown on every job card
- [ ] AC: Points awarded scale with job complexity
- [ ] AC: Bonus multiplier for high-rated brands (optional)

### P1 — Should Have

**US-7.6** | As an **operations manager**, I want brands to rate influencer performance after job completion (1-5 stars), so that quality is tracked.
- [ ] AC: Rating prompt sent to brand after job completion
- [ ] AC: Average rating displayed on influencer profile
- [ ] AC: Rating below 3 stars triggers review

**US-7.7** | As an **influencer**, I want to rate the brand/restaurant experience (1-5 stars), so that other influencers know what to expect.
- [ ] AC: Rating prompt sent to influencer after job completion
- [ ] AC: Brand rating visible on job board postings
- [ ] AC: Mutual rating system builds trust

**US-7.8** | As an **operations manager**, I want a leaderboard showing top influencers by points, so that competition drives engagement.
- [ ] AC: Monthly and all-time leaderboards
- [ ] AC: Top 10 displayed in job board
- [ ] AC: Leaderboard shared in WhatsApp community

---

## Epic 8: Account & Proxy Management System 🔧

> 🆕 **NEW** — Centralized management for all IG outreach accounts and proxy groups.

### P0 — Must Have

**US-8.1** | As an **operations manager**, I want a centralized account management dashboard showing all IG outreach accounts with their status, assigned proxy, persona name, and health, so that I have full visibility.
- [ ] AC: Dashboard lists all IG accounts
- [ ] AC: Each account shows: username, persona name, proxy group, status (active/challenged/banned/cooldown), last activity, DMs sent today
- [ ] AC: Real-time health indicators

**US-8.2** | As an **operations manager**, I want to define proxy groups where 1 proxy serves 2 IG accounts, so that I optimize proxy costs while maintaining safety.
- [ ] AC: Proxy group configuration: 1 proxy → 2 accounts
- [ ] AC: Proxy assignment visible per account
- [ ] AC: Proxy health status (up/down/slow) monitored
- [ ] AC: Alert if proxy goes down

**US-8.3** | As an **operations manager**, I want to configure account settings (DM rate limit, warmup stage, auto-pause rules) per account, so that each account operates within safe parameters.
- [ ] AC: Per-account settings: max DMs/day, warmup stage, pause triggers
- [ ] AC: Settings editable without code changes
- [ ] AC: Defaults applied for new accounts

**US-8.4** | As an **operations manager**, I want to see session status for each instagrapi account (logged in, session expired, needs re-auth), so that I can proactively fix authentication issues.
- [ ] AC: Session status checked periodically
- [ ] AC: Expired sessions flagged for re-login
- [ ] AC: Session files backed up

### P1 — Should Have

**US-8.5** | As an **operations manager**, I want account warmup progress tracked (Week 1: follows/likes, Week 2: comments, Week 3: DMs), so that new accounts don't DM before they're ready.
- [ ] AC: Warmup schedule per account with stage tracking
- [ ] AC: DMs blocked until warmup complete
- [ ] AC: Activity levels configurable per account

**US-8.6** | As an **operations manager**, I want to rotate accounts automatically when one reaches its daily DM limit, so that outreach continues uninterrupted.
- [ ] AC: Auto-rotation to next available account
- [ ] AC: Rotation logic respects proxy group boundaries
- [ ] AC: Exhaustion alert if all accounts at limit

**US-8.7** | As an **operations manager**, I want account activity logs (DMs sent, challenges hit, errors) stored per account per day, so that I can audit and debug issues.
- [ ] AC: Logs stored per account per day
- [ ] AC: Searchable by date, account, event type
- [ ] AC: Retained for 30 days minimum

---

## Epic 9: CRM & Pipeline Management 📊

### P0 — Must Have

**US-9.1** | As an **operations manager**, I want a Google Sheet-based CRM with separate tabs for Brand Pipeline, Influencer Pipeline, and Job Board, so that the team has a single source of truth.
- [ ] AC: Brand Pipeline tab with 11 stages
- [ ] AC: Influencer Pipeline tab with 11 stages
- [ ] AC: Job Board tab with job status tracking
- [ ] AC: Dashboard tab with summary metrics

**US-9.2** | As an **operations manager**, I want daily action items auto-generated from pipeline data (e.g., "5 leads need Follow-up 2 today"), so that nothing falls through cracks.
- [ ] AC: Action items calculated from status + date rules
- [ ] AC: Displayed in dashboard tab
- [ ] AC: Sent as morning WhatsApp summary at 9 AM IST

**US-9.3** | As an **operations manager**, I want to track deal status for each campaign (lead → active → completed → paid), so that I know revenue pipeline health.
- [ ] AC: Deal tracker tab with: brand, influencers, deal type, amount, status
- [ ] AC: Total pipeline value calculated
- [ ] AC: Won/lost ratio tracked

### P1 — Should Have

**US-9.4** | As an **operations manager**, I want aging alerts when leads stay in a stage too long (>3 days in Contacted, >7 days in Follow-up), so that stale leads get actioned.
- [ ] AC: Aging rules configurable per stage
- [ ] AC: Alerts sent via WhatsApp
- [ ] AC: Stale leads highlighted in red

**US-9.5** | As an **operations manager**, I want conversion rate metrics per pipeline stage (Contacted → Interested: X%), so that I can identify and fix bottlenecks.
- [ ] AC: Conversion rates calculated per stage transition
- [ ] AC: Displayed in dashboard
- [ ] AC: Updated daily

---

## Epic 10: Campaign Execution 🎬

### P0 — Must Have

**US-10.1** | As an **operations manager**, I want to match available influencers to a brand campaign (or job board posting) based on niche, tier, location, points, and deal type, so that I can propose a shortlist.
- [ ] AC: Filter influencers by: niche, tier range, area, deal preference, minimum points
- [ ] AC: Shortlist of 5-10 influencers generated
- [ ] AC: Quality score, engagement rate, and points visible in shortlist

**US-10.2** | As an **operations manager**, I want to track each campaign through its lifecycle (matching → outreach → visit → content → posting → payment), so that nothing is missed.
- [ ] AC: Campaign tracker with step-by-step status
- [ ] AC: Dates for each step recorded
- [ ] AC: Overdue steps highlighted

**US-10.3** | As an **operations manager**, I want to handle all 4 deal types (Barter, Paid, Paid+Barter, Performance) with different workflows, so that each deal is managed correctly.
- [ ] AC: Deal type determines: product handling, payment timing, commission calculation
- [ ] AC: Workflow steps displayed based on deal type
- [ ] AC: Commission (20%) auto-calculated

**US-10.4** | As a **brand owner**, I want to receive a campaign completion report with reach, engagement, and content links, so that I can see the ROI.
- [ ] AC: Report includes: influencer profiles, post links, view/like/comment counts
- [ ] AC: Sent as PDF via WhatsApp
- [ ] AC: Generated within 48 hours of campaign completion

**US-10.5** | As an **operations manager**, I want points awarded to influencers automatically after campaign/job completion, so that the points system stays current.
- [ ] AC: Points added based on job type after completion
- [ ] AC: Tier upgrade checked and applied if threshold crossed
- [ ] AC: Influencer notified of points earned and any tier change

### P1 — Should Have

**US-10.6** | As an **operations manager**, I want content QC checks before posting (hashtags present, brand mention included, video length OK), so that brand requirements are met.
- [ ] AC: Checklist of brand brief requirements
- [ ] AC: Influencer uploads draft for review
- [ ] AC: Approval/rejection flow with feedback

**US-10.7** | As an **operations manager**, I want to coordinate the restaurant visit date between brand and influencer via WhatsApp/DM, so that both parties confirm.
- [ ] AC: Date proposal message sent to both parties
- [ ] AC: Confirmation tracked in campaign sheet
- [ ] AC: Reminder sent 24 hours before visit

---

## Epic 11: Payments & Commission Tracking 💰

### P0 — Must Have

**US-11.1** | As an **operations manager**, I want to track brand payments per campaign (amount, payment method, payment date, status), so that I know who has paid.
- [ ] AC: Payment status: Pending, Received, Overdue
- [ ] AC: UPI transaction ID recorded
- [ ] AC: Invoice generated (manual template for Phase 1)

**US-11.2** | As an **operations manager**, I want to calculate and track influencer commissions (20% deducted from deal value), so that payouts are accurate.
- [ ] AC: Gross amount, commission (20%), net payout calculated
- [ ] AC: Payout status: Pending, Paid
- [ ] AC: Minimum payout threshold: ₹1,000

**US-11.3** | As an **influencer**, I want to know my earnings, commission breakdown, and points earned per job, so that I trust the payment process.
- [ ] AC: Earnings summary sent via DM/WhatsApp after each deal
- [ ] AC: Shows: gross deal value, commission deducted, net payout, points awarded
- [ ] AC: UPI payment confirmation shared

---

## Epic 12: Reporting & Analytics 📈

### P0 — Must Have

**US-12.1** | As an **operations manager**, I want a daily operations summary (DMs sent, replies, brands contacted, active campaigns, job board activity, revenue pipeline), so that I have a pulse on the business.
- [ ] AC: Summary generated at 10 PM IST daily
- [ ] AC: Sent via WhatsApp to ops team
- [ ] AC: Includes key metrics and action items

**US-12.2** | As an **operations manager**, I want a weekly business report (new leads, conversions, active campaigns, job board stats, revenue, pending payments), so that I can share progress with stakeholders.
- [ ] AC: Report generated every Sunday at 8 PM IST
- [ ] AC: Includes week-over-week comparison
- [ ] AC: PDF format for sharing

### P1 — Should Have

**US-12.3** | As an **operations manager**, I want outreach A/B test results (which DM template gets highest reply rate), so that I can optimize messaging.
- [ ] AC: Reply rate tracked per template variation
- [ ] AC: Statistical significance flagged when enough data
- [ ] AC: Top-performing templates highlighted

**US-12.4** | As an **operations manager**, I want a job board analytics view (jobs posted, applications, fill rate, average time-to-fill), so that I can optimize the job board experience.
- [ ] AC: Metrics tracked per job posting
- [ ] AC: Overall board health metrics in dashboard
- [ ] AC: Underperforming job types identified

---

## Summary — Story Count

| Epic | P0 | P1 | P2 | Total |
|------|----|----|-----|-------|
| 1. Data Import & Enrichment | 5 | 2 | 1 | **8** |
| 2. Influencer Outreach (IG DM) | 6 | 4 | 2 | **12** |
| 3. Influencer Onboarding | 4 | 3 | 0 | **7** |
| 4. Brand Data Import | 4 | 1 | 0 | **5** |
| 5. Brand Outreach (WhatsApp — Manual) | 4 | 2 | 0 | **6** |
| 6. Job Board System 🆕 | 6 | 3 | 0 | **9** |
| 7. Points & Reputation 🆕 | 5 | 3 | 0 | **8** |
| 8. Account & Proxy Management 🆕 | 4 | 3 | 0 | **7** |
| 9. CRM & Pipeline | 3 | 2 | 0 | **5** |
| 10. Campaign Execution | 5 | 2 | 0 | **7** |
| 11. Payments & Commission | 3 | 0 | 0 | **3** |
| 12. Reporting & Analytics | 2 | 2 | 0 | **4** |
| **TOTAL** | **51** | **27** | **3** | **81** |

> **Phase 1 Launch = 51 P0 stories** (minimum viable launch)
> **Full Phase 1 = 78 stories** (P0 + P1)
> **With nice-to-haves = 81 stories** (all priorities)

---

## Sprint Planning Suggestion

| Sprint | Duration | Focus | Key Stories |
|--------|----------|-------|-------------|
| Sprint 1 | Week 1-2 | Data Import + Enrichment + Account Setup | US-1.1→1.5, US-8.1→8.4 |
| Sprint 2 | Week 3-4 | IG Outreach System (instagrapi) | US-2.1→2.6 |
| Sprint 3 | Week 5-6 | Job Board + Points System | US-6.1→6.6, US-7.1→7.5 |
| Sprint 4 | Week 7-8 | Onboarding + Brand Import + WhatsApp Templates | US-3.1→3.4, US-4.1→4.4, US-5.1→5.4 |
| Sprint 5 | Week 9-10 | Campaign Execution + CRM + Payments + Reporting | US-9.1→9.3, US-10.1→10.5, US-11.1→11.3, US-12.1→12.2 |
