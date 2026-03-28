# 🎬 Job Board & Campaign SOPs (200-299)

---

# SOP-201: Job Creation & Posting

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-201 |
| **Executor** | Ops Team / Brand |
| **Frequency** | Per campaign |
| **Estimated Time** | 10 min |
| **Prerequisites** | Brand onboarded (SOP-106 complete) |
| **Related SOPs** | SOP-202 (Matching), SOP-203 (Review) |

## Steps

1. **Define job details:**
   - Brand name + location
   - Job type: Quick Review / Standard Collab / Full Campaign / Long-term
   - Deal type: Barter / Paid / Paid+Barter
   - Budget: total amount (₹)
   - Points to award: 10 / 25 / 50 / 100
   - Deliverables: specific content requirements (X reels, Y stories)
   - Niche requirement: (e.g., Food)
   - Minimum tier: (e.g., Micro+)
   - Minimum engagement rate: (e.g., 2%+)
   - Area preference: (e.g., South Delhi)
   - Expiry date: (default 14 days)
2. **Add to Job Board tab:** Create new row with all fields
3. **Set `job_status`** = `Open`
4. **Trigger SOP-202** — AI distributes to matching influencers

## Quality Checklist
- [ ] Budget approved by brand
- [ ] Deliverables clearly stated (no ambiguity)
- [ ] Niche + tier + area specified
- [ ] Points value matches job type
- [ ] Expiry date set

---

# SOP-202: Job Matching & DM Distribution

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-202 |
| **Executor** | AI Agent |
| **Frequency** | Per new job posting |
| **Estimated Time** | Automated (2-5 min per batch) |

## Steps

1. **Query matching influencers:** Filter CRM for:
   - `pipeline_status` = `Available`
   - `niche` matches job niche
   - `tier` ≥ job minimum tier
   - `area` matches or within radius
   - `engagement_rate` ≥ job minimum
   - `ban_status` ≠ `Banned`
2. **Rank by quality_score** (highest first)
3. **Select top 5-10** matching influencers
4. **Send IG DM notification:**
   ```
   Hey {name}! 🎯 New job match for you:
   📍 {brand_name} — {area}
   🍽️ {job_type} | {deal_type} | ₹{budget}
   ⭐ {points} points
   📋 {deliverables}
   Reply "interested" to apply!
   ```
5. **Track sends:** Log which influencers were notified
6. Set `job_status` = `Applications Open`

## Decision Points
| Condition | Action |
|-----------|--------|
| Fewer than 3 matches | Broaden filters (drop area, lower tier) |
| Influencer replies "interested" | Create application → SOP-203 |
| No applications in 3 days | Re-notify, expand criteria |
| Job expired | Close job, notify brand |

---

# SOP-203: Application Review & Acceptance

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-203 |
| **Executor** | Ops Team |
| **Frequency** | Per application |
| **Estimated Time** | 5 min per application |

## Steps

1. **Review application:** Check influencer's profile, past performance, rating, tier
2. **Check conflicts:** Is influencer already on another campaign this week?
3. **Accept or Reject:**
   - **Accept:** DM influencer "You've been selected for {brand_name}! Details coming soon."
   - **Reject:** DM influencer "Thanks for applying! This one wasn't a fit, but we'll have more jobs soon."
4. **Update Campaign Tracker:** Create new row linking brand + influencer + job
5. **Proceed to SOP-204** (Visit Scheduling)

---

# SOP-204: Visit Scheduling & Coordination

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-204 |
| **Executor** | Ops Team |
| **Frequency** | Per accepted application |
| **Estimated Time** | 10-15 min |

## Steps

1. **Propose 2-3 dates** to both brand and influencer via DM/WA
2. **Confirm mutually available date**
3. **Send details to influencer:**
   - Restaurant name, address, Google Maps link
   - Visit date + time
   - Who to ask for (contact person)
   - Brief: what to feature, hashtags, mentions, posting timeline
4. **Send details to brand:**
   - Influencer name, handle, follower count
   - Visit date + time
   - What the influencer will create
5. **Update Campaign Tracker:** `campaign_status` = `Scheduled`, `visit_date` = confirmed date
6. **Set reminder:** 24 hours before visit, send reminder to both parties

## Decision Points
| Condition | Action |
|-----------|--------|
| Brand reschedules | Update all parties, new dates |
| Influencer reschedules (24h+ notice) | Update, no penalty |
| Influencer no-shows | → SOP-502 (No-Show Handling) |

---

# SOP-205: Content QC Review

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-205 |
| **Executor** | AI Agent + Ops Team |
| **Frequency** | Per content submission |
| **Estimated Time** | AI: 30 sec; Human: 3-5 min |
| **Related SOPs** | SOP-501 (Content Quality Standards) |

## Steps

### AI First Pass
1. Influencer submits draft content (video link / file)
2. AI checks against brief requirements:
   - ✅ Correct hashtags used?
   - ✅ Brand @mentioned?
   - ✅ Required deliverables met? (reel vs story count)
   - ✅ Video duration meets minimum?
   - ✅ Food/restaurant clearly visible?
   - ✅ No competitor branding visible?
3. AI generates QC report with pass/fail per criterion

### Human Review (Flagged Items)
4. If AI flags any issues → Ops reviews manually
5. Ops makes final call: **Approve** or **Request Revision** (SOP-206)
6. If approved → influencer posts → `campaign_status` = `Posted`

---

# SOP-206: Content Revision Request

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-206 |
| **Executor** | Ops Team |
| **Frequency** | Per QC failure |

## Steps

1. **DM influencer** with specific, actionable feedback:
   - "Great reel! Two things to fix: 1) Add @brand_handle in caption 2) Include #DelhiFood hashtag"
2. **Set deadline:** 48 hours for revision
3. **Track in Campaign Tracker:** `campaign_status` = `Revision Requested`
4. When resubmitted → re-run SOP-205
5. Maximum 2 revision cycles — if still failing, Ops and influencer discuss directly

---

# SOP-207: Campaign Completion & Points Award

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-207 |
| **Executor** | Ops + AI Agent |
| **Frequency** | Per completed campaign |
| **Estimated Time** | 5 min |

## Steps

1. **Verify content posted:** Check influencer's IG profile for the live post
2. **Capture metrics:** Screenshot or log initial metrics (views, likes, comments)
3. **Award points:** Add job-type points to influencer's total
   - Quick Review: 10 pts, Standard: 25 pts, Full: 50 pts, Long-term: 100 pts
4. **Check tier upgrade:**
   - If total ≥ 50 → Silver
   - If total ≥ 200 → Gold
   - If total ≥ 500 → Platinum
   - If upgraded → DM notification: "🎉 You've reached {tier}! Unlocked {benefits}"
5. **Update `points_last_earned`** = today (resets 12-month decay timer)
6. **Set `campaign_status`** = `Completed`
7. **Trigger:** SOP-208 (Ratings), SOP-209 (Report), SOP-401 (Invoice)

---

# SOP-208: Mutual Rating Collection

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-208 |
| **Executor** | AI Agent |
| **Frequency** | 24 hours after campaign completion |

## Steps

1. **DM influencer:** "How was your experience with {brand_name}? Rate 1-5 ⭐ and optional comment"
2. **Message brand:** "How was {influencer_handle}'s content? Rate 1-5 ⭐ and optional comment"
3. Store ratings in CRM
4. Update influencer's `avg_rating`
5. Flag ratings below 3 for Ops review

---

# SOP-209: Campaign Report Generation

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-209 |
| **Executor** | AI Agent |
| **Frequency** | 7 days after content posted |

## Steps

1. Collect post metrics: views, likes, comments, shares, saves
2. Calculate: total reach, engagement rate, estimated ad value
3. Generate PDF report (see `docs/designs/07_campaign_report.png`):
   - Campaign summary
   - Content performance metrics
   - Financial summary (brand paid → commission → influencer payout)
4. Send to brand via WhatsApp / email
5. Store copy in campaign records
