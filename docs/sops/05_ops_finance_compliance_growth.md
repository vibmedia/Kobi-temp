# 💰 Finance & Payment SOPs (400-499)

---

# SOP-401: Invoice Generation

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-401 |
| **Executor** | Ops Team |
| **Frequency** | Per completed campaign |
| **Estimated Time** | 5-10 min |

## Steps

1. **Trigger:** Campaign marked `Completed` (SOP-207)
2. **Generate invoice:**
   - Invoice number: VIB-{YYYY}-{sequential}
   - Brand name, GST (if registered)
   - Campaign details: job type, deliverables, dates
   - Amount: campaign budget
   - Commission breakdown (20%)
   - Payment terms: 7 days
3. **Send to brand** via WhatsApp / email
4. **Log in Campaign Tracker:** `payment_status` = `Invoiced`
5. **Set reminder:** Check payment in 7 days

---

# SOP-402: Brand Payment Collection

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-402 |
| **Executor** | Sales / Ops Team |
| **Frequency** | Per invoice |
| **Estimated Time** | 5 min (follow-up as needed) |

## Steps

1. **Monitor:** Check invoices where `payment_status` = `Invoiced` and due within 2 days
2. **Reminder (Day 5):** Send friendly payment reminder via WhatsApp
3. **Reminder (Day 7):** Send firm reminder: "Payment due today"
4. **Payment received:** Verify in bank account
5. **Update CRM:** `payment_status` = `Paid`, `paid_date` = today, `paid_amount` = X
6. **Trigger:** SOP-403 (Influencer Payout)

## Decision Points
| Condition | Action |
|-----------|--------|
| Payment overdue > 7 days | Escalate to Ops Manager |
| Payment overdue > 14 days | Pause new campaigns for this brand, direct call |
| Partial payment | Accept and log, follow up for remainder |
| Dispute about amount | Ops Manager mediates (SOP-503) |

---

# SOP-403: Influencer Payout Processing

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-403 |
| **Executor** | Ops Team |
| **Frequency** | Per paid campaign |
| **Estimated Time** | 10 min |
| **Prerequisites** | Brand payment received (SOP-402); Influencer UPI/bank details on file |

## Steps

1. **Calculate payout:**
   - `influencer_payout = paid_amount - (paid_amount × 0.20)`
   - Example: ₹10,000 paid → ₹2,000 commission → ₹8,000 payout
2. **Verify minimum threshold:** Payout ≥ ₹1,000 (if less, hold until next campaign)
3. **Process UPI transfer:**
   - Verify UPI ID from CRM
   - Initiate transfer
   - Screenshot confirmation
4. **Notify influencer** via DM: "₹{amount} has been transferred to your UPI. Thanks for the great work on {brand_name}! 🎉"
5. **Update CRM:** `payout_status` = `Paid`, `payout_date` = today
6. **For barter-only deals:** No payout needed. Just update status.

## Quality Checklist
- [ ] Commission correctly calculated at 20%
- [ ] UPI ID double-checked before transfer
- [ ] Transfer confirmation screenshot saved
- [ ] Influencer notified
- [ ] CRM updated

---

# SOP-404: Commission Tracking & Reconciliation

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-404 |
| **Executor** | Ops Manager |
| **Frequency** | Weekly (every Monday) |
| **Estimated Time** | 30 min |

## Steps

1. **Pull data:** All campaigns completed this week with `payment_status` = `Paid`
2. **Calculate totals:**
   - Total revenue collected from brands
   - Total commission (20%)
   - Total payouts to influencers
   - Net margin = commission - operational costs
3. **Verify:** Commission in CRM matches actual bank receipts
4. **Flag discrepancies:** Any mismatch → investigate immediately
5. **Update Dashboard tab** with weekly financial summary
6. **Monthly P&L:** At month end, compile into monthly financial report

---

# 🛡️ QC & Compliance SOPs (500-599)

---

# SOP-501: Content Quality Standards Checklist

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-501 |
| **Executor** | AI Agent + Ops |
| **Frequency** | Per content submission |

## AI Automated Checks

| Check | Pass Criteria | Action if Fail |
|-------|--------------|----------------|
| Brand @mention in caption | Present | Request revision |
| Required hashtags | All specified hashtags present | Request revision |
| Video duration | ≥ 15 seconds (reels), ≥ 5 sec (stories) | Request revision |
| Food/product visibility | Food clearly shown in frame | Flag for human review |
| No competitor branding | No visible competitor logos/text | Flag for human review |
| Audio quality | No background noise drowning speech | Flag for human review |
| Caption length | ≥ 50 characters | Suggest improvement |
| Posting timeline | Within agreed deadline | Flag if overdue |

## Human Review (If AI Flags)

| Check | Criteria |
|-------|----------|
| Brand positivity | Content shows restaurant in positive light |
| Brief compliance | All deliverables in the campaign brief are met |
| Authenticity | Looks genuine, not overly scripted or forced |
| ASCI compliance | Partnership disclosure present ("Paid partnership with...") |
| Overall quality | Would this impress the brand? |

---

# SOP-502: No-Show Incident Handling

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-502 |
| **Executor** | Ops Team |
| **Frequency** | Per incident |
| **Estimated Time** | 15-20 min |

## Steps

1. **Confirm no-show:** Brand reports influencer didn't arrive at scheduled time
2. **Contact influencer:** DM + WhatsApp — ask for reason
3. **Determine cause:**

| Cause | Action |
|-------|--------|
| Emergency/illness (genuine) | Reschedule, no penalty |
| Forgot | Warning (1st time) |
| Ghosted (no response) | Full no-show penalty |
| Cancelled < 24h | Warning |

4. **Apply penalty per guardrails:**
   - 1st no-show: Warning via DM, logged in CRM
   - 2nd no-show: 7-day ban from Job Board (`ban_status` = `Banned`, `ban_until` = today + 7)
   - 3rd no-show: Permanent tier demotion (Gold → Silver, etc.)
   - 4th+: Account suspension pending review
5. **Reschedule:** Find replacement influencer or reschedule with same
6. **Apologize to brand:** Offer free upgrade on next campaign
7. **Update `no_show_count`** in CRM

---

# SOP-503: Dispute Resolution

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-503 |
| **Executor** | Ops Manager |
| **Frequency** | Per incident |
| **Estimated Time** | 30-60 min |

## Steps

1. **Receive complaint:** Via Chatwoot/WhatsApp from either party
2. **Document:** Log the complaint with date, parties, issue description
3. **Investigate:**
   - Review conversation history
   - Review campaign brief
   - Review content submitted
   - Check CRM data
4. **Mediate:** Contact both parties, hear both sides
5. **Resolution options:**

| Dispute Type | Resolution |
|-------------|------------|
| Content quality disagreement | Re-review against brief. If meets brief, content stands |
| Payment dispute | Verify invoices and transfers. Adjust if error found |
| Influencer not credited | Verify points/payment, correct if missing |
| Brand made poor experience | Offer free redo with different influencer |
| Harassment/misconduct | Immediate ban + incident report |

6. **Communicate decision** to both parties within 72 hours
7. **VibMedia's decision is binding** for disputes under ₹25,000
8. **Above ₹25,000:** Refer to mediation/arbitration

---

# SOP-504: Fraud & Fake Engagement Detection

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-504 |
| **Executor** | AI Agent + Ops |
| **Frequency** | During enrichment (SOP-002) + spot checks |

## Red Flags (Automated Detection)

| Indicator | Threshold | Action |
|-----------|----------|--------|
| Following/Follower ratio | > 1.5 | Flag as suspicious |
| Engagement rate | < 0.5% with > 10K followers | Flag as likely bot |
| Comments are all generic | > 50% "nice", "great", emoji-only | Flag for manual review |
| Sudden follower spike | > 20% growth in 1 week | Check if purchased |
| Same comments repeated | Many identical comments | Bot farm |
| Location mismatch | Claims Delhi but posts from different city | Verify |

## Steps on Detection

1. Flag influencer as `Suspicious` in CRM
2. Ops manually reviews profile
3. If confirmed fake engagement → set `pipeline_status` = `Rejected — Fake`
4. If borderline → monitor for 2 weeks, re-check
5. If an active influencer later found cheating → deduct all points, ban

---

# SOP-505: DPDPA Data Request Processing

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-505 |
| **Executor** | Ops Team (human-in-the-loop) |
| **Frequency** | Per request |
| **Estimated Time** | 30 min per request |

> **Status:** Human-in-loop process. Automated flow to be implemented after final legal policy review.

## Steps

### Data Access Request ("What data do you have about me?")
1. Receive request via any channel
2. Pull their row from CRM
3. Export as PDF/text (mask UPI/bank details except last 4)
4. Send to requester within 48 hours

### Data Correction Request ("My info is wrong")
1. Verify requester's identity (match IG handle + phone)
2. Update corrected fields in CRM
3. Confirm to requester

### Data Deletion Request ("Delete everything")
1. Verify identity
2. Remove: phone, email, rates, UPI, bank details, conversation logs
3. Retain: handle + `Opted Out` status (suppression list)
4. Confirm deletion within 30 days
5. Log in compliance register

---

# 🚀 Growth & Expansion SOPs (600-699)

---

# SOP-601: New Area Expansion Playbook

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-601 |
| **Executor** | Ops Manager |
| **Frequency** | Per expansion decision |
| **Estimated Time** | 1-2 weeks setup |

## Steps

1. **Market research:** Identify restaurant density, food influencer count in new area
2. **Update categories:** Add new area + sub-localities to `data_schema.md` config
3. **Influencer sourcing:** Find 100+ food influencers in the new area (manual)
4. **Brand sourcing:** Find 50+ restaurants in the new area (manual)
5. **Template customization:** Update outreach templates with area-specific references
6. **Import & enrich:** Run SOP-001 + SOP-002 + SOP-101 for new batches
7. **Test outreach:** Send 10 test DMs, evaluate response rate
8. **If response rate ≥ 5%:** Full launch in new area
9. **If response rate < 5%:** Adjust messaging, try again, or deprioritize

---

# SOP-602: New Industry Vertical Launch

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-602 |
| **Executor** | Ops Manager |
| **Frequency** | Per expansion decision |
| **Estimated Time** | 2-4 weeks setup |

## Steps

1. **Define industry categories:** Add niche categories to `data_schema.md`
   - e.g., Fashion: Streetwear, Luxury, Casual, Sportswear
2. **Influencer criteria:** Define minimum metrics for new industry
   - e.g., Fashion: engagement > 3%, follower > 5K, aesthetic feed
3. **Brand criteria:** Define ideal brand profile
   - e.g., Fashion: D2C brands, boutiques, e-commerce
4. **Template creation:** Write industry-specific outreach templates (SOP-603)
5. **Content QC standards:** Update SOP-501 with industry-specific quality criteria
6. **Pilot:** 20 influencer imports + 10 brand imports
7. **Measure:** First 30 days — reply rate, onboarding rate, first deal
8. **Go/No-go decision:** Based on pilot metrics

---

# SOP-603: Template Creation & Approval

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-603 |
| **Executor** | Ops + Sales |
| **Frequency** | Per new template / update |

## Steps

1. **Draft template:** Write personalized copy following brand voice
2. **Variable check:** Ensure all `{variables}` map to existing CRM fields
3. **Tone review:** Natural, friendly, not salesy or spammy
4. **Compliance check:**
   - No false claims
   - No pressure tactics
   - Opt-out option available
   - ASCI compliant if promotional
5. **A/B test:** Send 20 messages each of version A and version B
6. **Measure:** Compare reply rates after 5 days
7. **Select winner:** Archive loser, make winner the active template
8. **Store in:** `templates/outreach_templates.md`
9. **Review schedule:** All templates reviewed quarterly
