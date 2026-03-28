# ⚙️ Operations & Infrastructure SOPs (300-399)

---

# SOP-301: Daily Operations Routine

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-301 |
| **Executor** | Ops Manager |
| **Frequency** | Daily (10 AM IST) |
| **Estimated Time** | 30-45 min |

## Steps

### Morning Check (10:00 AM)
1. **Account Health:** Check IG Account Manager tab — all accounts green? Any action blocks?
   - If blocked → SOP-303 immediately
2. **WhatsApp Status:** Check Evolution API — all numbers active? Any bans?
   - If banned → SOP-306 immediately
3. **Chatwoot Inbox:** Check unread messages — any hot leads waiting > 1 hour?
   - Assign immediately to sales
4. **CRM Status:** Check today's automated actions:
   - How many Day 1/2/3 DMs are queued?
   - How many WA messages are queued?
   - Any onboarding chats in progress?

### Midday Review (2:00 PM)
5. **Reply Check:** Review new IG DM and WA replies classified by AI
6. **Override misclassifications:** If AI classified wrong, manually re-route
7. **Check campaign progress:** Any visits today? QC submissions pending?

### End of Day (6:00 PM)
8. **Log daily numbers** in Dashboard tab:
   - DMs sent, replies received, positive rate
   - WA sent, replies, hot leads
   - Jobs posted, applications received
   - Revenue collected today
9. **Set tomorrow's priorities**

## Quality Checklist
- [ ] All accounts healthy (green status)
- [ ] Chatwoot inbox cleared (no unanswered leads > 2 hours)
- [ ] Today's batch ran successfully
- [ ] Dashboard tab updated with daily numbers
- [ ] Any fires flagged for tomorrow

---

# SOP-302: Weekly Business Review

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-302 |
| **Executor** | Ops Manager |
| **Frequency** | Weekly (Sunday 7 PM) |
| **Estimated Time** | 60 min |

## Steps

1. **Pipeline Review:**
   - New influencers imported this week?
   - Reply rate trend (up/down)?
   - Onboarding completion rate?
   - Brand response rate?
2. **Revenue Review:**
   - Campaigns completed? Revenue collected?
   - Outstanding invoices?
   - Commission earned?
3. **Agent Performance:**
   - Classification accuracy (spot-check 10 random classifications)
   - Any false positives/negatives in outreach?
4. **Infrastructure Health:**
   - Proxy uptime?
   - API call costs?
   - Any IG/WA incidents?
5. **Next Week Plan:**
   - Import new batches?
   - New job postings?
   - Template updates?
6. **Generate weekly report** and share in Ops WhatsApp group

---

# SOP-303: IG Account Health Check

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-303 |
| **Executor** | AI Agent + Ops |
| **Frequency** | Daily (automated) + manual spot-check |
| **Estimated Time** | 5 min (manual review) |

## Steps

1. **Automated check:** Script runs daily at 8 AM IST
2. For each IG account:
   - Attempt login via `load_settings()` → If success → `Healthy`
   - If challenge required → `Challenge Pending` → auto-resolve if possible
   - If login fails → `Session Expired` → re-login
   - If action block → `Blocked` → stop all activity
3. **Update Account Manager tab** with status
4. **Alert Ops** if any account is not green

## Decision Points
| Condition | Action |
|-----------|--------|
| Session expired | Re-login, dump new settings |
| SMS challenge | Resolve via challenge handler, or manual OTP |
| Action block (temp) | Cool down 24-48 hours, reduce DM volume |
| Action block (permanent) | Retire account, provision new (SOP-309) |
| Account disabled by IG | Abandon. Do NOT try to recover. New account needed. |

---

# SOP-304: IG Session Management (instagrapi)

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-304 |
| **Executor** | AI Agent |
| **Frequency** | Per session refresh |

## Steps

1. **Load session:** `client.load_settings("session_{account}.json")`
2. **Attempt login:** `client.login(username, password)`
3. **If successful:** `client.dump_settings("session_{account}.json")`
4. **If challenge:** Attempt `challenge_resolver_auto()` → if fails, escalate to Ops
5. **Rate limiting:** Each session gets max 50 API calls/hour
6. **Proxy binding:** Each account MUST use its assigned proxy (never switch)

---

# SOP-305: Proxy Management & Rotation

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-305 |
| **Executor** | Ops Team |
| **Frequency** | Weekly check + as needed |

## Steps

1. **Check proxy health:** Ping each proxy IP, verify it's residential (not datacenter)
2. **Monitor IP reputation:** Check if IP is flagged on any blacklists
3. **Rotation rules:**
   - DO NOT rotate frequently — IG ties sessions to IPs
   - Only rotate if: IP gets flagged, proxy provider changes IP, or account gets blocked
4. **Assignment rule:** 1 proxy : 2 outreach accounts (NEVER exceed)
5. **If proxy goes down:** Both assigned accounts must stop immediately. Assign backup proxy.
6. **Update Account Manager tab** with proxy assignments

---

# SOP-306: WhatsApp Number Ban Recovery

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-306 |
| **Executor** | Ops Team |
| **Frequency** | Per ban incident |
| **Estimated Time** | 15-30 min |

## Steps

1. **Detect ban:** Evolution API reports delivery failure / number disconnected
2. **Pause ALL outreach** from the banned number immediately
3. **Check ban type:**
   - Temporary (24-72 hours): Wait it out, reduce volume after recovery
   - Permanent: Number is lost
4. **Shift to next available number:**
   - Check backup numbers in Evolution API
   - If available → configure in settings → resume outreach at 50% volume
   - If none available → **pause brand outreach entirely** until new number is ready
5. **New number setup:**
   - Register new WhatsApp Business number
   - Connect to Evolution API
   - Warm up for 5-7 days (send only to known contacts)
   - Then resume outreach at low volume (5/day → ramp to 10-15/day)
6. **Update CRM:** Note which brands were in-progress, reassign to new number
7. **Post-mortem:** Why was the number banned? Too many messages? Reported by recipients?

---

# SOP-307: Category & Data Point Management

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-307 |
| **Executor** | Ops Team |
| **Frequency** | As needed (when adding new categories) |
| **Related SOPs** | See `docs/data_schema.md` for full schema |

## Steps

### Adding a New Category/Subcategory
1. Open `data_schema.md` → find relevant section (influencer or brand)
2. Add new entry to the JSON config
3. Update Google Sheets **Config** tab with the new value
4. Test: create a test record with the new category, verify it appears in filters
5. Update relevant templates if the new category affects outreach messaging

### Adding a New Data Point (Column)
1. Add column header to the relevant Google Sheets tab
2. Document in `data_schema.md` → appropriate table
3. If collected during onboarding → update onboarding agent prompt
4. If auto-enriched → update enrichment agent logic
5. Test: verify data flows correctly

### Removing a Data Point
1. Hide (don't delete) the column in Google Sheets
2. Mark as `deprecated` in `data_schema.md`
3. Agents will ignore empty/hidden columns automatically

---

# SOP-308: Google Sheets CRM Maintenance

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-308 |
| **Executor** | Ops Team |
| **Frequency** | Weekly |

## Steps

1. **Backup:** Download full Sheet as .xlsx every Monday
2. **Cleanup:** Archive old Cold/Opted Out rows to separate "Archive" tab
3. **Formulas:** Verify Dashboard auto-formulas haven't broken
4. **Conditional formatting:** Ensure pipeline status badges show correct colors
5. **Permissions:** Verify only authorized team members have edit access
6. **Performance:** If Sheet gets slow (>5000 rows), split into quarterly tabs

---

# SOP-309: New IG Account Provisioning

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-309 |
| **Executor** | Ops Team |
| **Frequency** | As needed |
| **Estimated Time** | 30 min + 3 weeks warmup |

## Steps

1. **Create new IG account** with a VibMedia persona (e.g., "Selena — VibMedia Recruiter")
2. **Complete profile:** Bio, profile pic, 9 initial posts (food/lifestyle content)
3. **Assign proxy:** Pick from available proxy slots (must have < 2 accounts per IP)
4. **Configure instagrapi:** Login, dump settings, verify connection
5. **Add to Account Manager tab** with status `Warming Up`
6. **Start warmup protocol:** SOP-003 (3 weeks)
7. **After warmup complete:** Set status `Active`, add to outreach rotation

---

# SOP-310: New Team Member Onboarding

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-310 |
| **Executor** | Ops Manager |
| **Frequency** | Per new hire |
| **Estimated Time** | 2-3 days |

## Steps

### Day 1: Context
1. Share README.md — full system overview
2. Share guardrails.md — critical rules
3. Share SOP Index — all procedures
4. Assign domain-specific SOPs based on role (Ops / Sales / Content)
5. Grant Google Sheets access (view-only initially)

### Day 2: Shadow
6. Shadow existing team member for a full day
7. Observe: daily routine, Chatwoot workflow, CRM navigation
8. Practice: classify 10 random replies manually, compare with AI classification

### Day 3: Supervised Execution
9. Execute 3-5 tasks under supervision (e.g., import a batch, review an application)
10. If clean → grant edit access to CRM
11. Set 30-day check-in for performance review
