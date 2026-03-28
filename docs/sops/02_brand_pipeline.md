# 🏢 Brand Pipeline SOPs (100-199)

---

# SOP-101: Brand Data Import & Validation

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-101 |
| **Executor** | Ops Team |
| **Frequency** | Per batch import |
| **Estimated Time** | 15 min per batch |
| **Prerequisites** | CSV with restaurant_name + phone; Google Sheets access |
| **Related SOPs** | SOP-102 (WA First Touch), SOP-307 (Category Mgmt) |

## Steps

1. **Validate CSV:** Ensure `restaurant_name`, `phone`, `locality` present
2. **Normalize phone:** Convert to +91XXXXXXXXXX format
3. **Normalize cuisine:** Map to standard categories from `categories.json`
4. **Dedup:** Check against Brand Pipeline tab — skip existing entries
5. **Import:** Paste into Brand Pipeline, set `pipeline_status` = `Raw Lead`
6. **Score leads:** Mark `lead_score` based on: Zomato rating, follower count, area premium
7. **Trigger:** Queue for SOP-102 (WhatsApp AI First Touch)

## Decision Points
| Condition | Action |
|-----------|--------|
| Phone number invalid | Skip import, flag as `Invalid Phone` |
| Already in CRM as Active client | Skip — already onboarded |
| Already in CRM as Dormant | Move to re-engagement (SOP-107) |
| Blacklisted | Do NOT re-import |

---

# SOP-102: WhatsApp AI First Touch

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-102 |
| **Executor** | AI WhatsApp Agent |
| **Frequency** | Daily batch (max 10-15 messages/day per number) |
| **Estimated Time** | Automated (~10 sec per message) |
| **Prerequisites** | Brand data enriched; Evolution API active; WA number warmed |
| **Related SOPs** | SOP-103 (Reply Classification), SOP-306 (Ban Recovery) |

## Steps

### Day 1 — Personalized Hook
1. Select brands with `pipeline_status` = `Raw Lead`
2. Generate personalized message using Gemini Flash:
   - Template: "Hi {restaurant_name}! 👋 We noticed your {popular_dish} looks amazing 🔥..."
   - Variables: `{restaurant_name}`, `{popular_dish}`, `{area}`, `{cuisine}`
3. Send via Evolution API
4. Update: `pipeline_status` = `Contacted`, `wa_day` = 1, `contacted_date` = today

### Day 2 — Social Proof (If No Reply)
1. Query brands with `wa_day` = 1, `contacted_date` = yesterday, no reply
2. Send: "{nearby_restaurant} just got 45K views from a creator campaign with us..."
3. Update: `wa_day` = 2

### Day 3 — Free Deal Offer (If No Reply)
1. Query brands with `wa_day` = 2, sent yesterday, no reply
2. Send: "We'd love to offer {restaurant_name} a FREE influencer visit..."
3. Update: `wa_day` = 3

### After Day 3 (No Reply)
1. Set `pipeline_status` = `No Response`
2. Set `re_engage_date` = today + 30 days
3. Eligible for SOP-107 after 30 days

## Rate Limits
- Max 10-15 messages per WhatsApp number per day
- Minimum 2-minute gap between messages
- No messages before 9 AM or after 8 PM IST

---

# SOP-103: WhatsApp Reply Classification

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-103 |
| **Executor** | AI Classifier Agent |
| **Frequency** | Real-time (per reply) |

## Steps

1. Receive reply via Evolution API webhook
2. Classify using Gemini Flash into:
   - `positive` → "interested", "tell me more" → Route to Chatwoot (SOP-104)
   - `neutral/question` → "how does it work?", "pricing?" → AI answers FAQ, then routes to Chatwoot
   - `negative` → "not interested", "stop" → Mark `Cold`, log reason
   - `price_objection` → "too expensive" → Route to Chatwoot for human negotiation
   - `already_doing` → "we already use influencers" → Route to Chatwoot (competitive intel)
   - `opt_out` → "stop messaging" → Immediately stop, mark `Opted Out`
3. Update CRM with classification + confidence
4. Log full conversation in Chatwoot regardless of class

---

# SOP-104: Human Handover via Chatwoot

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-104 |
| **Executor** | Sales Team |
| **Frequency** | Per hot lead |
| **Estimated Time** | 5-30 min per conversation |

## Steps

1. **Notification:** Sales receives Chatwoot notification — "Hot lead: {restaurant_name}"
2. **Context Review:** Read AI conversation history in Chatwoot (what was sent, what was replied)
3. **Respond within 30 min:** First human response must happen fast
4. **Qualify:** Assess fit — budget, expectations, timeline
5. **If qualified:** Move to SOP-105 (Free Trial)
6. **If not fit:** Politely decline, mark `Not Qualified`, log reason
7. **Update CRM:** Set `pipeline_status` = `Interested`, `assigned_sales` = self

## Escalation
- If sales can't respond within 1 hour → auto-assign to backup
- If brand asks technical question → loop in Ops

---

# SOP-105: Brand Qualification & Free Trial

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-105 |
| **Executor** | Sales Team |
| **Frequency** | Per interested brand |
| **Estimated Time** | 15-30 min + coordination |

## Steps

1. **Pitch free trial:** Explain the model — "We send 1 food creator to visit, shoot content, post. Zero cost to you."
2. **Collect requirements:** What dishes to feature? Visit date preference? Any restrictions?
3. **Match influencer:** Use CRM to find 2-3 suitable influencers (niche, area, tier match)
4. **Present shortlist:** Share 2-3 influencer profiles with the brand
5. **Brand selects:** Brand picks preferred influencer(s)
6. **Schedule:** Coordinate visit date between brand and influencer (SOP-204)
7. **Update CRM:** `pipeline_status` = `Free Trial`

---

# SOP-106: Brand Onboarding

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-106 |
| **Executor** | Sales + Ops |
| **Frequency** | After successful free trial |

## Steps

1. **Post-trial review:** Share campaign results with brand (reach, engagement, views)
2. **Upsell conversation:** Present pricing tiers (Starter / Growth / Premium)
3. **Collect brand details:** billing name, GST (if applicable), preferred payment method
4. **Add to active clients:** Set `pipeline_status` = `Onboarded`
5. **First paid campaign:** Create job posting (SOP-201) with agreed budget
6. **Set expectations:** Content timelines, QC process, reporting schedule

---

# SOP-107: Brand Re-engagement (Dormant/No Response)

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-107 |
| **Executor** | AI Agent + Sales |
| **Frequency** | Monthly batch (30-day cycle) |

## Steps

1. Query: brands with `No Response` and `re_engage_date` ≤ today
2. Check if restaurant is still operational (quick Google check)
3. Send new personalized WA message with updated hook (different angle from first attempt)
4. If reply → route per SOP-103
5. If no reply after 2nd cycle → mark `Permanently Cold`
6. Never contact a `Blacklisted` or `Opted Out` brand
