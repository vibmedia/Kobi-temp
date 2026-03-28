# 🏷️ Influencer Pipeline SOPs (001-099)

---

# SOP-001: Influencer Data Import & Validation

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-001 |
| **Version** | 1.0 |
| **Domain** | Influencer Pipeline |
| **Owner** | Ops Manager |
| **Executor** | Ops Team |
| **Frequency** | As needed (per batch import) |
| **Estimated Time** | 15-30 min per batch |
| **Prerequisites** | CSV file with IG handles ready; Google Sheets CRM access |
| **Related SOPs** | SOP-002 (Enrichment), SOP-307 (Category Management) |

## Purpose
Ensure every new influencer imported into the CRM has valid, deduplicated data with all required fields populated.

## Steps

### Step 1: Prepare CSV File
**Who:** Ops Team
**Tool:** Spreadsheet editor
1. Verify CSV has at minimum: `instagram_handle`, `city`
2. Remove any rows without an IG handle
3. Ensure handles are in `@handle` format (lowercase, no spaces)
4. Remove duplicate handles

**✅ Done when:** CSV has only valid, unique IG handles with city field populated.

### Step 2: Check for Duplicates Against Existing CRM
**Who:** Ops Team
**Tool:** Google Sheets
1. Open the **Influencer Pipeline** tab in CRM
2. Use `VLOOKUP` or `CTRL+F` to check if any imported handles already exist
3. Remove handles already in the CRM from the import batch
4. Flag any handles that exist but have `Cold` status — consider re-import as `Re-engage`

**✅ Done when:** Zero duplicates remain in import batch.

### Step 3: Import to CRM
**Who:** Ops Team
**Tool:** Google Sheets
1. Paste validated rows into the **Influencer Pipeline** tab
2. Set `pipeline_status` = `Imported` for all new rows
3. Set `imported_date` = today's date
4. Leave enrichment fields blank (SOP-002 will fill them)

**✅ Done when:** All new rows visible in CRM with status `Imported`.

### Step 4: Trigger Enrichment
**Who:** Ops Team
**Tool:** Backend API / Script
1. Run enrichment agent on the batch (SOP-002)
2. Monitor for errors (private accounts, deleted accounts)

**✅ Done when:** Enrichment batch started.

## Decision Points
| Condition | Action |
|-----------|--------|
| Handle is private account | Mark as `Private — Skip` |
| Handle doesn't exist | Mark as `Invalid — Remove` |
| Handle already in CRM as Active | Skip entirely |
| Handle in CRM as Cold | Flag for re-engagement (SOP-008) |

## Quality Checklist
- [ ] All handles in @lowercase format
- [ ] Zero duplicates against existing CRM
- [ ] City field populated for every row
- [ ] Pipeline status set to `Imported`
- [ ] Enrichment triggered

---

# SOP-002: Profile Enrichment

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-002 |
| **Version** | 1.0 |
| **Domain** | Influencer Pipeline |
| **Owner** | Ops Manager |
| **Executor** | AI Enrichment Agent |
| **Frequency** | Per batch import + monthly refresh |
| **Estimated Time** | 2-5 sec per profile (rate-limited) |
| **Prerequisites** | Imported influencer with valid IG handle; IG account session active |
| **Related SOPs** | SOP-001 (Import), SOP-303 (Account Health), SOP-304 (Session Mgmt) |

## Purpose
Automatically enrich influencer profiles with follower counts, engagement rates, niche classification, and quality scores from public Instagram data.

## Steps

### Step 1: Fetch Profile Data
**Who:** AI Enrichment Agent
**Tool:** instagrapi `user_info_by_username()`
1. For each handle in the batch, call `user_info_by_username()`
2. Extract: `follower_count`, `following_count`, `post_count`, `bio`, `is_verified`, `is_private`, `profile_pic_url`
3. Rate limit: 1 request per 3 seconds, rotate accounts

**✅ Done when:** Raw profile data fetched for all handles.

### Step 2: Calculate Engagement Rate
**Who:** AI Agent
**Tool:** Python calculation
1. Fetch last 12 posts using `user_medias(user_id, amount=12)`
2. Calculate: `engagement_rate = (avg_likes + avg_comments) / follower_count * 100`
3. Store: `avg_likes`, `avg_comments`, `engagement_rate`

**✅ Done when:** Engagement rate calculated for all non-private profiles.

### Step 3: Classify Niche
**Who:** AI Agent
**Tool:** Gemini Flash
1. Send bio + last 5 post captions to Gemini Flash
2. Prompt: "Classify this Instagram profile into one or more content niches: [categories from config]"
3. Store primary and secondary niche tags

**✅ Done when:** Niche tags assigned.

### Step 4: Calculate Quality Score & Assign Tier
**Who:** AI Agent
**Tool:** Scoring algorithm
1. Quality Score (0-100) = weighted sum of:
   - Engagement rate (40% weight)
   - Follower count (20%)
   - Post consistency (15%)
   - Bio completeness (10%)
   - Account age (10%)
   - Verified status (5%)
2. Assign tier: Nano (1K-10K), Micro (10K-50K), Mid (50K-200K), Macro (200K+)

**✅ Done when:** Quality score and tier assigned.

### Step 5: Write to CRM
**Who:** AI Agent
**Tool:** Google Sheets API
1. Update all enrichment fields in the Influencer Pipeline tab
2. Set `enriched_date` = today
3. Flag any profiles that failed enrichment with error codes

**✅ Done when:** CRM updated with enriched data.

## Decision Points
| Condition | Action |
|-----------|--------|
| Profile is private | Skip. Mark `Private — Not Enriched` |
| Profile deleted/not found | Mark `Invalid — Remove` |
| Engagement < 1% | Flag as `Low Quality` — still import but deprioritize |
| Account is bot/fake (following >> followers) | Flag as `Suspicious` |
| instagrapi rate limit hit | Pause 15 min, switch account, retry |

## Failure Recovery
- **If instagrapi session expires:** Run SOP-304 (Session Management) to refresh
- **If IG account gets action-blocked:** Switch to backup account, run SOP-303
- **If Gemini API fails:** Use fallback niche from bio keywords

---

# SOP-003: IG Account Warmup

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-003 |
| **Version** | 1.0 |
| **Domain** | Influencer Pipeline |
| **Owner** | Ops Manager |
| **Executor** | Ops Team + AI Scheduler |
| **Frequency** | Per new IG account (2+ weeks) |
| **Estimated Time** | 15 min/day for 3 weeks |
| **Prerequisites** | New IG account created; residential proxy assigned |
| **Related SOPs** | SOP-305 (Proxy Mgmt), SOP-309 (New Account Provisioning) |

## Purpose
Warm up new Instagram outreach accounts to avoid action blocks when starting DM outreach.

## Steps

### Week 1: Passive Activity (Days 1-7)
**Who:** Ops Team (manual or scheduled script)
1. Follow 10-15 food accounts per day (organic-looking pace)
2. Like 20-30 posts per day in the food niche
3. Browse explore page for 5-10 min
4. Watch 5-10 stories per day
5. **DO NOT send any DMs or comments**

**✅ Done when:** 7 days of passive activity logged.

### Week 2: Light Engagement (Days 8-14)
**Who:** Ops Team
1. Comment on 5-10 food posts per day (genuine comments, not generic)
2. Continue following 5-10 accounts per day
3. Continue liking 20-30 posts per day
4. Reply to 1-2 stories per day (casual, non-promotional)
5. **DO NOT send any cold DMs yet**

**✅ Done when:** 14 days completed, no action blocks received.

### Week 3: Gradual DMs (Days 15-21)
**Who:** AI Outreach Agent (supervised)
1. Day 15-16: Send 3 DMs per day (to test accounts first)
2. Day 17-18: Send 5 DMs per day
3. Day 19-21: Send 6 DMs per day
4. Monitor for any action blocks or warnings
5. If clean at Day 21 → account is **Warm** and ready for SOP-004

**✅ Done when:** Account has sent 30+ DMs without any action block.

## Decision Points
| Condition | Action |
|-----------|--------|
| Action block during Week 1 | Stop ALL activity for 48 hours, then resume slower |
| Action block during Week 2 | Stop comments for 72 hours, continue likes only |
| Action block during Week 3 | Stop DMs for 5 days, restart at 2/day |
| Account suspended | Abandon account, provision new one (SOP-309) |

---

# SOP-004: IG DM Outreach (3-Day Drip Sequence)

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-004 |
| **Version** | 1.0 |
| **Domain** | Influencer Pipeline |
| **Owner** | Ops Manager |
| **Executor** | AI Outreach Agent |
| **Frequency** | Daily (batches of 5-8 DMs per account) |
| **Estimated Time** | Automated — 30-60 sec per DM |
| **Prerequisites** | Warmed account (SOP-003 complete); enriched influencer batch (SOP-002 complete) |
| **Related SOPs** | SOP-003 (Warmup), SOP-005 (Reply Classification) |

## Steps

### Step 1: Select Today's Batch
**Who:** AI Outreach Agent
1. Query CRM for influencers with status `Imported` or `Enriched`
2. Filter: not private, quality_score ≥ 30, city matches target area
3. Select batch (max 5-8 per account based on ramp schedule)
4. Assign outreach account (round-robin across warm accounts)

### Step 2: Send Day 1 DM — Intro
**Who:** AI Outreach Agent
**Tool:** instagrapi `direct_send()`
**Template:** (from `templates/outreach_templates.md`)
```
Hey {name}! 👋
I'm from VibMedia — we connect Delhi food creators with restaurants for paid collabs.
Loved your recent post about {recent_post_topic}!
Would you be interested in getting regular brand deals through us?
```
1. Personalize `{name}` from enriched data, `{recent_post_topic}` from last post caption
2. Send via instagrapi through assigned proxy
3. Update CRM: `pipeline_status` = `DM Sent`, `dm_sent_date` = today, `dm_day` = 1
4. Wait 30-90 seconds between DMs (randomized)

### Step 3: Day 3 — Follow-up (If No Reply)
**Who:** AI Outreach Agent (scheduled by Celery)
1. Check CRM for influencers with `dm_day = 1` and `dm_sent_date` = 2 days ago and no reply
2. Send Follow-up template
3. Update: `dm_day` = 2

### Step 4: Day 5 — Final Message (If No Reply)
**Who:** AI Outreach Agent
1. Check for `dm_day = 2` and sent 2 days ago, no reply
2. Send final value-prop template
3. Update: `dm_day` = 3

### Step 5: Close Out After Day 5
1. If no reply after Day 5 → set `pipeline_status` = `Cold`
2. Set `cold_date` = today
3. Eligible for SOP-008 (Re-engagement) in 30 days

## Decision Points
| Condition | Action |
|-----------|--------|
| Reply received at any point | Stop sequence → SOP-005 |
| Action block on outreach account | Pause batch, rotate account, resume |
| Message send fails | Retry once after 5 min. If fails again, skip and flag |

## Quality Checklist
- [ ] DM is truly personalized (not just template)
- [ ] DMs sent within rate limits (5-8/day per account)
- [ ] CRM updated after every send
- [ ] No generic greetings — reference their content

---

# SOP-005: Reply Classification & Routing

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-005 |
| **Version** | 1.0 |
| **Domain** | Influencer Pipeline |
| **Owner** | Ops Manager |
| **Executor** | AI Classifier Agent |
| **Frequency** | Real-time (per incoming DM reply) |
| **Estimated Time** | 1-2 seconds per classification |
| **Prerequisites** | Gemini Flash API configured; classification prompt with few-shot examples |
| **Related SOPs** | SOP-004 (Outreach), SOP-006 (Onboarding) |

## Steps

### Step 1: Receive Reply Notification
1. instagrapi polls for new DM replies every 60 seconds
2. Extract: sender handle, message text, conversation context

### Step 2: Classify Intent
**Tool:** Gemini Flash with few-shot prompt
1. Send message + last 2 messages of context to classifier
2. Classify into: `positive`, `negative`, `question`, `spam`, `onboarding_data`, `opt_out`
3. Include confidence score (0-1)

### Step 3: Route Based on Classification
| Class | Confidence | Action |
|-------|-----------|--------|
| `positive` | ≥ 0.8 | Start onboarding → SOP-006 |
| `positive` | < 0.8 | Escalate to Ops for manual review |
| `negative` | ≥ 0.7 | Mark `Cold`, log reason, stop sequence |
| `question` | any | AI agent answers, continue dialogue |
| `spam` | ≥ 0.9 | Ignore, mark `Spam` |
| `onboarding_data` | any | Extract data, pass to SOP-006 |
| `opt_out` | any | Stop all messages, mark `Opted Out` → SOP-009 |

### Step 4: Update CRM
1. Set `pipeline_status` = `Replied`
2. Log classification: `reply_class`, `reply_confidence`, `reply_text`
3. If routed to onboarding → update status to `Onboarding Chat`

---

# SOP-006: Influencer Onboarding Chat

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-006 |
| **Version** | 1.0 |
| **Domain** | Influencer Pipeline |
| **Owner** | Ops Manager |
| **Executor** | AI Onboarding Agent (LangGraph) |
| **Frequency** | Per positive reply |
| **Estimated Time** | 3-5 min conversation (over 1-2 hours elapsed) |
| **Prerequisites** | Positive classification (SOP-005); Gemini Pro configured |
| **Related SOPs** | SOP-005 (Classification), SOP-007 (Profile Review) |

## Steps

### Step 1: Welcome & Start Data Collection
**Who:** AI Onboarding Agent
**Tool:** instagrapi DM + LangGraph state machine
1. Send welcome message: "Awesome! Welcome to VibMedia's creator network! 🚀"
2. Collect data in this order:
   - Area (e.g., "South Delhi, Hauz Khas")
   - Rate per reel (₹ amount)
   - Rate per story (₹ amount)
   - Deal preference (Barter / Paid / Both)
   - Phone number (WhatsApp)

### Step 2: Validate Each Response
1. Area → check against known Delhi NCR localities
2. Rate → must be a number, reasonable range (₹500 - ₹5,00,000)
3. Phone → must be valid Indian mobile (+91 / 10 digits)
4. If invalid → politely ask again with hint

### Step 3: Confirm & Save
1. Summarize all collected data back to the influencer
2. "Here's what I have: Area: South Delhi, Rate: ₹5K/reel, Phone: +91 98xxx. All correct?"
3. If confirmed → save to CRM
4. If corrections → update and re-confirm

### Step 4: Complete Onboarding
1. Set `pipeline_status` = `Onboarded`
2. Set `total_points` = 0, `tier` = `Bronze`
3. Send: "You start at 🥉 Bronze tier. Complete jobs to earn points and level up!"
4. Trigger SOP-007 (Profile Review)

## Decision Points
| Condition | Action |
|-----------|--------|
| Influencer stops replying mid-onboarding | Wait 24 hours, send 1 reminder, then pause |
| Influencer gives invalid data 3 times | Offer form link as fallback |
| Influencer asks to be removed | Stop → SOP-009 |
| Conversation in Hindi | Agent responds in Hindi (detected automatically) |

---

# SOP-007: Influencer Profile Review & Approval

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-007 |
| **Version** | 1.0 |
| **Executor** | Ops Team (manual review) |
| **Frequency** | Per onboarded influencer |
| **Estimated Time** | 2-3 min per profile |

## Steps

1. Open onboarded influencer's row in CRM
2. Verify: all fields populated, no obviously fake data
3. Quick check IG profile (look for bot patterns: high following, low engagement, generic content)
4. If clean → set `pipeline_status` = `Available`
5. If suspicious → set `pipeline_status` = `Under Review`, flag for deeper check (SOP-504)

---

# SOP-008: Re-engagement of Cold/Idle Influencers

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-008 |
| **Version** | 1.0 |
| **Executor** | AI Agent + Ops |
| **Frequency** | Monthly batch |

## Steps

1. Query CRM for influencers with `Cold` status and `cold_date` ≥ 30 days ago
2. Check if their IG account is still active and public
3. Re-enrich profile (SOP-002) — follower/engagement may have changed
4. If improved (engagement up, follower growth) → send "We have new deals!" DM
5. If unchanged → skip, try again in 60 days
6. If account deleted → mark `Permanently Cold`
7. Update `pipeline_status` = `Re-engaged` or `Permanently Cold`

---

# SOP-009: Opt-out & Data Deletion Request

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-009 |
| **Version** | 1.0 |
| **Executor** | Ops Team (human-in-the-loop) |
| **Frequency** | Per request |
| **Estimated Time** | 10 min |

## Steps

1. Identify opt-out intent (reply: "stop", "remove me", "unsubscribe", "delete my data")
2. Immediately stop ALL automated messages to this handle
3. Set `pipeline_status` = `Opted Out`
4. Confirm to the influencer: "Your data has been noted for removal. We'll process within 48 hours."
5. Remove personal data (phone, email, rates, UPI) within 48 hours
6. Retain only: handle + `Opted Out` status (for suppression list)
7. Log the request in compliance log

> **Note:** Final DPDPA automated policy to be implemented after legal review. Currently human-in-the-loop.
