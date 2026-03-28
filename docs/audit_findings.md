# 🔍 System Audit — Loopholes, Clashes & Missing Data

> Cross-reference audit of PRD, guardrails, system_architecture, ROADMAP, user_stories, Blueprint, resource_requirements, and outreach templates.

---

## 🔴 Critical Clashes (Must Fix Before Build)

### 1. Blueprint vs PRD: LLM Choice Conflict
| Document | LLM Choice |
|----------|------------|
| Blueprint (Section 4.2) | Claude 3.5 Sonnet (primary), Llama 3 70B (worker) |
| PRD (Section 12) | Google Gemini 2.0 Flash/Pro (primary), Llama 3 70B (backup) |
| System Architecture | Gemini Flash + Pro |

**Resolution:** PRD is authoritative → **Use Gemini Flash/Pro** (aligns with $300 GCP credits). Blueprint's Claude recommendation is aspirational and pre-dates the GCP credit commitment.

### 2. Blueprint vs PRD: Scraping Contradiction
| Document | Stance |
|----------|--------|
| Blueprint (Phase 1 table) | "Seed 500 Delhi NCR food influencers from IG manual research + **basic scraping**" |
| PRD (Assumption #1) | "All raw data is **provided manually** — no automated scraping" |
| Guardrails (Don't #2) | "**Don't scrape anything**" |
| data/brands/README.md | Lists "**Google My Business scrape**" and "**Zomato scrape**" as expected sources |

**Resolution:** PRD + guardrails are authoritative → **NO scraping. All data is manually sourced.** Update `data/brands/README.md` to remove "scrape" language.

### 3. Blueprint vs PRD: CRM Tool Conflict
| Document | CRM |
|----------|-----|
| Blueprint (Section 4.2) | Odoo (self-hosted) |
| PRD (Phase 1) | Google Sheets |
| Guardrails (Rule #2) | "Use Google Sheets as CRM first" |

**Resolution:** PRD is authoritative → **Google Sheets for Phase 1.** Odoo can be evaluated in Phase 2+ but is not currently planned.

### 4. Blueprint vs PRD: Email Tool Conflict
| Document | Email |
|----------|-------|
| Blueprint (Section 4.2) | Postal (self-hosted SMTP) for bulk outreach |
| PRD (Section 9) | Socionator manual cold-email fallback only |
| Guardrails | No mention of Postal or email automation |

**Resolution:** PRD is authoritative → **No email automation. Socionator is manual-only for cold-email when IG DMs fail.** Postal is not needed in Phase 1.

---

## 🟡 Loopholes (Gaps in the System)

### 5. Missing: WhatsApp Ban Recovery Plan
**Issue:** The hybrid WhatsApp model now sends AI-automated messages via Evolution API, but there is NO documented ban recovery plan for WhatsApp numbers.
- PRD mentions Evolution API number warmup for 4-5 numbers
- Guardrails mentions "rate limit 5-10/day, warm up numbers"
- **Missing:** What happens when a WhatsApp number gets banned? No fallback, no rotation plan.

**Needed:** Add WhatsApp number rotation + ban recovery to guardrails (similar to IG account management).

### 6. Missing: Influencer Opt-out / DPDPA Compliance Flow
**Issue:** DPDPA 2023 requires explicit consent and data deletion rights.
- resource_requirement_guide (Domain 12) mentions legal compliance research is "Incomplete"
- PRD mentions no consent mechanism for DM outreach
- **Missing:** How does an influencer opt out? Where is data deletion tracked?

**Needed:** Add opt-out keyword detection to reply classifier ("stop", "remove me", "unsubscribe"). Add data deletion logging.

### 7. Missing: Job Board — How Do Influencers ACCESS It?
**Issue:** The Job Board is described as "Google Sheets or simple web form" in Phase 1, but:
- User stories (US-6.2) say "influencers browse available jobs filtered by profile"
- Phase 1 guardrails say "Don't build a dashboard"
- **Gap:** How does an influencer SEE and APPLY to jobs? Google Sheets are internal. Influencers don't have access.

**✅ RESOLVED:** Option A — **AI sends matching jobs via IG DM.** Influencers reply "interested" to apply. No web page needed in Phase 1.

### 8. Missing: Points Decay / Anti-Gaming Rules
**Issue:** Points system is well-defined for earning but:
- No mention of points expiry or decay
- No anti-gaming rules (e.g., friend creates fake brand, posts fake job, friend applies and "completes")
- No penalty for no-shows after accepting a job

**✅ RESOLVED:**
- **Points decay:** Points expire after **12 months** of inactivity
- **No-show policy:** Warning on first offense → 7-day ban on repeat → permanent demotion on 3rd

---

## 🟢 Minor Inconsistencies (Non-blocking)

### 9. Template Variables Mismatch
| Template File | Variable Used | Data Source |
|---------------|--------------|-------------|
| WhatsApp Day 1 | `{specific_dish_from_zomato}` | Zomato data |
| brands/README.md | `popular_dishes` column | Manual import |
| PRD enrichment | "Popular dishes field populated" | Provided source files |

**Issue:** Template references "from Zomato" but PRD says no scraping. Should say "from provided data."
**Fix:** Update template variable name to `{popular_dish}` (no Zomato reference).

### 10. Influencer Pipeline Stage Count Mismatch
| Document | Stages | Stage Names |
|----------|--------|-------------|
| PRD Section 7.2 | 11 stages | Imported → DM Sent → Follow-up → Replied → Onboarding Chat → Onboarded → Available → On Campaign → Completed → Idle → Cold |
| system_architecture CRM diagram | 11 stages | Same as PRD ✅ |
| Blueprint Phase 1 | Not defined | N/A |

**Status:** ✅ Consistent across PRD and architecture.

### 11. Cost Projections Differ
| Document | Phase 1 Monthly Cost |
|----------|---------------------|
| Blueprint (Section 4.3) | ₹0 |
| PRD (Section 13) | ~$135/mo (~₹11,000/mo) |

**Note:** Blueprint says ₹0 for Phase 1 (free tier), PRD says ~$135/mo (GCP credits). Both are technically correct — ₹0 out-of-pocket since GCP credits cover the $135.

---

## ✅ Things That Are Consistent (Good)

- Points system: 10/25/50/100 pts → consistent across PRD, user_stories, architecture
- Tier definitions: Nano/Micro/Mid/Macro → consistent everywhere
- Reputation tiers: Bronze/Silver/Gold/Platinum → consistent with thresholds
- Proxy ratio: 1:2 everywhere
- DM ramp schedule: 5→6→8 everywhere
- Commission: 20% everywhere
- Job types: Quick Review / Standard Collab / Full Campaign / Long-term → consistent
- Infrastructure: 2 GCP VMs → consistent across PRD + architecture

---

## Action Items

| # | Action | Priority | Status |
|---|--------|----------|--------|
| 1 | Standardize LLM to Gemini Flash/Pro across all docs | 🔴 High | ✅ Done |
| 2 | Remove "scrape" language from data/brands/README.md | 🔴 High | ✅ Done |
| 3 | Job Board = AI sends via IG DM, influencers reply "interested" | 🔴 High | ✅ Resolved |
| 4 | WhatsApp ban → shift to next number or pause outreach | 🟡 Medium | ✅ Resolved |
| 5 | DPDPA opt-out → human-in-the-loop for now, final policy later | 🟡 Medium | ✅ Resolved |
| 6 | Points expire after 12 months. No-show: warning → ban → demotion | 🟡 Medium | ✅ Resolved |
| 7 | Update template to remove Zomato reference | 🟢 Low | ✅ Done |
| 8 | No Odoo. Google Sheets Phase 1, PostgreSQL Phase 2 | 🟢 Low | ✅ Resolved |
